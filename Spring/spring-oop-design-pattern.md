# 스프링이 사랑한 디자인 패턴 

> 객체 지향 4대 원칙(캡슐화, 상속, 추상화, 다형성)을 가지고, 설계 원칙(SOLID)를 사용해서 디자인 패턴을 설계 한다!
> 

디자인 패턴은 객체 지향의 특성 중 상속, 인터페이스, 합성(객체를 속성으로 사용)을 이용한다. 



### Adapter Pattern

> 호출당하는 쪽의 메서드를 호출하는 쪽의 코드에 대응하도록 중간에 변환기를 통해 호출하는 패턴
> 

- 개방 폐쇄 원칙(OCP)을 활용한 설계 패턴. ODBC/ JDBC와 JRE가 어댑터 역할 수행
- 객체를 속성으로 만들어서 참조(합성)하는 디자인 패턴

< 어댑터 패턴이 적용되지 않은 코드 >

```java
public class ServiceA{
	void runServicA(){
		System.out.println("ServiceA");
	}
}

public class ServiceB{
	void runServicB(){
		System.out.println("ServiceB");
	}
}

public class ClientWithNoAdapter{
	public static void main(String[] args){
		ServiceA sa1 = new ServiceA();
		ServiceB sb1 = new ServiceB();
	
		sa1.runServcieA();
		sb1.runServiceB();
	}
}
```

< 어댑터 패턴이 적용된 코드 >

```java
public class AdapterServiceA{
	ServiceA sa1 = new Service();

	void runService(){
		sa1.runServiceA();
	}
}

public class AdapterServiceB{
	ServiceB sa2 = new Service();

	void runService(){
		sa2.runServiceB();
	}
}

public class ClientWithAdapter{
	public static void main(String[] args){
		AdapterServiceA asa1 = new AdapterServiceA();
		AdapterServiceA asb1 = new AdapterServiceA();
	
		asa1.runServcie();
		asb1.runService();
	}
}
```



### Proxy Pattern

> 제어 흐름을 조정하기 위한 목적으로 중간에 대리자를 두는 패턴
> 

실제 서비스 객체가 가진 메서드와 같은 이름의 메서드를 사용하는데 이를 위해 인터페이스를 사용한다.

인터페이스를 사용하며 서비스 객체가 들어갈 자리에 대리자 객체를 투입해 클라이언트 쪽에서는 실제 서비스 객체 또는 대리자 객체 어떤거를 통해 메서드를 호출하고, 반환값을 받는지 모르게 처리가 가능하다. 


<img width="488" alt="Untitled" src="https://user-images.githubusercontent.com/103729286/165497768-bf2973cd-8b7b-493a-a3a5-9c953a834137.png">



```java
public Interface IService{
	String runSomething();
}

public class Service implements IService{
	public String runSomething(){
		return "Service runs";
	}
}

public class Proxy implements IService{
	IServcie service1;

	public String runSomething(){
		System.out.println("호출에 대한 흐름 제어가 주목적, 반환 결과를 그대로 전달");

		service1 = new Service();
		return service1.runSomething();
	}
}

public class ClientWithProxy{
	public static void main(String[] args){
		IService proxy = new Proxy();
		System.out.println(proxy.runSomething());
	}
}
```

**프록시 패턴은 실제 서비스 메서드의 반환값에 가감하는 것을 목적으로 하지 않고 제어의 흐름을 변경하거나 다른 로직을 수행하기 위해 사용한다.**

- 대리자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이때 인터페이스를 사용한다.
- 대리자는 실제 서비스에 대한 참조 변수를 갖는다(합성)
- 대리자는 실제 서비스의 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게 돌려준다.
- 대리자는 실제 서비스의 메서드 호출 전후에 별도의 로직을 수행할 수도 있다.
- 개방 폐쇄 원칙(OCP), 의존 역전 원칙(DIP)를 활용한 설계 패턴




### Decorator Pattern

> 메서드 호출의 반환값에 변화를 주기 위해 중간에 장식자를 두는 패턴
> 

프록시 패턴과 동일한 구조를 가지고, 다른 점은 클라이언트가 받는 반환값에 장식을 더한다. 


```java
public Interface IService{
	public abstract String runSomething();
}

public class Service implements IService{
	public String runSomething(){
		return "Service runs";
	}
}

public class Decorator implements IService{
	IServcie service1;

	public String runSomething(){
		System.out.println("호출에 대한 장식 주목적, 클라이언트에게 반환 결과에 장식을 더하여 전달");
		service1 = new Service();
		return "플러스" + service1.runSomething();
	}
}

public class ClientWithDecorator{
	public static void main(String[] args){
		IService decorator = new Decorator();
		System.out.println(proxy.runSomething());
	}
}
```

