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

데이터 확인 방법   
</br>
config 건수 확인
```sql
SELECT sum(count)
from (
SELECT
 "json_size"("configurationItem"."configuration", '$.configrulelist') "count"
FROM default.aws_config_configuration_snapshot
CROSS JOIN UNNEST("configurationitems") t (configurationItem)
WHERE (("configurationItem"."resourcetype" = 'AWS::Config::ResourceCompliance') AND ("json_extract_scalar"("configurationItem"."configuration", '$.configrulelist[0].configrulename') IS NOT NULL)) AND ("dt" = 'latest'))
```
   
</br>
</br>

생성된 view 테이블 건수 확인   
```sql
SELECT count(*) FROM "default"."v_config_rules_resource_compliances"
```

동일하게 412건 확인가능
</br>
<img width="934" alt="image" src="https://user-images.githubusercontent.com/42329161/156950175-9e1c0525-aa04-406d-8f5e-bb66fb4d935e.png">
</br>
</br>
<img width="929" alt="image" src="https://user-images.githubusercontent.com/42329161/156950184-30398b77-e435-4f0d-92b0-0d29c9c13517.png">




***

참고 Athena 한달전 날짜 확인 SQL
```sql
select cast(date(current_timestamp) - interval '1' day as varchar)
```

***
일자별 조회 방법
한달 전 기준으로 view 생성   
기존 view는 dt = latest 조건으로 생성되어 있음
```sql
CREATE OR REPLACE VIEW v_config_rules_resource_compliances_one_month AS 
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
WHERE ((("configurationItem"."resourcetype" = 'AWS::Config::ResourceCompliance') AND (n.configrulename IS NOT NULL)) AND ("dt" > (select cast(date(current_timestamp) - interval '1' month as varchar)))) 
```

Quicksight에서 확인하고 싶은 데이터에 따라 dt의 조건을 일정 기간을 지정하던가 아니면 지정을 하지 않던가 해서 view를 생성하면 일자별 현황을 확인 가능합니다. 

예) 일자별 Compliant / NON_Compliant 건수 확인

<img width="800" alt="image" src="https://user-images.githubusercontent.com/42329161/156949700-1978e639-5f80-4db9-9fd3-e92d27093ddb.png">

예) 계정별 NON_Compliant 건수 확인

<img width="787" alt="image" src="https://user-images.githubusercontent.com/42329161/156949752-1c0a1718-de41-4f98-a365-3d962b22b70f.png">
