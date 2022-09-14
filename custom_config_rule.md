
#### Cloudformation 배포 yaml 작성

<details>
<summary>yaml 확인</summary>
<div markdown="1">

  ```yaml
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  ConfigPermissionToCallLambda: 
    Type: AWS::Lambda::Permission
    Properties: 
      FunctionName: 
         Fn::GetAtt: 
          - LambdaRequireTagsWithValidValues
          - Arn
      Action: "lambda:InvokeFunction"
      Principal: "config.amazonaws.com"
  LambdaRequireTagsWithValidValues: 
    Type: AWS::Lambda::Function
    Properties: 
      Code: 
        ZipFile: |
          import json
          import boto3


          # Specify desired resource types to validate
          APPLICABLE_RESOURCES = ["AWS::Lambda::Function"]


          # Iterate through required tags ensureing each required tag is present, 
          # and value is one of the given valid values
          def find_violation(current_tags, required_tags):
              violation = ""
              for rtag,rvalues in required_tags.items():
                  tag_present = False
                  for tag in current_tags:
                      if tag == rtag:
                          tag_present = True
                          value_match = False
                          rvaluesplit = rvalues.split(",")
                          for rvalue in rvaluesplit:
                              if current_tags[tag] == rvalue:
                                  value_match = True
                              if current_tags[tag] != "":
                                  if rvalue == "*":
                                      value_match = True
                          if value_match == False:
                              violation = violation + "\n" + current_tags[tag] + " doesn't match any of " + required_tags[rtag] + "!"
                  if not tag_present:
                      violation = violation + "\n" + "Tag " + str(rtag) + " is not present."
              if violation == "":
                  return None
              return  violation


          def evaluate_compliance(configuration_item, rule_parameters):

              if configuration_item["resourceType"] not in APPLICABLE_RESOURCES:
                  return {
                      "compliance_type": "NOT_APPLICABLE",
                      "annotation": "The rule doesn't apply to resources of type " +
                      configuration_item["resourceType"] + "."
                  }

              if configuration_item["configurationItemStatus"] == "ResourceDeleted":
                  return {
                      "compliance_type": "NOT_APPLICABLE",
                      "annotation": "The configurationItem was deleted and therefore cannot be validated."
                  }


              if configuration_item["resourceType"] == "AWS::Lambda::Function":
                  client = boto3.client('lambda')
                  all_tags = client.list_tags(Resource=configuration_item["ARN"])
                  current_tags = all_tags['Tags']  # get only user  tags.  


              violation = find_violation(current_tags, rule_parameters)        

              if violation:
                  return {
                      "compliance_type": "NON_COMPLIANT",
                      "annotation": violation
                  }

              return {
                  "compliance_type": "COMPLIANT",
                  "annotation": "This resource is compliant with the rule."
              }

          def lambda_handler(event, context):
              invoking_event = json.loads(event["invokingEvent"])

              configuration_item = invoking_event["configurationItem"]

              rule_parameters = json.loads(event["ruleParameters"])

              result_token = "No token found."
              if "resultToken" in event:
                  result_token = event["resultToken"]

              evaluation = evaluate_compliance(configuration_item, rule_parameters)

              config = boto3.client("config")
              config.put_evaluations(
                  Evaluations=[
                      {
                          "ComplianceResourceType":
                              configuration_item["resourceType"],
                          "ComplianceResourceId":
                              configuration_item["resourceId"],
                          "ComplianceType":
                              evaluation["compliance_type"],
                          "Annotation":
                              evaluation["annotation"],
                          "OrderingTimestamp":
                              configuration_item["configurationItemCaptureTime"]
                      },
                  ],
                  ResultToken=result_token
            )
      Handler: "index.lambda_handler"
      Runtime: python3.8
      Timeout: 180
      Role: 
        Fn::GetAtt: 
          - LambdaExecutionRole
          - Arn
  ConfigRuleForLambdaRequireTagsWithValidValues: 
    Type: AWS::Config::ConfigRule
    Properties: 
      ConfigRuleName: ConfigRuleForLambdaRequireTagsWithValidValues
      InputParameters:
        Dept: 'Dev,ProjecvtA'
      Scope: 
        ComplianceResourceTypes: 
          - "AWS::Lambda::Function"
      Source: 
        Owner: "CUSTOM_LAMBDA"
        SourceDetails: 
          - 
            EventSource: "aws.config"
            MessageType: "ConfigurationItemChangeNotification"
        SourceIdentifier: 
          Fn::GetAtt: 
            - LambdaRequireTagsWithValidValues
            - Arn
    DependsOn: ConfigPermissionToCallLambda
  LambdaExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - lambda.amazonaws.com
          Action:
          - sts:AssumeRole
      Path: "/"
      Policies:
      - PolicyName: lambdaForConfigExecutionPolicy
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - lambda:List*
            - lambda:Get*
            - config:Put*
            - config:Get*
            - config:List*
            - config:Describe*
            - logs:CreateLogStream
            - logs:PutLogEvents
            - logs:CreateLogGroup
            Resource: "*"  
```

</div>
</details>


#### 해당 yaml을 codecommit 특정 경로에 upload
(저같은 경우에는 templates/customconfigrule.yaml 경로에 업로드 함)

#### manifest.yaml에 아래 내용 추가
```
  - name: custom-config
    resource_file: templates/customconfigrule.yaml
    deploy_method: stack_set
    deployment_targets: # :type: list
      organizational_units:
        - Security
        - DEVENV
    regions:
      - ap-northeast-2   
```  
   
#### Cfct 정상 배포 확인   
![image](https://user-images.githubusercontent.com/42329161/190043627-b62054c7-daf7-4556-87cb-29918abd2f01.png)

   
#### Config 내에서 해당 Rule이 해당 OU 하위 account에 적용된 내용 확인    
![image](https://user-images.githubusercontent.com/42329161/190043159-31f9978f-098c-4117-88b9-f735ddec4cda.png)

#### 다른 Account 들도 확인 가능
![image](https://user-images.githubusercontent.com/42329161/190043279-60f4aded-6550-4db8-91cd-7355c060ec2a.png)
![image](https://user-images.githubusercontent.com/42329161/190043395-56a761a7-bcb9-49cb-8933-ca8d21928f84.png)


#### Lambda에 태그 추가 후 Compliance 상태가 변경되는지 확인
(조건을 Key : Dept  Value : Dev,ProjectA 일 경우 Compliant로 정의하였음)
![image](https://user-images.githubusercontent.com/42329161/190044899-61e14f9e-510b-4ed4-835e-a7ce4c6061b0.png)








#### 참고 URL   
awslabs - config-rule : https://github.com/awslabs/aws-config-rules/blob/master/python/lambda_require_tags_with_valid_values.py
CloudFormation - Config rule : https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/aws-resource-config-configrule.html

