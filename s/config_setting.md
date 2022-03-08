## 1.CT 관리 계정에서 적용하고자 하는 계정으로 AWSControlTowerExecution 로 assume role 하여 진행

Switch role 클릭   
![image](https://user-images.githubusercontent.com/42329161/157204435-a66558f5-b5b5-4053-bd65-3856fd174808.png)

Account ID 입력 : 529869257540   
Role 입력 : AWSControlTowerExecution   
Display : 임의로 입력   
![image](https://user-images.githubusercontent.com/42329161/157204755-c514ac1b-cf46-4bf3-b22a-122bda38a8ee.png)
</br></br>
***
## 2. SNS 세팅
CT 관리 계정에서 ```AWSControlTowerBP-SECURITY-TOPICS``` 선택
![image](https://user-images.githubusercontent.com/42329161/157243724-f4affe69-35e3-451b-8984-622ec74b10f9.png)

</br></br>
Action -> Add to stacks to stackset 선택
![image](https://user-images.githubusercontent.com/42329161/157245306-91b7c3ad-dca2-4981-8666-1eecaff2bda2.png)

</br></br>
Account -> Audit account 입력
Region -> us-west-1 선택
나머진 기본값으로 next/ Review 창에서 summit 선택
![image](https://user-images.githubusercontent.com/42329161/157245625-cc967535-7cef-4c49-948a-0add1378a5e9.png)

</br></br>
Operation Tab에서 작업중인 내역 확인가능 - Status가 Succeeded로 변경되면  Stack instances Tab에서 us-west-1에 배포된 걸 확인 가능
![image](https://user-images.githubusercontent.com/42329161/157245917-5128bc9d-d0e1-4c01-9887-128f8ca3bf75.png)
<br>
![image](https://user-images.githubusercontent.com/42329161/157246096-d99c3791-3ba1-4bb1-96bb-4ff4e531e71f.png)
</br></br>
***
## 3. Config 세팅
해당 계정의 Config가 활성화된 리전에서 아래 정보 확인</br>
Config - setting - edit 버튼 클릭
<img width="1008" alt="image" src="https://user-images.githubusercontent.com/42329161/157204916-ef1f69db-2c3f-4680-b862-bfa0a98c102d.png">
</br></br>
해당 계정의 us-west-1 config 컨솔에서 Get started 버튼 클릭</br>
Delivery method를 위에서 확인한 값과 동일하게 세팅하고 SNS값은 us-west-1에 배포된 topic의 ARN 값으로 대체 (region 값만 us-west-1으로 바꿔도 됨)
<img width="1079" alt="image" src="https://user-images.githubusercontent.com/42329161/157247224-18bf895f-f8f8-484d-9717-43f5db84f41d.png">

![image](https://user-images.githubusercontent.com/42329161/157246857-cd5873f1-4ca0-46b1-8a63-3ea4751e4f1a.png)
</br></br>

이렇게 구성후 테스트 시에 정상 배포 확인

