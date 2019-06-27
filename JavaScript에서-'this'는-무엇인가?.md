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
````
class Car {
  constructor(make) {
    this.make = make;
  }

  getMake() {
    return this.make;
  }
}
const myCar = new Car("BMW");
myCar.getMake(); // "BMW"
````

## Object Method / Static Method
함수가 객체의 메서드 인 경우 this는 객체를 참조합니다.  
````
const foo = {};
foo.bar = function () { console.log(this); };
foo.bar(); // foo
````

모든 함수는 JavaScript의 객체이기 때문에 this는 JavaScript안에서 클래스의 정적 메서드와 같습니다.
````
class Car {
  constructor(make) {
    this.make = make;
  }

  static convertKmToMiles(km) {
    return km * this.kmToMilesConversionRate;
  }
}
Car.kmToMilesConversionRate = 0.621371;
Car.convertKmToMiles(42); // 26.097582
````

또 다른 예는 window.setTimeout을 실행할 때입니다. setTimeout 내부에서 this는 window 객체를 참조합니다.  
````
const foo = {};
foo.bar = function () {
  window.setTimeout(function () {
    console.log(this);
  }, 0);
};
foo.bar(); // window
````

## Binding this
#### .bind 
this가 함수 내에서 항상 특정 객체를 참조하도록 하려면 함수의 bind 메소드를 사용할 수 있습니다.  
````
const foo = function () { console.log(this) };
const boundFoo = foo.bind("always me!");

foo(); // window
boundFoo(); // "always me!"

const a = {
  b: foo,
  boundB: boundFoo
}
a.b(); // a
a.boundB(); // "always me"

bar = function () {};
bar.prototype.x = foo;
bar.prototype.boundX = boundFoo;

const myBar = new bar();

myBar.x(); // myBar
myBar.boundX(); // "always me"
````

#### .call and .apply
Function.prototype.bind는 객체에 바인딩 된 새 함수를 반환하지만 함수를 호출하지는 않습니다.  
Function.prototype.call 및 Function.prototype.apply는 즉시 this값을 전달하면서 함수를 호출합니다.  

.call과 .apply의 유일한 차이점은 .call이 인수를 직접 나열하는 반면 .apply는 인수를 배열로 그룹화 할 것으로 기대한다는 것입니다.  

````
const foo = function (x, y) {
  console.log(this);
  console.log(x);
  console.log(y);
};

foo(); // window, undefined, undefined
foo.call(); // window, undefined, undefined
foo.call("Yo!"); // "Yo!", undefined, undefined
foo.call("Yo!", 1, 2); // "Yo!", 1, 2
foo.apply("Yo!", [1, 2]); // "Yo!", 1, 2
````

## ES6 / 7
ES6에는 this가 바인딩 되는 방식에 영향을 주는 두 가지 새로운 구문이 있습니다.  

* Arrow functions
* Reflect.apply() 

ES7에는 새로운 :: bind 연산자가 있습니다.  

### Arrow functions
화살표 함수는 자동으로 함수 본문 내부의 컨텍스트를 바인딩합니다.  
````
function Car (make) {
  window.setTimeout(function () {
    console.log(this); // window
  }, 0);
  window.setTimeout(() => {
    console.log(this); // Car
  }, 2000)
}

new Car();
// window
// Car
````  

### Reflect.apply()
Reflect.apply는 Function.prototype.apply를 작성하는 또 다른 방법입니다.  
````
const foo = function (x, y) {
  console.log(this);
  console.log(x);
  console.log(y);
};

foo.apply('a', ['b', 'c']); // 'a', 'b', 'c'
Reflect.apply(foo, 'a', ['b', 'c']) // 'a', 'b', 'c'
````

그것들은 몇 가지 주의사항과 동등합니다.  
가장 관련성이 높은 것은 Reflect.apply가 Function.prototype.apply와 항상 같지만 객체의 apply 메소드를  
오버라이드 할 수 있다는 것입니다.  

````
foo.apply = function(x, y) { console.log(y.concat([x])) };
foo.apply('a', ['b', 'c']); // ['b', 'c', 'a']
Reflect.apply(foo, 'a', ['b', 'c']) // 'a', 'b', 'c'
````

### :: bind operator
:: bind 연산자는 객체의 메서드를 객체 자체에 바인딩하는 제안 된 연산자입니다.  
````
console.log(this); // window
console.log(this).bind(console); // console
::console.log(this); // console
````