- 장식자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이때 인터페이스를 사용한다.
- 장식자는 실제 서비스에 대한 참조 변수를 갖는다(합성)
- 장식자는 실제 서비스의 같은 이름을 가진 메서드를 호출하고 그 반환값에 장식을 더해 클라이언트에게 돌려준다.
- 장식자는 실제 서비스의 메서드 호출 전후에 별도의 로직을 수행할 수도 있다.
- 개방 폐쇄 원칙(OCP), 의존 역전 원칙(DIP)를 활용한 설계 패턴




### Singleton Pattern

> 클래스의 인스턴스, 즉 객체를 하나만 만들어 사용하는 패턴
> 

커넥션 풀, 스레드 풀, 디바이스 설정 객체 등과 같은 경우 인스턴스를 여러 개 만들게 되면 불필요한 자원을 사용하게 된다. 싱글턴 패턴을 적용할 경우 두 개의 객체가 존재할 수 없다. 

- new를 실행할 수 없도록 생성자에 private 접근 제어자를 지정한다.
- 유일한 단일 객체를 반환할 수 있는 static method가 필요하다.
- 유일한 단일 객체를 참조할 수 있는 static 참조 변수가 필요하다.
- 단일 객체는 쓰기 가능한 속성을 갖지 않는 것이 정석이다.


```java
public class Singleton{
  static Singleton singletonObject; // 정적 참조 변수
  
  private Singleton(){}; // 생성자 private 처리 
  
  // 객체 반환 정적 메소드 
  public static Singleton getInstance(){
    if(singletonObject == null){
      singletonObject = new Singleton();
    }
    return singletonObject;
  }
  
}

public class Client{
  public static void main(String[] args){
		// Singleton singleton = new Singleton(); new로 인스턴스 생성 불가능
		Singleton s1 = Singleton.getInstance();
		Singleton s2 = Singleton.getInstance();
		Singleton s3 = Singleton.getInstance();

  }
}

```

<img width="479" alt="Untitled (1)" src="https://user-images.githubusercontent.com/103729286/165497995-38dd6d83-08a6-4e41-a6a4-0c6322457c33.png">





### Template Method Pattern

> 상위 클래스의 견본 메소드에서 하위 클래스가 오버라이딩한 메소드를 호출하는 패턴
> 

- 상위 클래스에 공통 로직을 수행하는 template method와 하위 클래스에 오버라이딩을 강제하는 abstract method 또는 선택적으로 오버라이딩을 할 수 있는 hook method를 두는 패턴이다.
- 의존 역전 원칙(DIP)를 활용한 설계 패턴

```java
public abstract class Animal{
  // template method
  public void playWithOwner(){
    System.out.println("공통 출력1");
    
    play(); // abstract method
    runSomething(); // hook method

    System.out.println("공통 출력2");
  }
  
  // abstract method
  abstract void play();
  
  // hook method
  void runSomething(){
    System.out.println("동물");
  }
}
```

```java
public class Dog extends Animal{
  // abstract method override
  @Override
  void play() {
    System.out.println("멍멍!");
  }
  
  // hook method override 
  @Override
  void runSomething() {
    System.out.println("멍멍 동물");
  }
}

public class Cat extends Animal{
  // abstract method override
  @Override
  void play() {
    System.out.println("야옹!");
  }

  // hook method override 
  @Override
  void runSomething() {
    System.out.println("야옹 동물");
  }
}
```

```java
public class Driver{
  public static void main(String[] args){
		Animal bolt = new Dog();
		Animal kitty = new Cat();

		bolt.playWithOwner();
		kitty.playWithOwner();
    
  }
}
```

<img width="537" alt="Untitled (2)" src="https://user-images.githubusercontent.com/103729286/165498138-534ff763-7569-4091-a1ca-23e4169e4225.png">




### Factory Method Pattern

> 오버라이드된 메소드가 객체를 반환하는 패턴
> 

- factory method는 객체를 생성 반환하는 매소드이다.
- 하위 클래스에서 factory method를 오버라이딩해서 객체를 반환하게 하는 것이 factory method pattern이다.
- 의존 역전 원칙(DIP)를 활용한 설계 패턴


```java
// 추상 클래스
public abstract class Animal{
  // abstract factory method
  abstract AnimalToy getToy();
}

public abstract class AnimalToy{
  // factory method가 생성할 객체의 상위 클래스
  abstract void identify();
}
```

