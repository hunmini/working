[1] Visualizing AWS Config data using Amazon Athena and Amazon QuickSight   
  : https://aws.amazon.com/blogs/mt/visualizing-aws-config-data-using-amazon-athena-and-amazon-quicksight/   


[2] How to query your AWS resource configuration states using AWS Config and Amazon Athena 
  : https://aws.amazon.com/blogs/mt/how-to-query-your-aws-resource-configuration-states-using-aws-config-and-amazon-athena/   
    

***


[2]링크 참고하여 작업 진행

**Table 생성**
```
CREATE EXTERNAL TABLE aws_config_configuration_snapshot (
    fileversion STRING,
    configSnapshotId STRING,
    configurationitems ARRAY < STRUCT <
        configurationItemVersion : STRING,
        configurationItemCaptureTime : STRING,
        configurationStateId : BIGINT,
        awsAccountId : STRING,
        configurationItemStatus : STRING,
        resourceType : STRING,
        resourceId : STRING,
        resourceName : STRING,
        ARN : STRING,
        awsRegion : STRING,
        availabilityZone : STRING,
        configurationStateMd5Hash : STRING,
        configuration : STRING,
        supplementaryConfiguration : MAP < STRING, STRING >,
        tags: MAP < STRING, STRING >,
        resourceCreationTime : STRING
    > >
) PARTITIONED BY (accountid STRING, dt STRING, region STRING) 
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://<S3_BUCKET_NAME>/AWSLogs/';
-- 예 's3://aws-controltower-logs-037777328400-ap-northeast-2/o-cjbxfvebcu/AWSLogs/'
```

람다 함수 생성
[2]링크에 나오는 람다함수에서 get_configuration_snapshot_object_key_match() 변경필요
Database명 변경 필요

*기존*

```
DATABASE_NAME = 'sampledb'
.....

def get_configuration_snapshot_object_key_match(object_key):
    # Matches object keys like AWSLogs/123456789012/Config/us-east-1/2018/4/11/ConfigSnapshot/123456789012_Config_us-east-1_ConfigSnapshot_20180411T054711Z_a970aeff-cb3d-4c4e-806b-88fa14702hdb.json.gz
    return re.match('^AWSLogs/(\d+)/Config/([\w-]+)/(\d+)/(\d+)/(\d+)/ConfigSnapshot/[^\\\]+$', object_key)
```

*변경(bucket 경로 확인 후 AWSLogs앞에 추가)*
```
DATABASE_NAME = 'default' #사용하는 DB명으로 변경
def get_configuration_snapshot_object_key_match(object_key):
    # Matches object keys like AWSLogs/123456789012/Config/us-east-1/2018/4/11/ConfigSnapshot/123456789012_Config_us-east-1_ConfigSnapshot_20180411T054711Z_a970aeff-cb3d-4c4e-806b-88fa14702hdb.json.gz
    return re.match('^o-cjbxfvebcu/AWSLogs/(\d+)/Config/([\w-]+)/(\d+)/(\d+)/(\d+)/ConfigSnapshot/[^\\\]+$', object_key)
```
S3 bucket에서 config log를 put하면 해당 람다가 동작함
람다 수행 후 partition 정보 확인 해보면 생성된 걸 확인 가능
<img width="693" alt="image" src="https://user-images.githubusercontent.com/42329161/155549273-b9ae1796-08f0-4fb0-9e90-48ad92f09bf0.png">


***


[2]번 작업을 통해 athena에 테이블이 생성되면 [1]번 링크의 작업들 수행

**Step4에 있는 View 테이블 생성**

<img width="348" alt="image" src="https://user-images.githubusercontent.com/42329161/155569948-023ada6a-b5a5-4b9c-b20b-25b7300e545a.png">



**Step6에 aws_org_accounts table 생성 (blog에 나와있는 컬럼을 다 만들지는 않았음, 필요한 내용만 넣거나 빼면 됨)**
```
CREATE EXTERNAL TABLE `aws_org_accounts`(

`accountid` string,

`businessunit` string,

`name` string)

ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'

STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'

OUTPUTFORMAT

'org.apache.hadoop.hive.ql.io.IgnoreKeyTextOutputFormat'

LOCATION

's3://hunmin-test11/'
```


**account table 조회**
<img width="941" alt="image" src="https://user-images.githubusercontent.com/42329161/155562084-3153cb21-ed5e-4465-be48-4e7faa3ecba2.png">


v_aws_org_accounts view를 생성하고 기존에 생성한 view와 account view와 조인하여 새로운 view를 생성
[1]의 Join the AWS Organizations data on the AWS Config views in Amazon Athena 항목

**생성된 테이블 조회**
<img width="937" alt="image" src="https://user-images.githubusercontent.com/42329161/155570769-47fed6c0-3016-4aae-846e-bc483b0471aa.png">

[1]번에 있는 Step9의 QuickSight 세팅을 따라서 세팅

**Dashboard 구성 예시**
 account 기준, OU 기준등으로 
![image](https://user-images.githubusercontent.com/42329161/155571826-a03b6f5a-5786-438f-abac-f4252324ee82.png)



