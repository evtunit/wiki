주말에 공부를 했는데 functional 프로그래밍, higher-order function, closures 및 비동기적 JavaScript를 다루었습니다.

이 과정의 하이라이트는 JavaScript 접근 방식을 OOP(Object-Oriented Programming)로 확장하고 새로운 운영자를 뒷받침이 어떻게 설명했는가 하는 것입니다. 이제 `new operator`를 사용할 때 hood 아래에서 무슨 일이 벌어지는지 충분히 이해했습니다.

<br>

## 객체 지향 프로그래밍 

OOP(Object-Oriented Programming)는 "객체"의 개념을 기반으로 하는 프로그래밍 패러다임입니다. 데이터 및 기능(기호 및 방법)은 객체 내에서 번들로 제공됩니다.

JavaScript의 객체는 키 값 쌍의 집합입니다. 이러한 키 값 쌍은 개체의 속성입니다. 속성은 배열, 함수, 개체 자체 또는 문자열이나 정수 같은 원시 데이터 유형일 수 있습니다.

JavaScript 툴박스에 객체 작성을 위한 기술은 무엇입니까?

우리가 방금 디자인한 게임에서 사용자를 만들고 있다고 가정해 보겠습니다. 이름, 점, 점 등의 사용자 세부 정보를 저장하고 점의 증가와 같은 방법을 구현하려면 어떻게 해야 합니까? 다음은 기본 개체 생성을 위한 두 가지 옵션입니다.

<br>

### option1 - 개체 문자 표기법

```js
let user1 = {
  name: "Taylor",
  points: 5,
  increment: function() {
    user1.points++;
  }
};
```

JavaScript 개체 리터럴은 이름-값 쌍의 목록입니다. 위의 예에서는 'user1' 개체가 생성되고 관련 데이터가 개체 내에 저장됩니다.


### options2 - Object.create()

`Object.create(proto, [ propertiesObject ])`

`Object.create`메소드는 두개의 인자를 포함합니다: 

1. proto: 새로 생성된 개체의 프로토타입이어야 하는 개체입니다. 개체 또는 null이어야 합니다.
2. propertiesObject: 새 개체의 속성입니다. 이 인수는 선택 사항입니다.

기본적으로 개체를 전달합니다.개체에서 상속할 개체를 생성하면 개체에서 상속되는 새 개체를 반환합니다.

```js
let user2 = Object.create(null);

user2.name = "Cam";
user2.points = 8;
user2.increment = function() {
  user2.points++;
}
```

> 위의 기본 개체 생성 옵션은 반복적입니다. 각 항목을 수동으로 생성해야 합니다. 이걸 어떻게 극복해야 할까요?

<br>

## Solutions


### Solution 1 — Generate objects using a function

아주 간단한 방법은 새로운 user라는 function을 생성하는 것 입니다.

```js
function createUser(name, points) {
  let newUser = {};
  newUser.name = name;
  newUser.points = points;
  newUser.increment = function() {
    newUser.points++;
  };
  return newUser;
}
```

사용자를 생성하려면 이제 기능의 매개 변수에 정보를 입력합니다.

```js
let user1 = createUser("Bob", 5);
user1.increment();
```
그러나 위의 예에서 increment 함수는 해당 복사본일 뿐입니다. 각 개체에 대해 기능의 변경 가능성을 수동으로 수행해야 하므로 이 방법은 코드를 쓰는 데 적합하지 않습니다.

<br>

### Solution 2 — Use the prototypal nature of JavaScript

Python 및 Java와 같은 개체 지향 언어와 달리 JavaScript에는 클래스가 없습니다. 상속을 위해 프로토타입과 프로토타입 체인의 개념을 사용합니다.

새로운 array를 생성하면 array와 같은 기본 제공 메서드에 자동으로 액세스할 수 있습니다. `Array.join`, `Array.sort`, `Array.filter`를 선택합니다. 이는 Array 개체가 `Array.protype`에서 속성을 상속하기 때문입니다.

