일반 {...} 구문을 사용하면 하나의 객체를 만들 수 있습니다.  
그러나 종종 우리는 여러 사용자, 메뉴 항목 등과 같이 많은 유사한 객체를 만들어야합니다.  
이는 생성자 함수와 "new"연산자를 사용하여 수행 할 수 있습니다.  

## Constructor function
생성자 함수는 기술적으로 정규 함수입니다. 하지만 두 가지 규칙이 있습니다.  
1. 이름은 대문자로 시작합니다.  
2. 그들은 "new" 연산자로만 실행되어야합니다.  
예를 들면 :
````
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User("Jack");

alert(user.name); // Jack
alert(user.isAdmin); // false
````

새 사용자 (...)로 함수가 실행되면 다음 단계가 수행됩니다.  
1. 새로운 빈 객체가 만들어지고 this에 할당됩니다.  
2. 함수 본문이 실행됩니다. 일반적으로 this를 수정하고 새로운 속성을 추가합니다.
3. this의 값이 리턴됩니다.

즉, 새로운 사용자 (...)는 다음과 같은 작업을 수행합니다.  
````
function User(name) {
  // this = {};  (implicitly)

  // add properties to this
  this.name = name;
  this.isAdmin = false;

  // return this;  (implicitly)
}
````

새로운 사용자 ( "Jack")의 결과는 다음과 같은 객체입니다.
````
let user = {
  name: "Jack",
  isAdmin: false
};
````

이제 다른 사용자를 만들려면 새 사용자 ( "Ann"), 새 사용자 ( "Alice") 등을 호출 할 수 있습니다.  
매번 리터럴을 사용하는 것보다 훨씬 짧으며 읽기 쉽습니다.  

재사용 가능한 객체 생성 코드를 구현하는 것이 생성자의 주요 목적입니다.  

기술적으로 모든 함수를 생성자로 사용할 수 있습니다.   
즉 : 모든 함수는 new로 실행될 수 있으며 위의 알고리즘을 실행합니다.   
"대문자 우선"은 기능이 새로운 기능으로 실행된다는 것을 분명히하기위한 공통된 합의입니다.  

````
ⓘ new function() { … }
하나의 복합 객체 생성에 관한 많은 코드 라인이 있다면 다음과 같이 생성자 함수로 그 객체를 래핑 할 수 있습니다.  

let user = new function() {
  this.name = "John";
  this.isAdmin = false;

  // ...other code for user creation
  // maybe complex logic and statements
  // local variables etc
};

생성자는 아무 곳에서나 저장되고 방금 생성되고 호출되기 때문에 다시 호출 할 수 없습니다.  
그래서 이 트릭은 미래의 재사용없이 단일 객체를 구성하는 코드를 캡슐화하는 것을 목표로합니다.

````

## Constructor mode test: new.target
````
ⓘ 고급기능

이 섹션의 구문은 거의 사용되지 않으며 모든 것을 알고 싶지 않으면 건너 뜁니다.  
````
함수 내에서 new.target 속성을 사용하여 new 또는 new가 호출되었는지 여부를 확인할 수 있습니다.
일반 호출의 경우 비어 있으며 new를 사용하여 호출 된 경우 함수와 같습니다.  
````
function User() {
  alert(new.target);
}

// without "new":
User(); // undefined

// with "new":
new User(); // function User { ... }
````

이 함수를 사용하여 함수가 "생성자 모드"에서 또는 "일반 모드에서" 사용하지 않고 새 함수로 호출되었는지 여부를 알 수 있습니다.  
우리는 다음과 같이 똑같이하기 위해 새롭고 규칙적인 호출을 할 수 있습니다 :
````
function User(name) {
  if (!new.target) { // if you run me without new
    return new User(name); // ...I will add new for you
  }

  this.name = name;
}

let john = User("John"); // redirects call to new User
alert(john.name); // John
````

