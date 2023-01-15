# JPA Introduction


JDBC

SQL Mapper - JdbcTemplate, MyBatis 

JPA   




객체를 관계형 DB에 관리 → 패러다임의 불일치 

RDB ↔ SQL 변환 ↔ Java 객체 


### **객체와 관계형 데이터베이스의 차이**

1. 상속

- 객체 : 상속 관계
- 테이블: Super - Sub 타입 관계

2. 연관관계

- 객체 : 참조 member.getTeam()
- 테이블: 외래키 JOIN ON M.TEAM_ID = T.TEAM_ID

3. 데이터 타입
4. 데이터 식별 방법


### JPA

ORM (Object-relational mapping)

- 객체는 객체대로 설계
- 관계형 데이터베이스는 관계형 데이터베이스대로 설계
- ORM 프레임워크가 중간에서 매핑
- Java 진영의 ORM 기술 표준은 JPA ( Java Persistent API)

JPA

- Entity 분석
- SQL 생성
- JDBC API 사용 → DB
- 패러다임 불일치 해결 ( 상속, 연관관계, 객체 그래프 탐색, 비교)
- 인터페이스의 모음

JPA 성능 최적화

- 1차 캐시, 동일성 보장
    - 동일한 트랜잭션에서 조회한 엔티티는 같음을 보장
    - 트랜잭션 격리 수준을 어플리케이션에서 Repeatable Read 보장
- 트랜잭션을 지원하는 쓰기 지연 transactional write-behind
    - 트랜잭션 커밋 전까지 insert sql 모았다가 JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전달  → 네트워크 통신 비용 절감
    - update, delete로 인한 row lock 시간 최소화
- 지연 로딩 Lazy Loading
    - 지연 로딩: 객체가 실제 사용될 때 로딩
    - 즉시 로딩: JOIN SQL로 한번에 연관된 객체까지 미리 조회
