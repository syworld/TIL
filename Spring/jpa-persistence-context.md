# JPA 영속성 컨텍스트


### 영속성 컨텍스트

= 엔티티를 영구 저장하는 환경

EntityManagerFactory에서 생성된 EntityManager가 DB 커넥션 사용 

`EntityManager.persist(entity);` → Entity를 영속성 컨텍스트에 저장한다. 



### 엔티티의 생명주기

<img width="585" alt="Untitled" src="https://user-images.githubusercontent.com/103729286/212547022-0d8a80bc-6a71-4886-81ce-348485265151.png">

- 비영속 new/transient
    - JPA와 상관없는 상태
    - `Entity entity = new Entity();`
    - 객체를 생성한 상태
- 영속 managed
    - `EntityManager.persist(entity);`
    - 객체를 저장/ 조회한 상태
- 준영속 detached
    - `entitymanager.detach(entity);`
    - 영속 상태의 엔티티가 영속성 컨텍스트에서 분리된 상태
- 삭제 removed
    - `entitymanager.remove(entity);`



### 영속성 컨텍스트 이점

-> **1차 캐시**
- `entitymanager.find();`
- 1차 캐시에서 조회하고 반환. 1차 캐시에 없으면 DB 조회하고 1차 캐시에 저장 후 반환
- 사실 앤티티매니저가 트랜잭션 단위에서 만들어지고 종료되기 때문에 요청이 끝나면 캐시도 지워짐

-> **영속 엔티티의** **동일성(identity) 보장**

```
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");
System.out.println(a == b); //동일성 비교 true
```

1차 캐시로 통해 조회하기 때문에 트랜잭션 격리 수준을 데이터베이스가 아닌 **애플리케이션 차원에서 REPEATABLE READ로** **제공한다.**


-> **트랜잭션을 지원하는 쓰기 지연 transactional write-behind**

JPA가 insert SQL 를 생성해서 영속성 컨텍스트의 쓰기 지연 SQL 저장소에 쌓아놓는다. 

트랜잭션을 커밋하는 시점에 쓰기 지연 SQL 저장소에 있는 sql이 flush되면서 commit된다. 

`hibernate.jdbc.batch_size` : 모았다가 한번에 DB에 쿼리할 사이즈 설정


-> **변경 감지 Dirty Checking**

<img width="637" alt="Untitled (1)" src="https://user-images.githubusercontent.com/103729286/212547105-6409ffd0-4b4e-4684-9412-94f4ca4200e4.png">

값이 바뀌면 JPA가 엔티티와 스냅샷(DB에서 처음 가져온 상태) 비교해서 트랜잭션 커밋 시점에 UPDATE SQL flush  

**flush**? 영속성 컨텍스트의 변경내용을 DB에 반영

- dirty checking
- 변경된 엔티티를 쓰기 지연 SQL 저장소에 등록
- 쓰기 지연 SQL 저장소의 쿼리를 DB에 전송

`entityManager.flush()`로 flush를 직접 호출하거나, `트랜잭션 커밋` 또는 `JPQL 쿼리가 실행`될 때 flush는 자동 호출된다. 


-> **지연 로딩 Lazy Loading**

   

### 준영속 상태

<img width="672" alt="Untitled (2)" src="https://user-images.githubusercontent.com/103729286/212547125-97d21446-0551-48d3-8a0f-78a131b127d4.png">

준영속 상태로 만들었으므로 UPDATE SQL은 생성되지 않는다. 

`entityManager.detach(entity);` 특정 엔티티만 준영속 상태로 전환

`entityManager.clear();` // 영속성 컨텍스트의 모든 엔티티를 초기화 

`entityManager.close();` // 영속성 컨텍스트를 종료