이 접근법은 구문을보다 융통성 있게 하기 위해 라이브러리에서 때때로 사용됩니다.  
사람들이 새로운 기능의 유무에 관계없이 기능을 호출 할 수 있도록하고, 여전히 작동합니다.  

새로운 것을 생략하면 어떤 일이 벌어지는 지 알 수 없기 때문에 모든 곳에서 사용하는 것은 좋지 않을 것입니다.  
새로운 것을 통해 우리는 새로운 대상이 만들어지고 있음을 모두 알고 있습니다.  

## Return from constructors
일반적으로 생성자에는 return 문이 없습니다. 그들의 임무는 이것에 필요한 모든 것을 쓰는 것이고, 자동적으로 그 결과가됩니다.  
그러나 return 문이 있으면 규칙은 간단합니다.  
* object와 함께 return이 호출되면, 대신 this가 반환됩니다.  
* 원시적인 것이 함께 return이 되면 이것은 무시됩니다.  

즉, 객체를 반환하면 해당 객체가 반환되고 다른 모든 경우 반환됩니다.  
예를 들어, 여기에서 객체를 반환하여 반환 값을 재정의합니다.  

````
function BigUser() {

  this.name = "John";

  return { name: "Godzilla" };  // <-- returns an object
}

alert( new BigUser().name );  // Godzilla, got that object ^^
````
빈 반환 값이있는 예가 있습니다 (또는 그 뒤에 기본 요소를 배치 할 수도 있고 중요하지 않을 수도 있습니다).  
````
function SmallUser() {

  this.name = "John";

  return; // finishes the execution, returns this

  // ...

}

alert( new SmallUser().name );  // John
````  
보통 생성자에는 return 문이 없습니다. 여기서 우리는 완벽을 기하기 위해 주로 객체를 반환하는 특수한 동작에 대해 언급합니다.  

````
ⓘ 괄호 생략
그런데 인수가없는 경우 new 뒤에 괄호를 생략 할 수 있습니다.

let user = new User; // <-- no parentheses
// same as
let user = new User();

여기에 괄호를 생략하는 것은 "좋은 스타일"로 간주되지 않지만 구문에 의해 허용됩니다.
````

## Methods in constructor
생성자 함수를 사용하여 객체를 생성하면 많은 유연성을 얻을 수 있습니다.  
생성자 함수는 객체를 생성하는 방법과 객체를 넣는 방법을 정의하는 매개 변수를 가질 수 있습니다.  

물론 우리는 this 속성뿐만 아니라 메서드도 추가 할 수 있습니다.

예를 들어, 아래의 새로운 User (name)는 주어진 이름과 메소드 sayHi로 객체를 생성합니다.
````
function User(name) {
  this.name = name;

  this.sayHi = function() {
    alert( "My name is: " + this.name );
  };
}

let john = new User("John");

john.sayHi(); // My name is: John

/*
john = {
   name: "John",
   sayHi: function() { ... }
}
*/
````

## 개요
* 생성자 함수 또는 간단히 말해서 생성자는 일반 함수이지만 대문자를 먼저 사용하는 것이 일반적입니다.  
* 생성자 함수는 new를 사용하여 호출해야합니다. 이러한 호출은 시작시 빈 this를 생성하고 끝에 채워진 것을 반환 함을 의미합니다.  

생성자 함수를 사용하여 여러 유사한 객체를 만들 수 있습니다.  
JavaScript는 많은 내장 언어 객체에 대해 생성자 함수를 제공합니다. 예를 들어 날짜는 날짜, 집합은 세트 및 기타 연구 할 계획입니다.  

````
ⓘ Objects, we’ll be back!

이 장에서는 객체와 생성자에 대한 기초만 다룹니다. 다음 장에서 데이터 유형 및 기능에 대해 더 자세히 배우기 위해 필수적입니다.  
그 사실을 알게 된 후에는 프로토 타입, 상속 및 클래스 장에서 객체로 돌아가 심층적 인 내용을 다룹니다.  

````