```java
// 구체 클래스
public class Dog extends Animal{
  // abstract factory method 오버라이드
  @Override
  AnimalToy getToy(){
    return new DogToy();
  }
}

public class DogToy extends AnimalToy{
  // factory method가 생성할 객체
  public void identify(){
    System.out.println("boltBall!");
  }
}

public class Cat extends Animal{
  // abstract factory method 오버라이드
  @Override
  AnimalToy getToy(){
    return new CatToy();
  }
}

public class CatToy extends AnimalToy{
  // factory method가 생성할 객체
  public void identify(){
    System.out.println("kittyTower");
  }
}
```

```java
public class Driver{
  public static void main(String[] args){
    // factory method가 보유한 객체 생성
    Animal bolt = new Dog();
    Animal kitty = new Cat();

    // factory method가 반환하는 객체
    AnimalToy boltBall = bolt.getToy();
    AnimalToy kittyTower = kitty.getToy();

    // factory method가 반환한 객체를 사용
    boltBall.identify();
    kittyTower.identify();
  }
}
```


<img width="490" alt="Untitled (3)" src="https://user-images.githubusercontent.com/103729286/165498214-8ea23826-b031-41be-a1fa-56eede6dfa7b.png">




### Strategy Pattern

> 클라이언트가 전략을 생성해 전략을 실행할 컨텍스트에 주입하는 패턴
> 

< 전략 패턴을 구성하는 3요소 >

- 전략 메소드를 가진 **객체**
- 전략 객체를 사용하는 **컨텍스트** (전략 객체의 소비자)
- 전략 객체를 생성해 컨텍스트에 주입하는 **클라이언트** ( 제 3자, 전략 객체의 공급자)

- 같은 문제의 해결책으로 상속을 이용하는 Template Method Pattern과 객체 주입을 통한 Strategy Pattern 중에서 선택 적용할 수 있다. 단일 상속만이 가능한 Java에서는 Strategy Pattern이 더 많이 활용된다.
- 개방 폐쇄 원칙(OCP), 의존 역전 원칙(DIP)를 활용한 설계 패턴

```java
// 다양한 전략을 공통된 방식으로 사용하기 위해 인터페이스를 정의한다. 
public interface Strategy{
  public abstract void runStrategy();
}
```

```java
// 다양한 전략 
public class StrategyGun implements Strategy{
  @Override
  public void runStrategy(){
    System.out.println("총 탕탕!");
  }
}

public class StrategySword implements Strategy{
  @Override
  public void runStrategy(){
    System.out.println("검 휙휙!");
  }
}
```

```java
// 전략을 사용할 컨텍스트 
public class Soldier{
  void runContext(Strategy strategy){
    System.out.println("전투 시작");
    strategy.runStrategy();
    System.out.println("전투 종료");
  }
}
```

```java
// 전략을 생성해서 컨텍스트에 주입할 클라이언트
public class Client{
  public static void main(String[] args){
    Strategy strategy = null;
    Soldier rambo = new Soldier();
    
    // 총을 람보에게 전달해서 전투를 수행하게함
    strategy = new StrategyGun();
    rambo.runContext(strategy);

    // 활을 람보에게 전달해서 전투를 수행하게함
    strategy = new StrategySword();
    rambo.runContext(strategy);
  }
}
```

<img width="515" alt="Untitled (4)" src="https://user-images.githubusercontent.com/103729286/165498332-14a2a19c-1f43-41be-b7cf-df1a56c4b013.png">




### Template Callback Pattern

> 전략을 익명 내부 클래스로 구현한 전략 패턴
> 

- Strategy Pattern의 변형으로 DI에서 사용하는 특별한 형태의 전략 패턴이다.
- 개방 폐쇄 원칙(OCP), 의존 역전 원칙(DIP)를 활용한 설계 패턴

```java
// 다양한 전략을 공통된 방식으로 사용하기 위해 인터페이스를 정의한다.
public interface Strategy{
  public abstract void runStrategy();
}

// 전략을 사용할 컨텍스트
public class Soldier{
  void runContext(Strategy strategy){
    System.out.println("전투 시작");
    strategy.runStrategy();
    System.out.println("전투 종료");
  }
  
  // 전략을 생성하는 코드가 컨택스트로 들어옴 (refactoring된 탬플릿 콜백 패턴)
  private Strategy executeWeapon(final String weaponSound){
    return new Strategy() {
      @Override 
      public void runStrategy() {
        System.out.println(weaponSound);
      }
    }
  }
}

// 익명 내부 전략을 사용해서 컨텍스트에 주입할 클라이언트
public class Client{
  public static void main(String[] args){
    Soldier rambo = new Soldier();

    // 익명 내부 전략을 사용해서 컨텍스트 주입 
    rambo.runContext(new Strategy() {
      @Override 
      public void runStrategy() {
        System.out.println("총 탕탕!");
      }
    });
    
    // refactoring된 탬플릿 콜백 패턴
    rambo.runContext("활 휙휙!");
  }
}
```
