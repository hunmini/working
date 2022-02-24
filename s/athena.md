Visualizing AWS Config data using Amazon Athena and Amazon QuickSight   
  : https://aws.amazon.com/blogs/mt/visualizing-aws-config-data-using-amazon-athena-and-amazon-quicksight/   


How to query your AWS resource configuration states using AWS Config and Amazon Athena   
  : https://aws.amazon.com/blogs/mt/how-to-query-your-aws-resource-configuration-states-using-aws-config-and-amazon-athena/   
    
Table 생성
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
```
