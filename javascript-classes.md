
# Javascript Classes — Under The Hood 

> 원문: [Javascript Classes — Under The Hood](https://medium.com/tech-tajawal/javascript-classes-under-the-hood-6b26d2667677)

자바스크립트 클래스는 기존의 프로토타입 기반 상속 및 생성자 함수를 구현한 편의문법이다. JS 클래스의 이면에 있는 아이디어를 이해하기 위해서는 생성자 함숴, 프로토타입 및 기타 관련 개념을 이해할 필요가 있다.

## 1. 생성자 함수 (constructor function)

Javascript는 모든 것이 단지 함수인 함수형 프로그래밍 언어이기 때문에,  클래스를 만들기 위해 생성자 함수를 사용하는 경우, 생성자 함수가 어떻게 작동하는지 알아보자.

```js
function Vehicle(make, model, color) {  
  this.make = make,  
  this.model = model,  
  this.color = color,  
  this.getName = function () {  
    return this.make + " " + this.model
  }
}
```

위의 함수는 Java의 클래스와 거의 유사한 기능을 제공한다. 해당 함수를 이용하여 `Vehicle` 유형의 객체를 생성하기 위해 다음과 같이 한다.:

```js
let car = new Vehicle("Toyota", "Corolla", "Black")
let car2= new Vehicle("Honda", "Civic", "White")
```

이건 완벽하다. 그렇지? 이제, 단지 한줄의 코드를 쓰는 것 만으로도 원하는 만큼 많은 종류의 `Vehicle`을 만들 수 있다.

하지만, 이 방법에는 몇가지 문제가 존재한다.

 우리가 `new Vehicle()`  쓰게되면, Javascript 엔진은 두 `Vehicle` 객체의 각각에 대한 생성자 함수의 복사본을 만든다. 각 속성과 메서드는 새  `Vehicle` 인스턴스로 복사된다. 근데 문제가 뭘까요?
 
여기서 문제는 생성자 함수의 멤버 함수(메서드)가 모든 객체에 나타나는 것을 원치 않는다는 것이다.. 또 다른 문제는 다음과 같은 기존 객체에 새로운 속성이나 메서드를 추가할 수 없다는 것이다. :

```js
car2.year = "2012"
```

이 year 속성을 추가하려면 해당 속성을 생성자 함수 자체에 추가해야한다.

```js
function Vehicle(make, model, color, year) {
  this.make = make
  this.model = model
  this.color = color
  this.year = year
  this.getName = function() {
    return this.make + " " + this.model;
  }
}
```

## 2. Prototype

Javascript에서 새로운 함수가 생성될 때 마다, Javascript 엔진은 기본적으로 `prototype` 속성을 추가한다. `prototype` 속성은 객체이며 이를 "프로토타입 객체"라고 부른다. 기본적으로 이 프로토타입 객체는 함수를 가르키는 `constructor` 속성을 가지고 있으며, 또 다른 속성인 `__proto__`는 객체이다.  아래 예시를 보자.

[[/resource/yongkwan/14/01.png]]

`__proto__` 속성은 "dunder proto"라고  불리며, 생성자 함수의 프로토타입 속성을 가리키고 있다.

> dunder는 두개의 under bar로 묶인 변수 이름을 dunder properties라고 부르는 python에서 유래한다.

생성자 함수의 새 인스턴스가 생성될 때마다 `__proto__` 속성은 은 다른 속성 및 메소드와 함께 인스턴스에 복사된다.

[[/resource/yongkwan/14/02.png]]

이제 이 프로토타입 객체를 사용하여 아래와 같이 새로운 속성 및 메서드를 추가할 수 있으며, 생성자 함수의 모든 인스턴스에서 사용할 수 있다.

```js
car.prototype.year = "2016";
```

프로토타입은 멋지지만, 프로토타입 접근법을 사용하면서 주의해야 할 점이 몇가지 있다. 프로토타입 속성과 메소드는 모든 생성자 함수의 인스턴스간에 공유되지만, 생성자 함수의 인스턴스 중 하나라도 원시 속성을 변경하면, 그 인스턴스에만 적용되고 다른 인스턴스에는 반영되지 않는다.

[[/resource/yongkwan/14/03.png]]

또 다른 한가지는 배열과 같은 참조 타입 속성이 모든 인스턴스간에 항상 공유 된다는 것이다. 생성자 함수의 한 인스턴스가 참조 타입 속성을 수정하면 모든 인스턴스가 변경된다.

[[/resource/yongkwan/14/04.png]]

프로토타입에 관한 매우 흥미롭고 자세한 기사는 [여기](https://hackernoon.com/prototypes-in-javascript-5bba2990e04b)에서 확인할 수 있다.

## 3. 클래스

생성자 함수와 프로타입을 이해했으면, 이제 클래스를 이해하기 쉽다. 왜냐하면 Javascript 클래스는 프로토타입을 이용하여 생성자 함수를 쓰는 새로운 방법에 불과하기 때문이다. 아래의 예를 보자:

```js
class Vehicle {
  constructor(make, model, color) {
    this.make = make
    this.model = model
    this.color = color
  }
  getName() {
    return this.make + " " + this.model
  }
}
```

`Vehicle` 클래스의 새로운 인스턴스를 생성하기 위해 아래와 같이 해보자:

```js
let car = new Vehicle("Toyota", "Corolla", "Black");
```

이 예제를 처음에 생성자 함수를 설명하면서 했던 것과 비교를 해 보면 아주 비슷하다.

즉 위에 작성한 코드는 아래의 코드와 같다.

```js
function Vehicle(make, model, color) {
  this.make = make
  this.model = model
  this.color = color
}

Vehicle.prototype.getName = function() {
  return this.make + " " + this.model
}

let car = new Vehicle("Toyota", "Corolla", "Black");
```

따라서 클래스가 생성자 함수를 수행하는 새로운 방법이라는 것을 증명한다. 하지만 실제 클래스처럼 만들기위해 새로이 도입 된 몇가지의 새로운 것들과 규칙이 있다.

1. 클래스의 `constructor`는 동작하기 위해 `new` 키워드가 필요하다. constructor는 우리가 아래처럼 호출할 경우에만 동작하는 것을 의미한다.

```js
let car = new Vehicle("Toyota", "Corolla", "Black");
```

그러나 생성자 함수에선 `new` 키워드 없이 함수처럼 사용할 수 있다.

[[/resource/yongkwan/14/05.png]]

클래스 변수는 위와 같이 인스턴스화 하려고 하면 다음과 같이 에러를 발생한다.

[[/resource/yongkwan/14/06.png]]

2.  클래스 메소드는 열거가 불가능하다.(non-enumerable) Javascript에서 객체의 각 속성에는 `enumerable` 플래그가 존재한다. 이 플래그는 해당 속성에서 수행할 일부 작업에 대한 가용성을 정의한다. 클래스에서는 `prototype`에 정의된 모든 메소드에 대해 이 플래그를 `false`로 설정한다.

3. 클래스에 `constructor`를 추가하지 않으면, 다음과 같이 기본으로 빈 `constructor`가 자동적으로 추가된다. 

```js
constructor() {}
```

4. 클래스 안의 코드는 항상 strict 모드이다. 이것은 오류가 없는 코드를 작성하거나, 잘못된 입력 또는 코드 작성 중에 작성된 구문 오류, 다른 곳에서 참조 된 코드를 실수로 제거하는 것을 방지해 준다.

5. 클래스 선언은 `호이스팅` 되지 않는다. Javascript에서 호이스팅은 모든 선언이 현재 스코프 위에 자동으로 이동하는 메커니즘이다. 호이스팅은 변수 또는 함수가 선언되기 전에 사용할 수 있게 한다.

그래서 클래스 선언은 호이스팅 되지 않기에, 클래스를 선언하기 전에는 사용할 수 없다는 것을 의미한다. 만약 사용하려고 정의되지 않은 에러를 리턴한다.

아래는 동작한다.

[[/resource/yongkwan/14/07.png]]

하지만 아래는 동작하지 않는다.

[[/resource/yongkwan/14/08.png]]

6. 클래스는 생성자 함수나 객체 리터럴과 같은 속성에 접근해서 값을 할당하는 것을 허용하지 않는다. 오직 함수나 getter / setter로만 가능하다. 따라서 클래스엔  `property:value`  직접 할당 값이 없다.

## 4. 클래스의 특징

### 1. Constructor

생성자는 클래스 선언에서 클래스 그 자신을 나타내는 특별한 함수이다. 클래스 인스턴스를 새로 만들면 생성자가 자동으로 호출된다.

```js
let car = new Vehicle("Honda", "Accord", "Purple");
```

생성자는 `super` 키워드를 사용하여 부모 생성자를 호출 할 수 있습니다.

> 클래스는 오직 1개의 생성자만 가질 수 있다.

### 2. Static Methods

정적 메소드는 프로토타입에 정의 된 클래스의 다른 메소드와 달리 프로토타입이 아닌 클래스 자체의 함수이다.

정적 메서드는 `static` 키워드를 사용하여 선언되며, 주로 유틸리티 함수를 만드는 데 사용된다. **클래스의 인스턴스를 만들지 않고 호출 할수 있다.** 아래 예를 보자.

```js
class Vehicle {
  constructor(make, model, color) {
    this.make = make
    this.model = model
    this.color = color
  }
  getName() { 
    return this.make + " " + this.model
  } 
  static getColor(v) { 
    return v.color
  }
}

let car = new Vehicle("Honda", "Accord", "Purple");

Vehicle.getColor(car); // "purple"
```

클래스 인스턴스에서 정적 메소드를 호출할 수 없다는 점을 기억하라.

### 3. Getters/Setters

클래스에선 아래처럼 속성의 값을 가져오거나, 설정하기 위한 getters/setters를 가질 수 있다.

```js
class Vehicle {
  constructor(model) {
    this.model = model
  }
  get model() {
    return this._model
  }
  set model(value) {
    this._model = value
  }
}
```

내부적으론, getters/setters는 클래스의 `prototype`에 정의되어 있다.

### 4. 상속

Javascript의 클래스를 상속하여 구현할 수 있다. `extends`를 사용하면 클래스의 자식 클래스를 만들 수 있다.

예를 들어 보자.

```js
class Vehicle {
  constructor(make, model, color) {
    this.make = make
    this.model = model
    this.color = color
  }
  getName() {
    return this.make + " " + this.model
  }
} 
class Car extends Vehicle{ 
  getName(){
    return this.make + " " + this.model +" in child class."
  }
} 

let car = new Car("Honda", "Accord", "Purple");

car.getName(); // "Honda Accord in child class."
```

`getName()`  메소드를 호출하면 자식 클래스에 있는 메소드가 호출 된 것을 볼 수 있다.

때로는 부모 클래스의 메소드를 호출해야한다. 자식 클래스의 메소드내에서 부모 클래스의 메소드를 호출하기 위해 `super` 키워드를 사용한다.

`getName()` 메소드를 아래와 같이 바꿔 보자.

```js
class Car extends Vehicle{
  getName(){
    return super.getName() +" - called base class function from child class."
  }
}
```

이제 `getName()`을 인스턴스에서 호출하면 결과가 아래와 같이 반환된다.

[[/resource/yongkwan/14/09.png]]

결론적으로 Javascript의 클래스 내부의 개념을 설명하려고 했으며, 결국에는 클래스의 몇가지의 특징에 대해 배웠다.

이 주제와 관련하여 더 자세한 것을 보고 싶으면 따라오는 멋진 기사들을 읽어라. 이뿐만 아니라, 인터넷에는 방대한 다른 기사들이 넘쳐난다.

### References:

1.  [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
2.  [http://2ality.com/2015/02/es6-classes-final.html](http://2ality.com/2015/02/es6-classes-final.html)
3.  [https://javascript.info/class](https://javascript.info/class)
4.  [https://thejsguy.com/tutorials/javascript-constructor-functions-and-classes](https://thejsguy.com/tutorials/javascript-constructor-functions-and-classes)
5.  [https://www.phpied.com/3-ways-to-define-a-javascript-class/](https://www.phpied.com/3-ways-to-define-a-javascript-class/)

