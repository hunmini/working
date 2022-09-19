## data-set 조회 - data-set-id 확인
``` 
aws quicksight list-data-sets --aws-account-id 123456789012
```

## data-set-id permission 조회
```
aws quicksight describe-data-set-permissions --aws-account-id 123456789012 --data-set-id 95e7f728-1234-5678-8e2d-85eecad7702b
```

조회 예시

![image](https://user-images.githubusercontent.com/42329161/190939742-f6444545-0e83-4535-9d94-ccf4f9fb4858.png)

## permission 변경
```
aws quicksight update-data-set-permissions --aws-account-id 123456789012 --data-set-id 95e7f728-1234-5678-8e2d-85eecad7702b --grant-permissions Actions="quicksight:DescribeDataSet","quicksight:DescribeDataSetPermissions","quicksight:PassDataSet","quicksight:DescribeIngestion","quicksight:ListIngestions","quicksight:UpdateDataSet","quicksight:DeleteDataSet","quicksight:CreateIngestion","quicksight:CancelIngestion","quicksight:UpdateDataSetPermissions",Principal=arn:aws:quicksight:ap-northeast-2:123456789012:user/default/Admin/hunmin-xxxxxx
```

### 확인
![image](https://user-images.githubusercontent.com/42329161/190939917-342ca02a-836c-450a-a812-27e84e4229ee.png)

Quicksight에서도 Owner로 변경된 내역 확인가능
