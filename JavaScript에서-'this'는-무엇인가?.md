JavaScript에서 this가 의미하는 것은 종종 이해하기 까다로운 일 중 하나입니다.  
즉, 현재 실행 컨텍스트를 참조합니다.  

## Global context
어떠한 함수나 클래스(실제로는 단지 함수인) 밖에 있을 때 this는 웹 브라우저 내부의 윈도우인 전역 실행 컨텍스트를 참조합니다.  
````
this; // window
this.foo = "bar";
window.foo; // "bar"
````
* Node.js에서 전역 컨텍스트의 이것은 기본적으로 빈 객체 인 내보내기 객체를 참조합니다.
````
this; // {}
````

## Function Context
함수 내부에서 this 값은 함수가 호출되는 방식에 따라 결정됩니다.  
예 : 
````
const bar = function () { console.log(this) };
const foo = {};
foo.bar = bar;

bar(); // window
foo.bar(); // bar
````

## Function
함수가 전역 컨텍스트 내에서 호출되면 this는 전역 실행 컨텍스트를 참조합니다.  
````
const bar = function () { console.log(this) };
bar(); // window
````
함수 블록은 본문의 실행 컨텍스트에 영향을 미치지 않습니다.
````
const foo = function () { bar(); }
foo(); // window
````

### Strict Mode
함수 내에서 this 값은 엄격 모드가 켜져 있는지 여부에 따라 결정됩니다.  
strict 모드가 없으면 전역 개체가 됩니다. 엄격 모드에서는 정의 되지 않습니다.  
````
const foo = function () { console.log(this) };
const bar = function () { 'use strict'; console.log(this); };

foo(); // window
bar(); // undefined
````

## Function Constructor
함수는 new 키워드와 함께 사용할 때 함수 생성자로 사용할 수 있습니다.  
함수 생성자는 새로운 객체를 만드는 템플릿 역할을 하며 ECMAScript2015(ES6)의 클래스로 설탕화(sugarized?)됩니다.  

함수가 함수 생성자로 사용될 때 this는 생성되는 객체를 참조합니다.
````
function Car(make) {
  this.make = make;
}

const myCar = new Car("BMW");
myCar.make; // "BMW"
````

함수 생성자는 ES6에서 클래스로 설탕화(sugarized?)되므로 ES6의 클래스 생성자에서도 마찬가지입니다.
````
class Car {
  constructor(make) {
    this.make = make;
  }
}

const myCar = new Car("BMW");
myCar.make; // "BMW"
````

## Prototype
함수 생성자는 프로토 타입 객체에 정의 된 메소드를 가질 수도 있습니다. this는 이 메소드를 호출하는 객체 인스턴스를 참조합니다.

````
Car.prototype.getMake = function () {
  return this.make;
}

myCar.getMake(); // "BMW"
````  

다시, 프로토 타입 객체는 클래스의 프로토타입 메소드와 동등하므로 동일한 규칙이 적용됩니다.
