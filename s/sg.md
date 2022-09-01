### A SG 생성

My ip에서 SSH로 접근 허용

![image](https://user-images.githubusercontent.com/42329161/187827194-32301772-2961-4e64-95a3-86ed8fd9dc1b.png)

### B SG 생성

A SG에서 SSH 접근 허용

![image](https://user-images.githubusercontent.com/42329161/187827333-d333a329-3833-4f66-a80b-45d8a58be554.png)

### A 인스턴스 생성
A SG 추가

### B 인스턴스 생성
B SG 추가

### 로컬(my ip)에서 A 인스턴스 SSH 접근 -성공
![image](https://user-images.githubusercontent.com/42329161/187827665-47b73c80-69aa-4412-ad14-8193e32990e6.png)

### 로컬(my ip)에서 B 인스턴스 SSH 접근 - 실패
![image](https://user-images.githubusercontent.com/42329161/187827887-dfeceb4a-fefd-48ce-ac5d-ca86d94e0b29.png)

### A 인스턴스 -> B 인스턴스로 접근 - 성공
<img width="643" alt="image" src="https://user-images.githubusercontent.com/42329161/187828670-8c15aa18-2268-4fa4-a4c2-3fb7abeb354f.png">
B인스턴스의 SG에 A SG를 Source로 넣었기 떄문에 A SG를 사용하는 A 인스턴스에서 접근 성공
