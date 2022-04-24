# 개략적인 규모 추정


## 2의 제곱수

데이터 볼륨의 단위를 2의 제곱수로 표현

2^10 = 1천 = 1KB

2^20 = 1백만 = 1MB

2^30 = 10억 = 1GB

2^40 = 1조 = 1TB

2^50 = 1000조 = 1PB 
 
 
 
## 응답지연 값

https://colin-scott.github.io/personal_website/research/interactive_latency.html
![image](https://user-images.githubusercontent.com/103729286/164912027-e8241c9f-f3d8-468c-a9e0-03d870d5de1a.png)


- 메모리는 빠르지만 디스크는 느리다
- disk seek는 가능한 한 피하라
- 단순한 압축 알고리즘은 빠르다
- 데이터를 인터넷으로 전송하기 전에 가능하면 압축하라
- 데이터 센터는 보통 여러 region에 분산되어 있고 센터들 간에 데이터를 주고받는 데 시간이 걸린다



## 가용성

high availability = 시스템이 오랜 시간 동안 지속적으로 중단 없이 운영될 수 있는 능력 

- QPS
- 최대 QPS
- 저장소 요구량
- 캐시 요구량
- 서버 수 추정
