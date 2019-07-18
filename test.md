# Exaplme
```
스쿱 설치 이후
3번문제
 1)  mysql -u root -p
   -- test database 생성
   CREATE DATABASE test;
   EXIT;

 2) filezilla 툴로 쿼리 파일 서버로 전송(CM host) - sql파일 두개
 3) data import (테이블 및 데이터 생성) - CM host : 마리아DB
    mysql -u root -p test < ./authors.sql
    mysql -u root -p test < ./posts.sql

 4) Extract Data
   sqoop import --connect jdbc:mysql://172.31.7.33/test --username training --password training --table authors --target-dir /user/training/authors --hive-import --create-hive-table --hive-table default.authors
   sqoop import --connect jdbc:mysql://172.31.7.33/test --username training --password training --table posts --target-dir /user/training/posts --hive-import --create-hive-table --hive-table default.posts

   붓고나서 Hue에서 확인
 
 5) 캡쳐

  참고) -- 그룹 추가 및 권한변경
     //sudo groupadd supergroup
     //sudo usermod -a -G supergroup training
	 
	 
4번문제
 1) Hue에서 Hive 편집기 오픈
 2) 아래 쿼리 실행
   insert overwrite directory '/results'
	row format delimited fields terminated by '\t'
	select A.id,
       A.first_name AS fname,
       A.last_name AS lname,
       B.num_posts AS num_posts
	from authors A
	inner join ( select author_id, count(author_id) AS num_posts
                from posts P
               group by author_id ) B
    on A.id = B.author_id
	
  3) 캡쳐
	
5번문제
 1) MySql test.results 테이블 생성
CREATE TABLE `results` (
  `id` int NOT NULL,
  `fname` varchar(255) default NULL,
  `lname` varchar(255) default NULL,
  `num_posts` int default 0
);
 2) 스쿱 export
 	sqoop export --connect jdbc:mysql://172.31.7.33/test --username training --password training --table results --export-dir /results --input-fields-terminated-by '\t'
	


```
