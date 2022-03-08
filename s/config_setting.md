## 1.CT 관리 계정에서 적용하고자 하는 계정으로 AWSControlTowerExecution 로 assume role 하여 진행

Switch role 클릭   
![image](https://user-images.githubusercontent.com/42329161/157204435-a66558f5-b5b5-4053-bd65-3856fd174808.png)

Account ID 입력 : 529869257540   
Role 입력 : AWSControlTowerExecution   
Display : 임의로 입력   
![image](https://user-images.githubusercontent.com/42329161/157204755-c514ac1b-cf46-4bf3-b22a-122bda38a8ee.png)


## 2. Config 세팅

해당 계정의 Config가 활성화된 리전에서 아래 정보 확인
Config - setting - edit 버튼 클릭

<img width="1008" alt="image" src="https://user-images.githubusercontent.com/42329161/157204916-ef1f69db-2c3f-4680-b862-bfa0a98c102d.png">

해당 계정의 us-west-1 config 컨솔에서 Get started 버튼 클릭
아래 값을 위에서 확인한 값과 동일하게 세팅
SNS 부분은 Audit계정에서 Us-west-1에 생성이 되어야 하는 부분이라 일단 패스하고 S3 bucket만 세팅
![image](https://user-images.githubusercontent.com/42329161/157205355-fdc0244b-b7bc-4ac6-801e-d6acc6fc3acd.png)

이렇게 구성후 테스트 시에 정상 배포 확인
