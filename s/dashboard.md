[1] Visualizing AWS Config data using Amazon Athena and Amazon QuickSight   
  : https://aws.amazon.com/blogs/mt/visualizing-aws-config-data-using-amazon-athena-and-amazon-quicksight/   

[1] 링크의 `Advanced queries against AWS Config rules`의 `Example 6: Create a view of each AWS Config rule and resource compliance evaluation` Create View SQL 구문 참고하여 생성      
[1] 링크의 `Join the AWS Organizations data on the AWS Config views in Amazon Athena` 의 Create View SQL을 참고하여 v_config_rules_resource_compliances와 Account정보를 조인한 View생성

예시
```sql
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


위에서 생성한 v_config_rules_resource_compliances_aws_org_accounts Veiw를 가지고 Dataset 생성




# 그래프 생성 방법
![image](https://user-images.githubusercontent.com/42329161/155921838-b7471d6a-5f8b-4ecc-a0bf-df9ee1f419c3.png)


## 1번항목

Fields list - Compliance Type 선택    
Visual Type - Donut Chart 선택

![image](https://user-images.githubusercontent.com/42329161/155927576-78f659c9-9354-4aa7-9dc3-fbb7deada2b4.png)

## 2번항목
Fields list - Resource Type 선택    
Visual Type - Donut Chart 선택   
Filter - Non_compliant 선택   
![image](https://user-images.githubusercontent.com/42329161/155928503-5a548f72-14b4-4164-a573-86f81330a7bb.png)
![image](https://user-images.githubusercontent.com/42329161/155928461-91f559f4-55df-4b84-a470-231b7a856ce2.png)

## 3번항목
1) Fields list - Compliance Type 선택     
2) Visual Type - Pivot Table 선택      
3) 그래프 우측 Swap row and colum 선택 ![image](https://user-images.githubusercontent.com/42329161/155938874-93b7ccc4-33b4-4ea3-abbd-89bc1a8c1d2e.png)   
4) Fields list - Business Unit 선택 (Blog 예시는 솔루션이나 제가 Account Table에 Soultion 항목을 만들지 않아서 Business Unit으로 선택해서 만들었습니다.      
5) 그래프 우측 Format visaul 선택 ![image](https://user-images.githubusercontent.com/42329161/155939235-a3506e9e-9afb-4ccc-b877-c61893beae3a.png), total값 추가   

      
![image](https://user-images.githubusercontent.com/42329161/155939283-1175b081-80d1-4913-84cc-d495aa422b2f.png)
   
![image](https://user-images.githubusercontent.com/42329161/155939329-3252b4b1-f848-4c75-b75b-5f446b8b5ce1.png)

## 4번 항목
1) Fields list - Compliance Type 선택     
2) Visual Type - Pivot Table 선택      
3) 그래프 우측 Swap row and colum 선택
4) Fields list - Configurulename 선택
5) 그래프 우측 Format visaul 선택, total값 추가   

![image](https://user-images.githubusercontent.com/42329161/155940558-75d6f884-3e9e-4022-a7ba-8bc732986ea6.png)


## 5번항목
1) Fields list - Configurulename 선택     
2) Visual Type - Auto Grape 그냥 사용   
3) Filter - Non_compliant 선택   

![image](https://user-images.githubusercontent.com/42329161/155940812-6eb2cb69-d4b9-48f6-8035-06f7cc93c655.png)


## 6번 항목
1) Fields list - account id 선택   
2) Visual Type - table 선택
3) Fields list 추가 하고자 하는 항목 선택
4) Fielter 추가 - Non-compliant만 선택 
![image](https://user-images.githubusercontent.com/42329161/155941325-8bb05983-9a68-4409-82ff-d67b1d9ab3bf.png)   
   
   
## 기타
각 항목의 Tilte은 맞게 변경필요

# 완성 화면

![image](https://user-images.githubusercontent.com/42329161/155941967-f515414a-4640-4fbb-92c2-8dd0b2107d70.png)
