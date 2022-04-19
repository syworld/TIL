## 데이터베이스 기초

데이터베이스를 관리하는 시스템을 DBMS라고 부르며 주로 오라클, MySQL, MSSQL 서버 등이 있다. 데이터의 손실이 발생하면 안되기 때문에  DBMS는 transaction을 보장하여 데이터의 신뢰성을 높여주기도 한다.


DBMS 기능
- 데이터 추가/조회/변경/삭제
- 데이터 무결성 유지
- 트랜잭션 관리
- 데이터 백업 및 복원
- 데이터 보안

### 테이블과 레코드

table: RDBMS에서 데이터를 저장하는 장소

schema: 테이블의 구조와 관련된 정보를 테이블. 스키마는 하나의 데이터에 대한 구조를 나타낸다.

record: 하나의 테이블은 여러 개의 레코드로 구성된다. 각 레코드는 테이블의 스키마에 정의된 칼럼에 해당하는 값을 갖는다.

### Primary Key와 Index

테이블에 저장한 레코드를 사용하려면 각 레코드를 구별하는 방법이 필요하다. 인덱스는 데이터의 순서를 미리 정렬해서 저장할 때 사용된다. Primary Key도 인덱스의 일종으로서 인덱스는 중복된 값에 대한 정렬이 가능하지만 Primary Key는 중복된 값을 가질 수 없다는 차이가 있다.

### 데이터베이스 프로그래밍의 일반적 순서

DBMS client, Database, DBMS

MySQL을 설치할 때 사용되는 기본 클라이언트는 mysql.exe이지만, MySQL Workbench라는 클라이언트를 따로 설치할 수도 있다. 자바에서는 JDBC Driver가 클라이언트의 역할을 대신한다.


![db2](https://user-images.githubusercontent.com/103729286/163995852-773271cc-1f96-4872-bd9b-cd0b6e98f3d7.png)




### 데이터베이스 생성

1. root 계정으로 DB 연결 후 데이터베이스 생성

   `create database chap14 default character set utf8;`
   
   <img width="741" alt="db1" src="https://user-images.githubusercontent.com/103729286/163995876-427b6759-8bf7-4eb5-8da7-07f429a2eb4c.png">



2. MySQL에서 사용할 사용자 추가

   DBMS는 OS와 별도로 자신만의 사용자 계정을 갖고 있다. MySQL은 root 계정이 기본적으로 제공되는 데이터베이스 관리 계정이다.

   `CREATE USER ‘[db 계정]’@’[해당 계정이 접근하는 호스트]’ IDENTIFIED BY ‘[암호]’`

   `GRANT [권한 목록] ON [데이터베이스].[대상] to ‘[db 계정]’@’[해당 계정이 접근하는 호스트]’`

   `[권한 목록]` : select, insert, update, delete, create, drop

    ```
    CREATE USER 'james'@'localhost' IDENTIFIED BY '1234srf';
    GRANT ALL PRIVILEGES ON chap14.* TO 'james'@'localhost'; // chap14 데이터베이스의 모든 테이블에 권한 부여 
    ```



## SQL 기초

### **주요 SQL 타입**

`CHAR, VARCHAR, LONG VARCHAR`

`NUMERIC, DECIMAL, INTEGER`

`TIMESTAMP, TIME, DATE`

`CLOB, BLOB`

DBMS는 표준 SQL 타입뿐만 아니라 DBMS 자체적으로 확장 타입을 추가 제공한다. 때문에 DBMS마다 서로 호환되지 않고 표준 SQL 타입과 다른 제약을 갖는다.

ex) MySQL의 DATETIME

### 테이블 생성 쿼리

MySQL에서 테이블은 InnoDB 저장 엔진을 사용해서 생성한다.

```
CREATE TABLE MEMBER(
    MEMBERID VARCHAR(10) NOT NULL PRIMARY KEY,
    PASSWORD VARCHAR(100) NOT NULL,
)
```

`ALTER TABLE MEMBER ADD EMAIL VARCHAR(100) NULL;` 칼럼 추가

### 데이터 삽입 쿼리

`INSERT INTO MEMBER (MEMBERID, PASSWORD) VALUES ('test', 'test1234');`

칼럼 목록을 표시하지 않으면 전체 칼럼에 대해 값을 지정해야 한다.

### 테이블 조회 쿼리

`SELECT * FROM MEMBER WHERE EMAIL <> '';`  EMAIL 칼럼의 값이 빈 문자열이 아닌 레코드 검색

`SELECT * FROM MEMBER WHERE EMAIL IS NOT NULL;`

`SELECT * FROM MEMBER WHERE EMAIL LIKE '이%' ORDER BY MEMBERID DESC;`

`SELECT COUNT(*) FROM MEMBER;`

### 데이터 수정 쿼리

`UPDATE MEMBER SET MEMBERID='test'` // 모든 레코드 업데이트

`UPDATE MEMBER SET MEMBERID='test' WHERE PASSWORD LIKE '%t%';`

### 데이터 삭제 쿼리

`DELETE FROM MEMBER`  // 모든 레코드 삭제

`DELETE FROM MEMBER WHERE MEMBERID='test2';`
