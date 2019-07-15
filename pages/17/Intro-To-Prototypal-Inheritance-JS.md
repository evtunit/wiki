# JS에서 상속

원문 - https://dev.to/danny/intro-to-prototypal-inheritance---js-9di

이 글에서 프로토타입 상속에 대해 설명을 하려 합니다.

"옵션" 전제 조건으로 "프로토 타입 소개"에 대한 이전 글을 볼수 있습니다.

[Intro To Prototype - Js](https://dev.to/danny/intro-to-prototype---js-35a)

### 프로토 타입 체인

상속에 대해서 자바스크립트는 오직 하나의 구조만을 가지고 있습니다. : 객체. 각 객체에는 프로토 타입이라고 하는 다른 객체에 대한 링크를 보유하는 private 속성( [[Prototype]]이라고 함 )이 있습니다. 프로토 타입 객체는 자체 프로토 타입을 가지고 있으며 객체가 프로토 타입으로 null로 도달할 때까지 계속 진행됩니다. 정의에 따르면 정의에 따르면 null에는 프로토 타입이 없으며이 프로토 타입 체인의 최종 링크 역할을합니다.

JS에서는 이 프로토 타입 체인 때문에 상속만 가능합니다.

예제를 봅시다. 이 예제는 프로토 타입 상속 개념을 설명합니다. first, last, age의 세가지 값을 취하는 Student 생성자 함수를 생성합니다. 

```javascript
var Student = function(first,last,age){
this.name = {
first,
last
};
this.age = age;
}
```

> 우리가 함수를 생성할 때마다 JS엔진은 우리에게 두 개의 객체를 만듭니다.

1. 하나는 함수 객체 자체이고
2. 하나는 프로토 타입 객체

우리는 프로토 타입 객체를 가리키는 ".prototype" 참조 속성을 사용하여 JS엔진이 생성한 prototype 속성을 사용할 수 있습니다. 

JS엔진에 의해 생성된 프로토 타입 객체에 "sayMessage"라는 함수를 추가할 수 있습니다.

```javascript
var Student = function(first,last,age){
this.name = {
first,
last
};
this.age = age;
}
Student.prototype.sayMessage = function(){
return "Hello!! "+this.name.first+" "+this.name.last+" your age is "+this.age+"."
}
```

Student (생성자) 함수를 사용하여 객체를 만들 수 있습니다.

```javascript
var studentOne = new Student("dharani","jayakanthan","six");
studentOne.sayMessage();
//"Hello!! dharani jayakanthan your age is six."
```

> 이 작동 방식은 "sayMessage" 함수를 호출할 때 JS엔진은 처음에 "Student" 객체를 볼 것입니다. Student 객체에 기능이 없으면 JS엔진은 프로토 타입 객체에서 "sayMessage" 함수를 찾습니다.

### 호출 함수를 사용하여 상속

이제 "Faculty"라는 또 다른 함수를 생성할 것입니다. "Student"과 동일한 매개 변수를 받지만 추가적으로 salary를 매개변수를 받습니다.

```javascript
var Faculty = function(first,last,age,salary){
Student.call(this,first,last,age);
this.salary = salary;
}
```

위의 코드가 수행하는 작업은 Faculty 생성자가 Student 생성자와 동일한 매개 변수를 사용하기를 원합니다. 따라서  "call" 함수를 통해 "Student" 생성자를 상속하고 "Faculty" 생성자가 Student들로 부터 상속하는 매개 변수를 전달합니다.

이 "call"함수는 기본적으로 다른 곳에서 정의된 함수를 호출 할 수 있지만 현재 컨텍스트에서는 허용하지 않습니다. 첫번째 매개 변수는 함수를 실행할 때 사용할 값을 지정하며, 다른 매개 변수는 호출 될 때 함수에 전달 되어야하는 매개 변수입니다.

> 형식 매개 변수가 없는 생성자를 상속 하려면 동일한 "call" 함수로 생성자를 수행할 수 있습니다. 그러나 이 참조를 "call" 참조의 매개 변수로 제공 해야 합니다.
>
> Ex : "ConstructorWithNoParameter.call(this)".

이제 "Student" 생성자를 상속 받았습니다. 그러나 "Faculty" 생성자 프로토 타입 객체의 링크를 변경하지 않았습니다. 이제 "Faculty" 생성자 프로토 타입 객체에는 생성자 자체를 가리키는 생성자 참조가 있습니다. 따라서 우리는 "Student" 생성자 프로토 타입 객체에서 생성한 "sayMessage" 함수에 액세스 하지 못합니다.

"Faculty"가 "Student" 프로토 타입 객체를 상속 받도록 하려면

- 아래의 코드가 추가적으로 필요합니다.

```javascript
Faculty.prototype = Object.create(Student.prototype);
```

student 프로토 타입을 상속 받았습니다.. 그러나 코드를 자세히 살펴보면 한가지 해야할 것이 더 있습니다.

- 위에서 한 것은 상속된 "Student" 프로토 타입 객체입니다. "Student.prototype"에는 "constructor"라는 속성이 있으며 이 속성은 "Student" 생성자 자체를 가리 킵니다.

이를 확인하기 위해 이 줄을 코드에 추가할 수 있습니다.

```javascript
Faculty.prototype.constructor;
/*
  function(first,last,age){
  this.name = {first,last};
  this.age = age;
  }
*/
(Student.prototype.constructor === Faculty.prototype.constructor) ? true : false;
//true
```

이 참조를 변경하기 위해 "Faculty.prototype.contructor"를 "Faculty" 함수로 지정합니다.

이제 false를 반환하는지 확인하면

```javascript
Faculty.prototype.contructor = Faculty;
(Faculty.prototype.constructor === Student.prototype.constructor) ? true : false;
//false
```

이제 모든 것을 정리할 수 있습니다.

```javascript
// Student Constructor
var Student = function(first,last,age){
  this.name = {first,last};
  this.age = age;
  }
Student.prototype.message = function(){
  if(this.salary){
      return this.name.first+" "+this.name.last+" "+"is "+this.age+" years old. And earns "+this.salary;
  }
  else{
      return this.name.first+" "+this.name.last+" "+"is "+this.age+" years old";
  }
  }
var studentOne = new Student("dharani","jayakanthan","20");

// Faculty Constructor
var Faculty = function(first,last,age,salary){
  Student.call(this,first,last,age);
  this.salary = salary;
}
Faculty.prototype = Object.create(Student.prototype);
Faculty.constructor = Faculty();
var facultyOne =  new Faculty("simon","peter","70","820,000");
facultyOne.message();
// simon peter is 70 years old. And earns 820,000
studentOne.message();
// dharani jayakanthan is 20 years old
```

> 이 것은 클래스를 상속하는 유일한 방법이 아닙니다. 자바스크립트에서 상속을 보다 쉽게 수행할 수 있는 ECMAScript 기능입니다. 그러나 이는 모든 브라우저에서 아직 널리지원되지 않습니다. 이 기사에서 논의한 코드는 IE9 이하 버전까지 지원됩니다.