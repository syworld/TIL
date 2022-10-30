# Inside JavaScript

## chp1 기본 개요

자바스크립트의 핵심 개념

boolean, number, string, null, undefined 제외  나머지는 모두 객체

자바스크립트에서는 함수도 객체로 취급 (First Class Object)

<프로토타입>

모든 객체는 숨겨진 link인 prototype을 가진다. 

이 링크는 해당 객체를 생성한 생성자의 프로로타입 객체를 가리킨다. 

<실행 컨텍스트 , 클로저>

JS는 자신만의 실행 컨텍스트를 만들고 그 안에서 실행이 이루어진다. 이 실행 컨텍스트는 자신만의 scope를 가지고 이 과정에서 클로저를 구현할 수 있다. 



# chp2 개발 환경

WebStorm 설치 



# chp3 데이터 타입과 연산자
<img width="663" alt="image" src="https://user-images.githubusercontent.com/103729286/198880442-22038ac5-536f-4dff-8bc9-f2963671004c.png">


JS는 느슨한 타입 체크 언어 

변수를 선언할 때 타입을 미리 정하지 않고 var라는 하나의 키워드로만 변수를 선언한다.



### Primitive Type

<숫자>

JS는 하나의 숫자형만 존재한다. JS는 모든 숫자를 64비트 부동 소수점 형태로 저장하기 때문이다. 

(C언어의 double 타입과 유사함)

JS는 모든 숫자를 실수로 처리하므로 나눗셈 연산을 할 때 주의해야함. 

`Math.floor(5/2); // 2`


<문자열>

single quote 또는 double quote 로 생성 

C의 char형과 같이 문자 하나만을 별도로 나타내는 데이터 타입은 존재하지 않음.

JS에서 한 번 생성된 문자열은 수정이 불가능하다.


<불린>

true

false

<null, undefined>

JS에서 기본적으로 값이 할당되지 않은 변수는 undefined 타입이며, undefined 타입의 변수는 변수 자체의 값도 undefined이다. 

undefined = 타입이자 값. 

typeof null 은 object 



### Reference Type

JS에서는 **key:value 형태의 프로퍼티**들을 저장하는 컨테이너를 객체라고 정의한다. 

JS에서 기본 타입은 하나의 값만, 참조 타입인 객체는 여러 개의 프로퍼티들을 포함할 수 있다. 

이러한 프로퍼티의 성질에 따라 객체의 프로퍼티는 함수로 포함할 수 있으며, JS에서는 이러한 프로퍼티를 메서드라고 부른다.


<객체 생성> 

JS에서는 클래스라는 개념이 없고, 객체 리터럴이나 생성자 함수 등 별도의 생성 방식이 존재한다. 

- Object() 객체 생성자 함수
    
    ```jsx
    var foo = new Obejct();
    
    foo.name = 'foo'; // foo object property 
    ```
    
- 객체 리터럴
    
    ```jsx
    var foo = {
    	name : 'foo'
    };
    ```
    
- 생성자 함수
    
    JS에서는 함수를 통해서도 객체를 생성할 수 있다.
    

<프로퍼티>

프로퍼티가 없으면 undefined 

프로퍼티 이름을 문자열 형태로 만들지 않으면 모든 JS 객체에서 호출가능한 toString() 메서드를 자동으로 호출해서 이를 문자열로 바꾸려는 시도를 한다. 

‘[]’ or ‘.’ 사용

NaN → 수치연산을 해서 정상적인 값을 얻지 못할 때 출력되는 값. 

삭제 

```
delete foo.name;
```



### 참조 타입의 특성

객체의 모든 연산이 실제 값이 아닌 참조값으로 처리된다. 

동등 연산자 == : 참조값이 같아야 true 

기본 타입 - call by value 

참조 타입 - call by reference 



### 프로토타입

JS의 **모든 객체는 자신의 부모 역할을 하는 객체 와 연결**되어 있다.

