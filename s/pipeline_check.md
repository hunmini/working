Test.yaml
```yaml
---
# Home region for CodePipeline, StepFunctions, Lambda, SSM, StackSets
region: us-east-1  # Control Tower Home Region
version: 2021-03-15

resources:
  # ControlTower Custom SCPs - Additional Preventive Guardrails
  - name: test-preventive-guardrails
    description: Prevent deleting or disabling resources in member accounts
    resource_file: s3://marketplace-sa-resources-ct-ap-northeast-2/ctlabs/preventive-guardrails.json
    deploy_method: scp
    # Apply to the following OU(s)
    deployment_targets:
      organizational_units:
        # New Essential OU names since ControlTower v2.7 April 16.
        # Observe the names of your existing OUs and comment out or uncomment below to match yours.
        - DEVENV # Foremerly Core

  # ControlTower Custom CFN Resources - Create Additional IAM Role
  - name: create-iam-role
    resource_file: s3://marketplace-sa-resources-ct-ap-northeast-2/ctlabs/describe-regions-iam-role.template
    deploy_method: stack_set
    deployment_targets:
      organizational_units:
        # New Essential OU names since ControlTower v2.7 April 16.
        # Observe the names of your existing OUs and comment out or uncomment these to match yours.
        - DEVENV # Foremerly Core
        #- Core
        #- Custom
    regions:
      - ap-northeast-2
```

***

# Pipeline clikck   

![image](https://user-images.githubusercontent.com/42329161/157059018-2e9d8f1d-909d-4ea7-8d12-bf0b5ca90d15.png)

# Details click   

![image](https://user-images.githubusercontent.com/42329161/157059218-45a7cc25-e5a2-44d8-8f3c-8a27f3aa875a.png)

# 해당 로그 확인 (view entire log 클릭 후 Cloud watch log groups에서 확인 가능)   

![image](https://user-images.githubusercontent.com/42329161/157059317-ba513843-0235-49a0-9325-7b396f761c77.png)

# Stack 으로 검색 - create 내역이 있는지 확인   

![image](https://user-images.githubusercontent.com/42329161/157060265-aa73d6f0-bc51-4f9e-83f5-3acee40e37b8.png)
