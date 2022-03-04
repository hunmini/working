[1] Visualizing AWS Config data using Amazon Athena and Amazon QuickSight   
  : https://aws.amazon.com/blogs/mt/visualizing-aws-config-data-using-amazon-athena-and-amazon-quicksight/   

[1] 링크의 `Advanced queries against AWS Config rules`의 `Example 6: Create a view of each AWS Config rule and resource compliance evaluation` Create View SQL 구문 변경필요


```sql
CREATE OR REPLACE VIEW v_config_rules_resource_compliances AS 
SELECT
  "configurationItem"."awsAccountId" "AccountId"
, "configurationItem"."awsregion" "Region"
, "split_part"("configurationItem"."resourceid", '/', 1) "ResourceType"
, "split_part"("configurationItem"."resourceid", '/', 2) "ResourceId"
, "n"."configrulename" "ConfigRuleName"
, "n"."compliancetype" "ComplianceType"
FROM
  (default.aws_config_configuration_snapshot
CROSS JOIN UNNEST("configurationitems") t (configurationItem)),
UNNEST(CAST("json_extract"("configurationItem"."configuration", '$.configrulelist') AS array(row(configruleid varchar,configrulename varchar,configrulearn varchar,compliancetype varchar)))) x (n)
WHERE ((("configurationItem"."resourcetype" = 'AWS::Config::ResourceCompliance') AND (n.configrulename IS NOT NULL)) AND ("dt" = 'latest'))
```


      
            
            

***
작성중





한달 전 기준으로 view 생성
```sql
CREATE OR REPLACE VIEW v_config_rules_resource_compliances_before_one_month AS 
SELECT
  "configurationItem"."awsAccountId" "AccountId"
, "configurationItem"."awsregion" "Region"
, "split_part"("configurationItem"."resourceid", '/', 1) "ResourceType"
, "split_part"("configurationItem"."resourceid", '/', 2) "ResourceId"
, "n"."configrulename" "ConfigRuleName"
, "n"."compliancetype" "ComplianceType"
FROM
  (default.aws_config_configuration_snapshot
CROSS JOIN UNNEST("configurationitems") t (configurationItem)),
UNNEST(CAST("json_extract"("configurationItem"."configuration", '$.configrulelist') AS array(row(configruleid varchar,configrulename varchar,configrulearn varchar,compliancetype varchar)))) x (n)
WHERE ((("configurationItem"."resourcetype" = 'AWS::Config::ResourceCompliance') AND (n.configrulename IS NOT NULL)) AND ("dt" = (select cast(date(current_timestamp) - interval '1' month as varchar))))
```
