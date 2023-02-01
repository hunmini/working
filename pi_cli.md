db.sql_tokenized 기준 전체 조회   
  
```
[
   {
      "Metric":"db.load.avg",
      "GroupBy":{
         "Group":"db.sql_tokenized",
         "Limit":5
      }
   }
]

```
   
결과 예시   
![image](https://user-images.githubusercontent.com/42329161/215984408-c79baa5a-5a36-499d-813b-208081de57b2.png)


Filter 적용   
```
[
   {
      "Metric":"db.load.avg",
      "GroupBy":{
         "Group":"db.sql_tokenized",
         "Limit":5
      },
      "Filter":{
         "db.wait_event.name":"IO:DataFilePrefetch"
      }
   }
]
```
   
결과 예시   
![image](https://user-images.githubusercontent.com/42329161/215984585-12612bf5-9558-421b-a9bf-94eede034e1c.png)
