# 객체 지향 설계 5원칙 - SOLID

SOLID 원칙 - High Cohesion, Loose Coupling 재정립 

- 결합도: 클래스간의 상호 의존 정도 → 낮은 결합도여야 상호 의존성이 줄어들어 객체의 재사용, 유지보수 용이
- 응집도: 클래스 내부에 존재하는 구성 요소들의 기능적 관련성 → 높은 응집도여야 독립성이 높아져 재사용, 유지보수 용이

### 1. Single Responsibility Principle - 단일 책임 원칙

> 어떤 클래스를 변경해야 하는 이유는 오직 하나뿐이어야 한다.
> 

데이터베이스 정규화 과정 → 테이블과 필드에 대한 단일 책임 원칙의 적용 

객체 지향 4대 특성 중 '추상화'와 연관 



### 2. Open Closed Principle - 개방 폐쇄 원칙

> 소프트웨어 엔티티(클래스, 모듈, 함수)는 확장에 대해서는 열려 있어야 하지만 변경에 대해서는 닫혀 있어야 한다.


= 자신의 확장에는 열려 있고, 주변의 변화에 대해서는 닫혀 있어야 한다. 


개방 폐쇄 원칙의 예:

JDBC 인터페이스 - JDBC 드라이버 관계

<img width="450" alt="image" src="https://user-images.githubusercontent.com/103729286/165475013-88da365a-554b-4e06-a8da-36045aef2916.png">


- JDBC를 사용하는 클라이언트는 데이터베이스가 오라클에서 MySQL로 바뀌더라도 Connection을 설정하는 부분 외에는 따로 수정할 필요가 없다. 
- 자바 어플리케이션은 데이터베이스(주변 변화)에 대해 닫혀있음
- 데이터베이스를 교체한다는 것은 데이터베이스는 자신의 확장에는 열려있음

Java
- 소스코드는 운영체제 변화에 닫혀 있고, 각 운영체제별 JVM은 확장에 열려 있는 구조

<img width="490" alt="image" src="https://user-images.githubusercontent.com/103729286/165474931-bb9c2943-c736-4e10-bd78-8201c5526a96.png">


### 3. Liskov Substitution Principle  -  리스코프 치환 원칙

> 서브 타입은 언제나 자신의 기반 타입(base type)으로 교체할 수 있어야 한다.
> 

객체 지향 4대 특성 중 '상속'과 연관 

하위 클래스 is a kind of 상위 클래스 : 하위 분류는 상위 분류의 한 종류다 

- `Animal pororo = new Penguin()`
- 펭귄 한 마리가 태어나 pororo로 이름을 짓고, pororo가 동물의 행위(메소드)를 하는 데 이상함이 없다. 
- 하위 클래스의 인스턴스는 상위형 객체 참조 변수에 대입해 상위 클래스의 인스턴스 역할을 하는 데 문제가 없어야 한다.
- 상속 - 계층도 X, 조직도 X, 분류도 O 

구현 클래스 is able to 인터페이스: 구현 분류는 인터페이스할 수 있어야 한다. 

하위형에서 선행 조건은 강화될 수 없다. 

하위형에서 후행 조건은 악회될 수 없다. 

하위형에서 상위형의 불변 조건은 반드시 유지되어야 한다.  



### 4. Interface Segregation Principle - 인터페이스 분리 원칙

> 클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안 된다.
> 

단일 책임 원칙 적용하는게 더 좋은 해결책이지만 특별한 경우, 인터페이스 분리 원칙 적용 

상위 클래스는 풍성할 수록 좋고, 인터페이스는 작을수록 좋다. 

![isp](https://user-images.githubusercontent.com/103729286/165473661-bdb1618f-72e0-4541-a59e-a11111a35a39.png)


```
Animal pororo = new Penguin()
Animal benny = new Bird()

// 상위 클래스형의 참조 변수를 이용하여 상속을 사용한다. 공통의 메소드 -> 추상 메서드로 선언 
// 아래는 형변환이 필요한 경우. 그래도 Animal인 객체 참조 변수를 상위 클래스로 정의하자! 
Systme.out.println((Penguin) pororo).penguinMethod()); 
Systme.out.println((Bird) pororo).birdMethod());
```
빈약한 상위 클래스를 이용한 경우 형변환이 발생한다. 객체 참조 변수를 Animal이 아닌 Penguin형으로, Bird 형으로 선언하고 사용할 수 있지만, 그럼 상속 구조를 만들 필요가 없다.
상위 클래스형의 참조 변수를 이용해야 상속의 가장 큰 혜택을 볼 수 있다.  



### 5. Dependency Inversion Principle - 의존 역전 원칙

> 고차원 모듈은 저차원 모듈에 의존하면 안 된다. 이 두 모듈 모두 다른 추상화된 것에 의존해야 한다.
> 

> 추상화된 것은 구체적인 것에 의존하면 안 된다. 구체적인 것이 추상화된 것에 의존해야 한다.
> 

> 자주 변경되는 구체 클래스에 의존하지 마라
> 

<img width="356" alt="image" src="https://user-images.githubusercontent.com/103729286/165476911-77374806-73ce-4c90-be49-07dac532596a.png">

자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 하는 것이 의존 역전 원칙이다. 



### SoC ( Seperation Of Concerns)

하나의 속성, 하나의 메서드, 하나의 클래스, 하나의 모듈, 하나의 패키지에는 하나의 관심사만 들어 있어야 한다. 관심사가 다르고 변화의 시기가 다르면 분리해야 한다. → SRP, ISP, OCP
