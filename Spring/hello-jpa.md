# Hello JPA 실습

Maven 프로젝트에서 pom.xml에 라이브러리 추가

```
<dependencies>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>5.3.10.Final</version>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>2.1.214</version>
        </dependency>
</dependencies>
```

![Untitled](https://user-images.githubusercontent.com/103729286/212546567-68c5d528-393c-4f2d-bea4-20a1780ac8e5.png) 

JPA는 인터페이스이고, 인터페이스의 구현체로 hibernate를 선택함 



### JPA 설정 파일

클래스 패스의 META-INF/persistence.xml 에 생성하면 별도의 설정 없이 JPA가 인식할 수 있다.

- persistence-unit name으로 이름 지정
- javax.persistence로 시작: JPA 표준 속성
- hibernate로 시작: 하이버네이트 전용 속성

```
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
</persistence>
```


**데이터베이스 방언**

JPA는 특정 DB에 종속적이지 않게 설계되어있음  

- 각각의 DB마다 제공하는 SQL 문법, 함수는 조금씩 상이함 (페이징 등등)
- hibernate.dialect 속성에 지정
- MySQL : org.hibernate.dialect.MySQL5InnoDBDialect  




### JPA 어플리케이션 개발

![Untitled (1)](https://user-images.githubusercontent.com/103729286/212546667-4f1921bc-e05b-40b6-a78b-6efc304ac4d3.png)

Persistence 클래스에서 persistence.xml 설정 정보를 읽어서 EntityManagerFactory 생성 

EntityManagerFactory에서 여러 개의 EntityManager 생성  

```
package com.jpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaMain {

    public static void main(String[] args) {

        // EntityManagerFactory는 application 로딩 시점에 하나만 생성해서 전체에서 공유
        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("hello");

        // EntityManager는 Thread간에 공유하지 않음
        EntityManager entityManager = entityManagerFactory.createEntityManager();

        // JPA의 모든 데이터 변경은 트랜잭션 안에서 실행
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();

        try {
            Member member = new Member();
            member.setId(1L);
            member.setName("Member1");
            entityManager.persist(member);

            transaction.commit();
        } catch (Exception e) {
            transaction.rollback();
        } finally {
            entityManager.close();
        }

        entityManagerFactory.close();

    }
}
```


![Untitled (2)](https://user-images.githubusercontent.com/103729286/212546703-f181681c-0b8b-445b-925e-709483be5d87.png)





조회, 수정, 삭제 쿼리
```
//select
Member findMember = entityManager.find(Member.class, 1L);
System.out.println(findMember.getName());

//update
findMember.setName("Member2");

//delete
entityManager.remove(findMember);
transaction.commit();
```

![Untitled (3)](https://user-images.githubusercontent.com/103729286/212546715-55315a04-059e-4c96-b481-91ff885461b0.png)





### JPQL

- **JPA를 사용하면 엔티티 객체를 중심으로 개발**
- **JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어 제공**
- **애플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 검
색 조건이 포함된 SQL이 필요**


`List<Member> result = entityManager.createQuery("select m from Member as m", Member.class).setFirstResult(1).setMaxResults(10).getResultList();`   

JPA는 쿼리를 짤 때 ****테이블이 아닌 객체를 대상으로 검색하는 객체 지향 쿼리****

**SQL은 데이터베이스 테이블을 대상으로 쿼리**


![Untitled (4)](https://user-images.githubusercontent.com/103729286/212546620-2d38e0e8-6ce2-48f3-973e-a9aba5016192.png)

