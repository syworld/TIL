# 사용자 수에 따른 규모 확장성

## 단일 서버

- 웹 앱, 데이터베이스, 캐시 등이 전부 서버 한 대에서 실행된다.

사용자가 늘면 서버 하나로는 충분하지 않아서 여러 서버를 두어야 한다.

1. 웹/모바일 트래픽 처리 용도 서버 (웹 계층)
2. 데이터베이스 서버 (데이터 계층)
    1. RDBMS
        - SQL를 사용하여 여러 테이블에 있는 데이터를 그 관계에 따라 Join하여 합칠 수 있음
    2. NoSQL
        - 아주 낮은 latency를 요구할 때
        - 아주 많은 양의 데이터 저장할 필요가 있을 때
        - 다루는 데이터가 unstructured
        - 데이터를 serialize/ deserialize 할 수 있기만 하면 됨



## Scale up vs Scale out 

수직적 규모 확장(scale up)

- 장점
    - 서버로 유입되는 트래픽의 양이 적을 때 단순한 확장
- 단점
    - 규모 확장의 한계 (CPU, 메모리 무한대 증설할 방법 없음)
    - 장애 발생시 중단 현상 (failover 방안 / redundancy 방안 없음)
    


## Load Balancer

- 부하 분산 집합에 속한 웹 서버들에게 트래픽 부하를 고르게 분산하는 역할을 한다.
- no failover 해소
- 웹 계층의 availability 향상

사용자 → DNS에서 로드밸런서의 public IP address 받음 → 해당 public IP address로 로드밸런서 접속 →HTTP 요청을 서버 Private IP address에 분산 

- 웹 서버는 클라이언트의 접속을 직접 처리하지 않는다.
- 서버 간 통신에는 private IP address가 사용되는데,  private IP addresss는 같은 네트웨크에 속한 서버 사이의 통신에만 사용되고, 인터넷을 통해서는 접속이 불가능하다.
- 웹 사이트로 유입되는 트래픽이 증가하면 로드밸런서가 자동적으로 트래픽을 분산한다.



## 데이터베이스 다중화

- 서버 사이에 master-slave 관계 설정하고 데이터 원본은 master 서버에, 사본은 slave 서버에 저장하는 방식
- 대부분 어플리케이션은 read  >> write
    - master에 write operaiton, slave에게 사본 전달
    - slave는 read operation만 지원
    - 일반적으로 slave 데이터베이스가 master 데이터베이스보다 많다. (read operation)

### 데이터베이스 다중화의 장점 

- 성능 👍 - 모든 데이터 변경 연산은 maser 데이터베이스 서버에만 전달되고 읽기 연산은 slave 데이터베이스 서버들로 분산된다. 병렬로 처리될 수 있는 query의 수가 늘어나므로 성능이 좋아짐
- 안정성(reliability)  👍 - 데이터를 여러 장소에 다중화 → 데이터베이스 서버 가운데 일부가 파괴되어도 데이터는 보존됨.
- 가용성(availability)   👍  - 하나의 데이터베이스 서버에 장애가 발생하더라도 다른 서버에 있는 데이터를 가져와 계속 서비스가 가능함

### 장애 지점 

- slave 서버가 한 대인데 다운된다면?
    - read는 일시적으로 모두 master 서버에서 수행하고 즉시 새로운 slave 서버 생성
- master 서버가 다운된다면?
    - slave 서버가 새로운 master서버가 되며, 모든 데이터베이스 연산은 일시적으로 새로운 master 서버에서 수행하고 새로운 slave 서버 생성
        
        <참고>
        
        - slave 서버에 보관된 데이터가 최신 상태가 아닐 때?
            - 없는 데이터는 recovery script 돌려서 추가
            - multi-masters
            - circular replication
    

## latency 개선  : cache + CDN


## Cache

- 값비싼 연산 결과
- 자주 참조되는 데이터
- latency 개선

### cache tier

별도의 캐시 계층을 두면 성능이 개선되고, 데이터베이스의 부하를 줄일 수 있으며 캐시 계층의 규모를 독립적으로 확장 가능해진다. 

- read-through caching strategy

### 주의점

- 캐시는 휘발성 메모리
- 캐시 expire 시점
- 여러 지역에 걸쳐 시스템을 확장해 나가는 경우 캐시와 저장소 사이의 일관성(consistency) 유지에 어려움
- 캐시 서버를 한 대만 두는 경우?
    - Single Point of Failure = 어떤 특정 지점에서의 장애가 전체 시스템의 동작을 중단시켜버릴 수 있는 경우