이것은 마치 객체지향의 상속 개념과 같이 부모 객체의 프로퍼티를 마치 자신의 것처럼 쓸 수 있는 것 같은 특징 

부모 객체 = 프로토타입 객체 = 프로토타입 

‘__proto__’ 프로퍼티 = 프로토타입 객체 

ECMAScript 명세서에 JS의 모든 객체는 자신의 프로토타입을 가리키는  `[[Prototype]]` 라는 숨겨진 프로퍼티를 가진다. ( 크롬에서는 ‘__proto__’ 프로퍼티 ) 

모든 객체의 프로토타입은 자바스크립트의 룰에 따라 객체를 생성할 때 결정된다. 

객체 리터럴 방식으로 생성된 객체의 경우 Object.prototype 객체가 프로토타입 객체가 된다. 



### 배열

크기를 지정하지 않아도 되며, 어떤 위치에 어느 타입의 데이터를 저장해도 에러가 나지 않는다. 

<배열 리터럴>

‘[]’ 사용

`var emptyArr = [];` 

emptyArr[0] // undefined이다. 배열 또한 JS 객체이기 때문에 객체에서도 포함하지 않은 객체의 프로퍼티에 접근한 경우 undefined가 출력된다. 

값이 할당되지 않은 인덱스의 요소는 undefined 값을 가진다. 

length 프로퍼티는 배열 내에 가장 큰 인덱스 + 1. 실제 메모리는 length 크기처럼 할당되지는 않는다. 

배열 메서드 push() 등은 length 프로퍼티 기반으로 동작하고 있다. 

배열의 경우 Array.prototype 객체가 부모 객체인 프로타입이 된다. Array.prototype 객체의 프로토타입은 Object.prototype 객체가 된다. 

<img width="673" alt="image" src="https://user-images.githubusercontent.com/103729286/198880527-cea21763-f75a-4a53-b839-e8f3558b0236.png">

<img width="673" alt="image" src="https://user-images.githubusercontent.com/103729286/198880537-77fb0700-a163-4d68-bcbb-4c95887d275f.png">



```
var arr = ['q', '1'];

arr.color = 'blue';
arr.length // 2 
배열의 length 프로퍼티는 배열 원소의 가장 큰 인덱스가 변했을 경우만 변경된다. 

delete arr[1]; 
console.log(arr); // [ 'q', undefined , color: 'blue' ] ;
```

<img width="664" alt="image" src="https://user-images.githubusercontent.com/103729286/198880546-faf8f296-ac7c-4bef-8447-3ea71bf883c5.png">


delete 연산자로 배열 요소 삭제 후에도 배열의 length 값은 변하지 않는다. 

undefined로 요소 값을 설정할 뿐 원소 자체를 삭제하지는 않는다. → splice() 메소드 사용해서 배열의 요소들을 완전히 삭제한다. 


<Array() 생성자 함수>

배열 리터럴도 결국 JS 기본 제공 Array() 생성자 함수로 배열을 생성하는 과정이다.

```
var foo = new Array(3); // [undefined, undefined, undefined]
var bar = new Array(1, 2, 3); // [1, 2, 3]
```


<유사 배열 객체> 

apply() 메소드 사용 

`Array.prototype.push.apply(object, [’newValue’]);`  



### 기보 타입과 표준 메소드

기본값은 메서드 처리 순간에 객체로 변환된 다음 각 타입별 표준 메서드를 호출하고, 호출이 끝나면 다시 기본값으로 복귀된다.

`“test”.charAt(2);`

`val num = 0.5;`

`num.toExponential(1);`



### 연산자

== 연산자: 피연산자의 타입이 다를 경우 타입 변환을 거친 다음 비교

=== 연산자: 타입변환 X 

!! 연산자: 피연산자를 boolean 값으로 변환

```
!!0 // false
!!1 // true
!!'hello' // true 
!!''// false
!!null //false 
!!undefined //false 
!!{} // true 
```
