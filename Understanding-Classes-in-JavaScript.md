# 소개

JavaScript는 프로토타입 기반 언어이며, JavaScript의 모든 객체에는 `[[Prototype]]`이라는 숨겨진 내부 속성이 있어 객체 특성 및 메서드를 확장하는 데 사용할 수 있습니다.

최근까지도 개발자들은 JavaScript에서 객체 지향 설계 패턴을 모방하기 위해 [constructor function](https://www.digitalocean.com/community/tutorials/understanding-prototypes-and-inheritance-in-javascript#constructor-functions)을 사용했습니다. ECMAScript 2015 언어 사양은 종종 ES6으로 불리며 JavaScript 언어에 `클래스`를 도입했습니다. JavaScript의 클래스는 실제로 추가 기능을 제공하지 않으며, 더 깨끗하고 간결한 문법을 제공한다는 점에서 프로토타입과 상속에 대해 "syntactical sugar"를 제공하는 것으로 설명되기도 합니다. 다른 프로그래밍 언어에서는 클래스를 사용하기 때문에 JavaScript의 클래스 구문을 사용하면 개발자가 언어 간에 쉽게 이동할 수 있습니다.

> * syntactical sugar? https://web-front-end.tistory.com/26

<br>

## Classes Are Functions

JavaScript 클래스는 함수 유형입니다. 클래스는 `class` 키워드로 선언됩니다. 함수 식 구문을 사용하여 함수를 초기화하고 클래스 식 구문을 사용하여 클래스를 초기화합니다.

```js
// Initializing a function with a function expression
const x = function() {}

// Initializing a class with a class expression
const y = class {}
```

`Object.getPrototypeOf()` 메서드를 사용하여 객체의 `[[Prototype]]`에 액세스할 수 있습니다. 이걸 사용하여 빈 function을 생성하도록 하겠습니다.

```js
Object.getPrototypeOf(x);

// Output
ƒ () { [native code] }
```

class를 생성하여 사용할 수 있습니다.

```js
Object.getPrototypeOf(y);

// Output
ƒ () { [native code] }
```

함수 및 클래스로 선언된 코드는 함수 `[[Prototype]]`을 반환합니다. 프로토타입을 사용하면 모든 기능이 새로운 키워드를 사용하여 생성자 인스턴스가 될 수 있습니다.

```js
const x = function() {}

// Initialize a constructor from a function
const constructorFromFunction = new x();

console.log(constructorFromFunction);

// Output
x {}
constructor: ƒ ()
```

classes도 적용됩니다.

```js
const y = class {}

// Initialize a constructor from a class
const constructorFromClass = new y();

console.log(constructorFromClass);
// Output
y {}
constructor: class
```

이러한 프로토타입 Constructor 예시는 비어 있지만, 구문 아래에서는 두 방법 모두 동일한 결과를 도출해가는 방법을 확인할 수 있습니다.

<br>

## Defining a Class

생성자 함수는 함수 자체를 참조하여 여러 매개변수를 사용하여 초기화됩니다. 식별자의 첫 번째 문자는 관례에 따라 대문자로 표시됩니다.

**constructor.js**

```js
// Initializing a constructor function
function Hero(name, level) {
    this.name = name;
    this.level = level;
}
```

이것을 클래스 구문(class syntax)으로 변환해 보면, 매우 유사한 구조로 되어 있음을 알 수 있습니다.

**class.js**
```js
// Initializing a class definition
class Hero {
    constructor(name, level) {
        this.name = name;
        this.level = level;
    }
}
```

생성자 함수는 initializer의 첫 번째 문자(선택 사항)의 대문자 및 구문에 대한 익숙함 통해 객체 Blueprint를 의미한다는 것을 알고 있습니다. 클래스 키워드는 우리 기능의 목표를 보다 직설적으로 전달합니다.

초기화 구문의 유일한 차이점은 함수 대신 `class` 키워드를 사용하고 `constroctor()` 메서드 내에 속성을 할당하는 것입니다.

<br>

## Defining Methods

생성자 기능은 일반적으로 아래 `greet()` 방법에서 볼 수 있듯이, 초기화 대신 프로토타입에 직접 메소드를 할당하는 것입니다.

**constructor.js**
```js
function Hero(name, level) {
    this.name = name;
    this.level = level;
}

// Adding a method to the constructor
Hero.prototype.greet = function() {
    return `${this.name} says hello.`;
}
```

클래스를 사용하면 문법, 구문이 단순화되고 메서드를 클래스에 직접 추가할 수 있습니다. ES6에서 소개한 방법을 사용하면 선언을 정의하는 것이 훨씬 더 코드들이 간결해지게 됩니다.

**class.js**
```js
class Hero {
    constructor(name, level) {
        this.name = name;
        this.level = level;
    }

    // Adding a method to the constructor
    greet() {
        return `${this.name} says hello.`;
    }
}
```

이러한 특성 및 실행 방법을 살펴보겠습니다. `new` 키워드를 사용하여 Hero의 새로운 인스턴스를 만들고 몇 가지 값을 할당할 것입니다.

```js
const hero1 = new Hero('Varg', 1);
```

`console.log(hero1)`를 사용하여 새 개체에 대한 자세한 정보를 출력하면 클래스 초기화에 대해 자세히 알 수 있습니다.

```js
Output
Hero {name: "Varg", level: 1}
__proto__:
  ▶ constructor: class Hero
  ▶ greet: ƒ greet()
```

`hero1`의 호출로 `constructor()`와 `greet()`의 값에 대한 할당이 `__proto__` 또는 [[Prototype]]`에 적용되었다는 것을 `console.log`에서 확인할 수 있습니다.

<br>

## Extending a Class

생성자 기능 및 클래스의 이점은 상위 항목을 기반으로 새 객체로 확장할 수 있다는 것입니다. 이렇게 하면 비슷하지만 몇 가지 추가 기능 또는 그 이상의 특정 기능이 필요한 개체에 대한 코드가 반복되지 않습니다.

새로운 생성자 function이 생성이 되면 `call()` 메서드를 부모에게 연결해줍니다. 예를들어, `Mage`라는 class를 만들고 `call()`을 사용하여 `Hero` 의 속성을 할당하고, 추가 속성을 추가할 것입니다.

**constructor.js**
```js
// Creating a new constructor from the parent
function Mage(name, level, spell) {
    // Chain constructor with call
    Hero.call(this, name, level);

    this.spell = spell;
}
```

여기서 포인트는 `Hero`는 `Mage`에 새로운 인스턴스가 생성이 되면서 프로퍼티를 할당받게 됩니다.

```js
const hero2 = new Mage('Lejon', 2, 'Magic Missile');
```

`hero2`를 콘솔에 찍어보게 되면 Mage를 통해 새로운 생성자라 생성이 된 것을 볼 수 있습니다.

```js
Output
Mage {name: "Lejon", level: 2, spell: "Magic Missile"}
__proto__:
    ▶ constructor: ƒ Mage(name, level, spell)
```

ES6 클래스 중 `super`라는 키워드가 있습니다. 이 키워드는 `call`대신 부모 function에 대한 기능을 액세스하게 됩니다. 부모의 class를 확장하기 위해 사용됩니다.

**class.js**
```js
// Creating a new class from the parent
class Mage extends Hero {
    constructor(name, level, spell) {
        // Chain constructor with super
        super(name, level);

        // Add a new property
        this.spell = spell;
    }
}
```

동일한 방식으로 `Mage`에 새로운 인스턴스를 생성합니다.

```js
const hero2 = new Mage('Lejon', 2, 'Magic Missile');
```

hero2를 콘솔로 찍어보면,

```js
Output
Mage {name: "Lejon", level: 2, spell: "Magic Missile"}
__proto__: Hero
    ▶ constructor: class Mage
```

출력은 거의 동일합니다. 단, 클래스 구성에서 `[[Prototype]]`이 부모(Hero)와 연결됩니다.
다음은 전체 초기화 프로세스, 추가 방법, 생성자 함수 및 클래스의 상속을 나란히 비교한 것입니다.

**constructor.js**
```js
function Hero(name, level) {
    this.name = name;
    this.level = level;
}

// Adding a method to the constructor
Hero.prototype.greet = function() {
    return `${this.name} says hello.`;
}

// Creating a new constructor from the parent
function Mage(name, level, spell) {
    // Chain constructor with call
    Hero.call(this, name, level);

    this.spell = spell;
}
```

**class.js**
```js
// Initializing a class
class Hero {
    constructor(name, level) {
        this.name = name;
        this.level = level;
    }

    // Adding a method to the constructor
    greet() {
        return `${this.name} says hello.`;
    }
}

// Creating a new class from the parent
class Mage extends Hero {
    constructor(name, level, spell) {
        // Chain constructor with super
        super(name, level);

        // Add a new property
        this.spell = spell;
    }
}
```

두개의 문법은 상당히 다르지만 두 방법 모두 기본 결과는 거의 동일합니다. 클래스를 통해 객체를 보다 간결하게 작성할 수 있으며, 생성자 기능은 hood 아래에서 발생하는 작업을 보다 정확하게 설명합니다.

<br>

## 결론

이 본문은 JavaScript 생성자 기능과 ES6 클래스 간의 유사점과 차이점에 대해 알아 보았습니다. 클래스 및 생성자는 모두 프로토타입 기반 상속 언어인 JavaScript에 객체 지향 상속 모델을 모방합니다.

효과적인 JavaScript 개발자가 되기 위해서는 프로토타입 상속을 이해하는 것이 무엇보다 중요합니다. React와 같은 인기 있는 JavaScript 라이브러리가 클래스 구문을 자주 사용하므로 클래스에 익숙해지는 것이 매우 유용합니다.