![](https://cdn-media-1.freecodecamp.org/images/CHrqNxf5I7tIo4-CfbSXqC6fnDd2H273ieWJ)

<br>

모든 JavaScript 기능에는 기본적으로 비어 있는 프로토타입 속성이 있습니다. 이 프로토타입 속성에 기능을 추가할 수 있으며, 이 양식에서는 이를 메소드라고 합니다. 상속된 기능이 실행되면 이 값이 상속되는 개체를 가리킵니다.

```js
function createUser(name, points) {
  let newUser = Object.create(userFunction);
  newUser.name = name;
  newUser.points = points;
  return newUser;
}

let userFunction = {
  increment: function() {this.points++};
  login: function() {console.log("Please login.")};
}

let user1 = createUser("Bob", 5);
user1.increment();
```

`user1` 객체가 생성되었을 때 `userFunction`과의 프로토타입 체인 결합이 형성되었습니다.

`user1.increment()`가 call stack에 있는 경우, 글로벌 메모리에서 `user1`을 찾습니다. 다음으로, `increment`function을 찾지만 찾지 못합니다. prototype chain의 다음 object를 보고 거기서 increment function을 찾을 것입니다.

<br>

### Solution 3 — new and this keywords

![](https://cdn-media-1.freecodecamp.org/images/OQKKYIojqDyXBnOwHdtZfxKL8YMOCii-2GTl)

new operator는 생성자 기능이 있는 개체의 인스턴스를 만드는 데 사용됩니다. 새로운 구성자 기능을 호출하면 다음 작업을 자동화합니다.

- 새로운 객체가 생성됩니다.
- 이 항목을 개체에 바인딩합니다.
- 생성자 함수의 프로토타입 개체가 새 개체의 __proto__ 속성이 됩니다.
- function에서 Object를 반환합니다.

자동화로 인해 반복적인 코드가 줄어들기 때문에 매우 좋습니다!

```js
function User(name, points) {
 this.name = name; 
 this.points = points;
}
User.prototype.increment = function(){
 this.points++;
}
User.prototype.login = function() {
 console.log(“Please login.”)
}

let user1 = new User(“Dylan”, 6);
user1.increment();
```

프로토타입 패턴을 사용하면 각 방법과 속성이 객체 프로토타입에 직접 추가됩니다.

프로토타입체인 위로 올라가 user의 프로토타입 특성 아래에서 increment function을 찾습니다.

> REMEMBER: JavaScript의 모든 기능도 객체입니다. 

이제 필요한 항목을 찾았으므로 `user1.increment()`를 실행할 때 새로운 로컬 실행 컨텍스트를 만들 수 있습니다.

<br>

> SIDE NOTE: __proto__와 프로토타입의 차이입니다.

이미 __proto__와 프로토타입에 대해 혼동하고 있다면 걱정하지 마세요! 프로토타입(Prototype)은 생성된 개체에서 __proto__ 속성이 되는 것을 결정하는 생성자 기능의 속성입니다. __proto__는 생성된 참조이며, 이 참조는 프로토타입 체인 결합이라고 알려져 있습니다.

<br>

### Solution 4 — ES6 ‘syntactic sugar’

다른 언어를 사용하면 "constructor" 객체 자체 내에서 공유 방법을 쓸 수 있습니다. ECMAScript6는 `class` 키워드를 도입하여 다른 클래식 언어의 일반 클래스와 유사한 클래스를 작성할 수 있습니다. JavaScript의 프로토타입에 대한 syntactic sugar입니다.

```js
class User {
  constructor(name, points) {
    this.name = name;
    this.points = points;
  }
  increment () {
    this.points++;
  }
  login () {
    console.log("Please login.")
  }
}

let user1 = new User("John", 12);
user1.increment();
```

솔루션 3에서는 `User.protype`을 사용하여 관련 방법을 정확하게 구현했습니다. 이 솔루션에서는 동일한 결과를 얻지만 구문이 더 깨끗해 보입니다.

<br>

## 결론

이제 JavaScript에서 개체를 생성하는 다양한 옵션에 대해 자세히 알아봤습니다. 클래스 선언과 새로운 운영자는 비교적 사용하기 쉽지만, 자동화된 것을 이해하는 것이 중요합니다.

> 다시 요약하려면 생성자 기능이 새 기능으로 호출될 때 다음 작업이 자동화됩니다.

- 새 개체가 생성됩니다.
- 이 항목을 개체에 바인딩합니다.
- 생성자 함수의 프로토타입 개체가 새 개체의 __proto__ 속성이 됩니다.
- function에서 Object를 반환합니다.