- SPOF를 피하려면, 여러 지역에 걸쳐 캐시 서버를 분산시켜야 함
- 캐시 메모리 크기 - overprovision
- eviction 정책
    - LRU
    - LFU
    - FIFO



## CDN

정적 컨텐츠를 전송하는 데 사용, 지리적으로 분산된 서버의 네트워크

이미지, 비디오, CSS, JavaScript 파일 등을 캐시할 수 있다. 



## Stateless 웹 계층

### 웹 계층을 수평적으로 확장하려면 

- 사용자 세션 데이터와 같은 상태 정보를 웹 계층에서 제거
- 상태 정보를 RDBMS나 NoSQL에 보관, 필요할 때 가져오도록 함

### 상태 정보 의존적 아키텍처

- 상태 정보를 보관하는 서버는 클라이언트 정보(상태)를 유지하여 요청들 사이에 공유되도록 함
- 같은 클라이언트로부터 요청은 항상 같은 서버로 전송되어야함
    - 로드밸런서에서 sticky session 지원하지만 부담
    - 로드밸런서 뒷단에 서버 추가/제거 어려움

### 무상태 아키텍쳐 

- 사용자로부터 HTTP 요청은 어떤 웹 서버로도 전달
- 웹 서버가 shared storage로 부터 상태 정보 데이터를 가져옴 ( 상태 정보의 물리적 분리)
- shared storage
    - RDBMS
    - Memchaed/Redis
    - NoSQL
- 상태 정보가 웹 서버들로부터 제거되었으면 트래픽 양에 따라 웹 서버를 생성, 제거하면서 자동으로 규모 확장 가능



## Data Center

장애가 없는 상황에서, 사용자는 가장 가까운 데이터 센터로 안내됨 

- geoDNS-routing - DNS 서비스에서 사용자의 위치에 따라 도메인 이름을 어떤 IP address로 변환할지 결정

주의점

- 트래픽 우회 - 올바른 데이터 센터로 트래픽을 보내는 효과적인 방법
- 데이터 동기화
    - 데이터 센터마다 별도의 데이터베이스 사용하는 상황?
        - 장애가 failover되어 트래픽이 다른 데이터베이스로 우회된다고 해당 데이터센터에 원하는 데이터가 없을 수 있음 → 여러 데이터센터에 걸쳐 다중화하는 전략
- 테스트와 배포



## Message Queue

메시지의 무손실(durability) 보장, 비동기 통신(asynchronous communication) 지원하는 컴포넌트 

서비스-서버 간 결합이 느슨해져서 규모 확장성이 보장되어야 하는 안정적인 어플리케이션을 구성하기 좋음 



## Log, Metric, Automation

### log

- 에러 로그를 서버 단위로 모니터링 할 수도 있지만, 로그를 단일 서비스로 모아 주는 도구 활용

### metric

- 호스트 단위 메트릭: CPU, memory, disk I/O
- aggregated 메트릭: 데이터베이스 계층 성능, 캐시 계층 성능
- 핵심 비즈니스 메트릭: daily active user, revenue, retention

### automation

- continuous integration 도구를 통해 코드 검증 절차를 자동으로 거치도록 할 수 있다.



## 데이터베이스 규모 확장

### 수직적 확장 (=scale up)

- 하드웨어 한계. 무한정 증설 불가능
- SPOF 위험성이 큼
- 고성능 서버로 갈수록 비용이 많이듬

### 수평적 확장 (= sharding)

- 데이터베이스를 shard 단위로 분할
- 모든 shard는 같은 스키마를 쓰지만 샤드에 보관되는 데이터 사이에는 중복이 없음
- sharding key (= partition key) 설정
    - 데이터가 어떻게 분산될지 정하는 하나 이상의 칼럼으로 구성
    - 샤딩 키를 통해 디비에 질의를 보내어 데이터 조회/ 변경 처리하므로 효율을 높임
- 샤딩 주의점
    - 데이터의 재 샤딩 resharding
        - 데이터가 너무 많아져서 하나의 샤드로 감당 불가능할 때
        - 샤드 간 데이터 분포가 균등하지 못하여 어떤 샤드에 할당된 공간 소모가 다른 샤드에 비해 빨리 진행될 때
        - shard exhaustion → consistent hashing 기법 활용하여 문제 해결
    - celebrity problem (= hotspot key problem)
        - 특정 샤드에 질의가 집중되어 서버에 과부하가 걸리는 문제
    - join and de-normalization
        - 하나의 데이터베이스를 여러 샤드 서버로 쪼개면 여러 샤드에 걸친 데이터를 조인하기가 힘들어짐 → 데이터베이스를 비정규화하여 하나의 테이블에서 질의가 이뤄지게 수행하여 해결



