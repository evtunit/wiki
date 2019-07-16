
# 원형 상속

> 원문: [Prototypal inheritance](https://javascript.info/prototype-inheritance)

프로그래밍에서 종종 무언가를 확장하기를 원한다.

예를들어, 속성들과 메서드를 가진 `user` 객체가 있고 이를 약간 변형하여  `admin`과 `guest`를 만드려고 할때  `user`에 있는 것들을 재사용하고 싶을 것이다. 즉, 메서드를 복사/재작성하지 않고 새로운 객체를 만들고 싶을 것이다.

 이를 위해 원형 상속(Prototypal inheritance)이라는 특징이 존재한다.
 
## [[Prototype]]

자바스크립트의 객체(object)는  `null` 이거나 다른 객체를 참조하는 숨겨진 속성인 `[[Prototype]]`을 가지고 있다. (스펙 문서에 적혀있듯이). 이 객체를 "프로토타입"이라 한다.

[[/resource/yongkwan/17/01.png]]

프로토타입은 약간 마법같다. `object`에서 속성 값을 접근하려고 할때, 해당 값이 존재하지 않으면 자바스크립트는 자동적으로 그 객체의 프로토타입(원형)에서 그 값을 가져온다. 많은 언어들의 특징들과 프로그래밍 기술이 이를 토대로한다.

`[[Prototype]]` 속성은 내부적으로 숨겨져 있는데, 이를 설정하는 방법은 여러가지가 존재한다.

그 중 하나가 아래와 같이 `__proto__`를 사용하는 것이다.

```js
let animal = {
  eats: true
};
let rabbit = {
  jumps: true
};

rabbit.__proto__ = animal;
```

> **`__proto__` 는 `[[Prototype]]`을 위한 historical getter/setter이다.**
> 
> `__proto__`와 `[[Prototype]]`과 _같지 않다_는 것을 기억해라. `__proto__`는 `[[Prototype]]`의 getter/setter이다.
>
> `__proto__`는 역사적 이유로 존재하는데, 현대 언어에서는 `Object.getPrototypeOf/Object.setPrototypeOf` 같은 함수 형태로 대체되었다. 좀 더 나중에 대체된 이유와 해당 함수를 살펴 볼 것이다.
>
> 스펙에 따르면 `__proto__`는 브라우저에 의해서만 지원되어야 하지만, 사실 서버 측을 비롯한 모든 환경에서 지원한다. 지금부턴 `__proto__` 표기법이 조금 더 직관적으로 명백하므로 예제에서 사용할 것이다.

`rabbit`의 어떤 속성을 찾는데 그것이 없다면, 자바스크립트는 자동적으로 `animal`에서 찾을 것이다.

예를 들어:

```js
let animal = {
  eats: true
};
let rabbit = {
  jumps: true
};

rabbit.__proto__ = animal; // (*)

// we can find both properties in rabbit now:
alert( rabbit.eats ); // true (**)
alert( rabbit.jumps ); // true
```

여기`(*)`가 표시된 줄에서 `rabbit`의 프로토타입(원형)을 `animal`설정하고 있다.

이후, `alert`이 `(**)`에서 `rabbit.eats`속성을 읽으려 할 때, `rabbit`에는 해당 속성이 존재하지 않는다. 따라서 자바스크립트는 `[[Prototype]]` 참조를 따라가 `animal`에서 해당 속성을 찾아낸다. (이때 아래서부터 찾는다).

[[/resource/yongkwan/17/02.png]]

이것을 "`animal`은 `rabbit`의 프로토타입"라고 하거나 "`rabbit`은 `animal`으로 부터 프토로타입 상속을 받는다."라고 할 수 있다.

그래서 `animal`이 많은 유용한 속성들과 메소드를 가지고 있다면, `rabbit`에서 자동으로 사용할 수 있게 된다. 그러한 속성들을 "inherited" 라고 부른다.

`animal`에 특정 메소드가 존재하면, `rabbit`에서도 호출할 수 있다.:

```js
let animal = {
  eats: true,
  walk() {
    alert("Animal walk");
  }
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

// walk is taken from the prototype
rabbit.walk(); // Animal walk
```

아래와 같이, 해당 메서드(`walk`)는 자동적으로 프로토타입에서 가져와진다.

[[/resource/yongkwan/17/03.png]]

이러한 현상을 프로토타입 체인이라 하는데, 프로토타입 체인은 더 길어질 수 있다.

```javascript
let animal = {
  eats: true,
  walk() {
    alert("Animal walk");
  }
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

let longEar = {
  earLength: 10,
  __proto__: rabbit
};

// walk is taken from the prototype chain
longEar.walk(); // Animal walk
alert(longEar.jumps); // true (from rabbit)
```

[[/resource/yongkwan/17/04.png]]

단, 두가지의 제약사항이 있다.

1. 참조는 순환이 되어선 안된다.  `__proto__`를 순환하도록 할당하면 자바스크립트는 오류를 던질 것이다.
2. `__proto__`의 값은 객체이거나 `null` 이어야한다. 원시 값과 같은 다른 타입들은 무시된다.

또한 당연하게도 `[[Prototype]]`은 오직 한개만 가질 수 있다. 한 객체는 두개의 객체에서 동시에 상속 받을 수 없다.

## 값을 쓰는 것에 프로토타입을 사용하지 않기

프로토타입은 읽기 전용 속성이다.

그러나 속성을 쓰고/읽는 연산은 객체에 직접적으로 적용할 수 있다.

아래의 예를 보면, `walk` 메소드를 `rabbit`에 할당하고 있다.

```js
let animal = {
  eats: true,
  walk() {
    /* this method won't be used by rabbit */
  }
};

let rabbit = {
  __proto__: animal
};

rabbit.walk = function() {
  alert("Rabbit! Bounce-bounce!");
};

rabbit.walk(); // Rabbit! Bounce-bounce!
```

이제, `rabbit.walk()`의 호출은 프로토타입을 사용하지 않고 객체에서 즉시 찾아져 실행될 것이다.

[[/resource/yongkwan/17/05.png]]

이는 데이터 속성만을 위한 것이지 접근자를 위한 것은 아니다. 속성이 getter/setter 라면 함수처럼 동작한다. getter/setter는 프로토타입을 바라본다.

그런 이유로, `admin.fullName`은 아래 코드와 같이 정상적으로 동작한다.

```javascript
let user = {
  name: "John",
  surname: "Smith",

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  },

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};

let admin = {
  __proto__: user,
  isAdmin: true
};

alert(admin.fullName); // John Smith (*)

// setter triggers!
admin.fullName = "Alice Cooper"; // (**)
```

`(*)`라인의 `admin.fullName`속성은 `user` 프로토타입에서 getter를 가지고 있다, 그래서 호출된 것이다. 그리고 `(**)`라인의 속성은 프로토타입에서 setter를 가지고 있다. 때문에 호출된 것이다.

## "this" 값

위의 예제에서 흥미로운 질문이 나올 수 있다:  `set fullName(valuie)`에서  `this`는 어떤 값을 가지고 있을까?  코드 상 어느 위치에서 `this.name`과  `this.surname`은 쓰여졌을까?  `user`  또는  `admin`?  답은 간단하다. 

`this`는 프로토타입에 의해 전혀 영향을 받지 않는다.

**메서드가 어디서 발견 되던간에, 메서드 호출에서 `this`는 언제나 `.` 연산 앞에 있는 객체를 가르킨다.**

그래서, setter가 `admin.fullName=`를 호출할 때 `user`가 아닌  `admin`을  `this`로 사용한다.  

이건 사실 매우 매우 중요한 사실이다.  왜냐하면 많은 메소드를 가진 객체를 가지고 있고 이 객체를 상속받은 객체가 존재한다고 생각해보자. 그 후에 부모 객체의 메소드를 상속된 객체에서 실행한다면, 부모 객체의 상태값이 아닌 상속 객체의 상태 값을 수정할 것이다,

예를 들어, `animal`가 "메소드 저장소"라고 가정하고, `rabbit`은 이것을 사용한다 치자.

`rabbit.sleep()`호출은  `this.isSleeping`을  `rabbit`객체에 할당할 것이다.

```javascript
// animal has methods
let animal = {
  walk() {
    if (!this.isSleeping) {
      alert(`I walk`);
    }
  },
  sleep() {
    this.isSleeping = true;
  }
};

let rabbit = {
  name: "White Rabbit",
  __proto__: animal
};

// modifies rabbit.isSleeping
rabbit.sleep();

alert(rabbit.isSleeping); // true
alert(animal.isSleeping); // undefined (no such property in the prototype)
```

결과는 그림과 같다.

[[/resource/yongkwan/17/06.png]]

 `animal`로 부터 상속한  `bird`나  `snake`  등의 객체를 가지고 있다고 가정해보자. 이 것들도 역시  `animal`의 메소드들에 접근할 수 있을 것이다. 하지만 각 메소드의  `this`는  `animal`이 아니라 `.` 이전에 호출 시점에 평가된 각 객체에 대응할 것이다. 그래서 `this`에 데이터를 저장할때, 이 객체들에 저장하는 것이다.

결과적으로 메소드는 공유되지만, 객체의 상태 값은 공유되지 않는다.


## for…in loop

`for..in`은 상속받은 속성들도 순환한다.

예를 들어:

```javascript
let animal = {
  eats: true
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

// Object.keys only return own keys
alert(Object.keys(rabbit)); // jumps

// for..in loops over both own and inherited keys
for(let prop in rabbit) alert(prop); // jumps, then eats
```

원하는 결과가 이것이 아닐 것이다. 상속 받은 속성을 제외하고 싶을 것이다. 이를 위한 내장 메서드 [obj.hasOwnProperty(key)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)가 있다. 이 메서드는 속성 이름 `key`가 `obj`가 가진 속성이면 (상속 받은 것이 아니라), `true`를 반환한다.

따라서 상속된 속성들을 걸러낼 수 있다.

```javascript
let animal = {
  eats: true
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

for(let prop in rabbit) {
  let isOwn = rabbit.hasOwnProperty(prop);

  if (isOwn) {
    alert(`Our: ${prop}`); // Our: jumps
  } else {
    alert(`Inherited: ${prop}`); // Inherited: eats
  }
}
```

다음과 같은 상속 체인이 있다. `rabbit`은 `animal`을 상속 받고 `animal`은 `Obejct.prototype`으로 부터 상속 받은 것이다. 왜냐하면 기본적으로, `animal`은 리터럴 객체 `{...}` 이기 때문이다. 그리고 `Obejct.prototype`은 `null`을 바라본다.

[[/resource/yongkwan/17/07.png]]

여기에서 재미있는 점이 있다.  `rabbit.hasOwnProperty`는 어디서 나온 것일까? 우리는 해당 메서드를 정의하지 않았다. 상속 체인을 보면, `Object.prototype.hasOwnProperty`에 의해 제공된 메서드임을 알 수 있다. 즉, 이건 상속 받은 것이다.

그러나  상속된 속성들도 나열하는 `for..in` 반복에서,   왜 `eats` 이나 `jumps`처럼 `hasOwnProperty`가 보이지 않을까? 

답은 간단하다. `hasOwnProperty`는 열거가능(enumerable)한 속성이 아니기 때문이다. 이와 같이 `Object.prototype`속성들은 `enumerable:false` 값을 가지고 있다. 이게 바로 반복에서 보이지 않는 이유이다.

> **모든 다른 반복 메소드들은 상속 속성을 무시한다**
>
> `Object.keys`, `Object.values`와 같은 key / value를 가져오는 메서드들은 상속된 값을 무시한다.
>
> 이들은 오직 객체 자체가 가진 값을 가져온다,

## 요약

- 자바스크립트에서, 모든 객체는 숨겨진  `[[Prototype]]`속성을 갖는다. 그리고 이 속성은 또다른 객체이거나  `null`이다.
- `obj.__proto__`를 사용하여 이 속성에 접근할 수 있다.
- `[[Prototype]]`에 의해 참조되는 객체를 "프로토타입(원형)"이라고 한다.
- `obj`의 속성을 읽거나 메서드를 호출하고 싶은데 존재하지 않는다면, 자바스크립트는 프로토타입에서 그것을 찾으려 할 것이다.
-  읽기/지우기 연산은 객체에 직접적으로 적용된다. 프로토타입을 사용하지 않는다.(setter가 아닌 데이터 속성이라 가정했을 경우).
- `obj.method()`를 호출하고, 해당 `method`가 프로토타입에서 가져온 것이라면,  `this`는 여전히  `obj`를 킨다. 심지어 메소드들이 상속됬다 하더라도 메소드들을 언제나 현재 객체에 적용될 것입니다.
- `for..in` 루프는 자신의 값과 상속된 값을 반복한다. 그 외의 key/value를 가져오는 다른  모든 메소드들은 오직 그 자신의 객체의 속성만 가져온다.
