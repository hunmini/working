[1] Visualizing AWS Config data using Amazon Athena and Amazon QuickSight   
  : https://aws.amazon.com/blogs/mt/visualizing-aws-config-data-using-amazon-athena-and-amazon-quicksight/   

[1] 링크의 `Advanced queries against AWS Config rules`의 `Example 6: Create a view of each AWS Config rule and resource compliance evaluation` Create View SQL 구문 참고하여 생성   
[1] 링크의 `Join the AWS Organizations data on the AWS Config views in Amazon Athena` 의 Create View SQL을 참고하여 v_config_rules_resource_compliances와 Account정보를 조인한 View생성

예시
```
CREATE OR REPLACE VIEW v_config_rules_resource_compliances_aws_org_accounts AS

WITH

a AS (

SELECT * FROM default.v_config_rules_resource_compliances

)

, b AS (

SELECT

"accountid"

, "name"

, "businessunit"

FROM default.v_aws_org_accounts

)

SELECT

"b"."name" "AccountName"

, "b"."businessunit" "BusinessUnit"

, a.*

FROM

(a

LEFT JOIN b ON ("a"."accountid" = "b"."accountid"))
```
