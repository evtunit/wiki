# Copying Objects in JavaScript

> 원문: [Copying Objects in JavaScript](https://scotch.io/bar-talk/copying-objects-in-javascript)

객체는 자바스크립트의 기본 블록이다. 객체는 속성의 집합이며 속성은 키(또는 이름)와 값의 연결이다. Javascript에 있는 거의 모든 객체는 프로토타입 체인의 상단에 있는 `Object`의 인스턴스들이다.

## 소개 

아시다시피, 할당 연산자는 객체의 복사본을 만들지 않고, 객체에 대한 참조 만을 할당한다. 다음 코드를 살펴보자.

```js
let obj = {
  a: 1,
  b: 2,
};
let copy = obj;

obj.a = 5;
console.log(copy.a);
// Result 
// a = 5;
```

`obj` 변수는 초기화된 새 객체를 담고 있다. `copy` 변수는 `obj`가 가지고 있는 객체를 가르키고, 그 것을 참조하고 있다.  그렇기에 기본적으로 `{ a: 1, b: 2 }` object는 두가지 변수를 통해 (obj, copy) 접근이 가능하다. `obj` 변수나  `copy` 변수에게 값을 할당 하면 `{ a: 1, b: 2 }` 값이 변경될 것이다.

Immutability(불변성)이란 단어가 요새 널리 사용되고 있는데, 이를 주목해야한다. 위와 같은 방법은 어떤 형태의 불변성을 제거하며 원래의 객체가 코드의 다른 부분에 의해 사용될 경우 버그로 이어질 수 있다.

## 단순한 객체 복사 방법

객체를 복사하는 단순한 방법은 원래의 객체를 순환시켜 각각의 속성을 차례로 복사하는 것이다. 이 코드를 살펴보자.

```js
function copy(mainObj) {
  let objCopy = {}; // objCopy will store a copy of the mainObj
  let key;

  for (key in mainObj) {
    objCopy[key] = mainObj[key]; // copies each property to the objCopy object
  }
  return objCopy;
}

const mainObj = {
  a: 2,
  b: 5,
  c: {
    x: 7,
    y: 4,
  },
}

console.log(copy(mainObj));
```

### 내제된 문제점 

1. 의도한 것과 달리, `mainObj` 객체의 프로토타입 메서드와 새로운 `objCopy`의 `Object.prototype` 메서드가 서로 다르다.  원래 의도는 객체의 정확한 사본을 가지는 것이다.
2. 속성 서술자(Property descriptors)는 복사되지 않는다. 값이 false로 설정된 "writable" 서술자는  `objCopy` 객체에서 true가 된다. 
	> 속성 서술자(Property descriptors) 참고: [Object.defineProperty()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty#%EC%84%A4%EB%AA%85)
3. 위의 코드는 `mainObj`의 열거가능한(enumerable) 속성만 복사한다.
4. 원본 객체의 속성 중 하나가 객체인 경우, 복사본과 원본 간에 공유되어 각각의 속성이 동일한 객체를 가르키도록 할 것이다.

## 얕은 객체 복사

최상 위 속성이 아무런 참조 없이 복사되고 값이 객체로서 참조로 복사되는 소스 속성이 존재할 때 개체는 얕게 복사된다고 한다. 원본 값이 객체에 대한 참조인 경우 해당 참조 값만 복사 된다.

> **필독**: [Learn React from Scratch! (2019 Edition)](https://bit.ly/2Xeeq5t)

얕은 복사는 최상위 속성을 복제하지만 *중첩된 객체*는 원본(소스)과 복사본(대상) 간에 *공유*된다.

### Object.assign() 메소드 이용하기

`Object.assign()` 메소드는 모든 열거 가능한 속성의 값을 복사하는 데 사용된다.

```js
let obj = {
  a: 1,
  b: 2,
};
let objCopy = Object.assign({}, obj);
console.log(objCopy);
// Result - { a: 1, b: 2 }
```

겉으로 보기엔, 지금까지 했던 결과랑 똑같아 보인다.  이미 copy 함수를 이용하여 `obj`를 복사 했었다. 위와 다르게 불변성(immutability)을 가지는지 확인해보자.

```js
let obj = {
  a: 1,
  b: 2,
};
let objCopy = Object.assign({}, obj);

console.log(objCopy); // result - { a: 1, b: 2 }
objCopy.b = 89;
console.log(objCopy); // result - { a: 1, b: 89 }
console.log(obj); // result - { a: 1, b: 2 }
```

위의 코드에서, `objCopy`의 `b` 속성 값을 `89`로 변경하였고, 콘솔에 수정된 `objCopy` 값을 찍어보면 변경 내용은 `objCopy`에만 적용된다. 코드 마지막 줄을 보면 `obj` 객체가 온전한 것을 확인할 수 있다. 이는 원본 객체에 대한 어떠한 참조도 없이 성공적으로 사본을 만들었다는 것을 의미한다. 

### Object.assign()의 문제점

성공적으로 복사본을 만들었고 모든 것이 잘 되고 있는 것처럼 보이지만, 얕은 복사에 대해 논의했던 것을 기억하는가? 이 예제를 살펴보자.

```js
let obj = {
  a: 1,
  b: {
    c: 2,
  },
}
let newObj = Object.assign({}, obj);
console.log(newObj); // { a: 1, b: { c: 2} }

obj.a = 10;
console.log(obj); // { a: 10, b: { c: 2} }
console.log(newObj); // { a: 1, b: { c: 2} }

newObj.a = 20;
console.log(obj); // { a: 10, b: { c: 2} }
console.log(newObj); // { a: 20, b: { c: 2} }

newObj.b.c = 30;
console.log(obj); // { a: 10, b: { c: 30} }
console.log(newObj); // { a: 20, b: { c: 30} }

// Note: newObj.b.c = 30; Read why..
```

### 왜 obj.b.c = 30 일까?

이 것이 `Object.assign()` 의 문제점이다. `Object.assign()`은 얕은 복사본만 만든다. `newObj.b` 와  `obj.b` 둘 다 각각의 복사본이 만들어지지 않았다. 따라서 동일한 객체를 공유하고, 객체에 대한 참조가 복사된 것이다. 객체 속성을 변경하면 해당 객체를 사용하는 모든 참조에 적용된다. 이걸 어떻게 고칠 수 있을까? 다음 섹션에 해결책이 있다.

참고 : 프로토타입 체인 및 열거 불가능 속성의 속성은 복사 할 수 없다. 아래 예제를 보자:

```js
let someObj = {
  a: 2,
}

let obj = Object.create(someObj, { 
  b: {
    value: 2,  
  },
  c: {
    value: 3,
    enumerable: true,  
  },
});

let objCopy = Object.assign({}, obj);
console.log(objCopy); // { c: 3 }
```

* `someObj`은 `obj`의 프로토타입 체인에 존재하므로 복사되지 않는다.
* `속성 b`는 열거가능한 속성이 아니다.
* `속성 c`는 enumerable 속성 서술자를 가지고 있다. 그래서 복사 된 것이다.

## 깊은 객체 복사

깊은 복사는 마주하는 모든 객체를 복제할 것이다. 사본과 원래의 객체는 아무것도 공유하지 않을 것이므로 원본의 사본이 될 것이다. `Object.assign()`을 사용하여 발생한 문제에 대한 해결책은 다음과 같다.

### JSON.parse(JSON.stringify(object))를 사용하기

해당 방법은 이전에 발생한 문제를 해결한다. 이제 `newObj.b`는 참조가 아닌 사본을 가진다. 이는 객체를 깊이 복사하는 방법이다. 예를 들어보자.

```js
let obj = { 
  a: 1,
  b: { 
    c: 2,
  },
}

let newObj = JSON.parse(JSON.stringify(obj));

obj.b.c = 20;
console.log(obj); // { a: 1, b: { c: 20 } }
console.log(newObj); // { a: 1, b: { c: 2 } } (New Object Intact!)
```

불변성도 가진다.

### JSON.parse(JSON.stringify(object))의 문제점

불행하게도 이 방법은 사용자 정의 객체 메소드를 복사하는 데 사용할 수 없다. 아래 내용을 참고하자.

## 객체의 메소드를 복사하기

함수인 메소드는 객체의 속성이다. 지금까지 예제에서는 객체의 메소드를 복사하지 않았다. 앞으로 예제에선 이전에 나온 방식을 사용하여 메소드 복사를 시도할 것이다.

```js
let obj = {
  name: 'scotch.io',
  exec: function exec() {
    return true;
  },
}

let method1 = Object.assign({}, obj);
let method2 = JSON.parse(JSON.stringify(obj));

console.log(method1); //Object.assign({}, obj)
/* result
{
  exec: function exec() {
    return true;
  },
  name: "scotch.io"
}
*/

console.log(method2); // JSON.parse(JSON.stringify(obj))
/* result
{
  name: "scotch.io"
}
*/
```

결과는, `Object.assign()`를 사용하면 메소드를 복사할 수 있는 반면에  `JSON.parse(JSON.stringify(obj)` 사용하면 메소드를 복사할 수 없다는 것을 보여준다.

## 순환 객체 복사

순환 객체는 자신을 참조하는 속성이있는 객체이다. 지금까지 배운 객체를 복사하는 방법을 사용하여 순환 객체의 복사본을 만들고 그것이 작동하는지 살펴보자.

###  JSON.parse(JSON.stringify(object)) 사용하기

 `JSON.parse(JSON.stringify(object))` 사용해서 순환 객체를 복사해보자.:

```javascript
// circular object
let obj = { 
  a: 'a',
  b: { 
    c: 'c',
    d: 'd',
  },
}

obj.c = obj.b;
obj.e = obj.a;
obj.b.c = obj.c;
obj.b.d = obj.b;
obj.b.e = obj.b.c;

let newObj = JSON.parse(JSON.stringify(obj));

console.log(newObj); 
```

결과는 아래와 같다.

[[/resource/yongkwan/18/01.png]]

`JSON.parse(JSON. stringify(obj)`는 분명히 순환 객체에 효과가 없다.

### Object.assign()을 사용하여 순환 객체 복사하기

 `Object.assign()` 사용해서 순환 객체를 복사해보자.:
 
```javascript
let obj = { 
  a: 'a',
  b: { 
    c: 'c',
    d: 'd',
  },
}

obj.c = obj.b;
obj.e = obj.a;
obj.b.c = obj.c;
obj.b.d = obj.b;
obj.b.e = obj.b.c;

let newObj2 = Object.assign({}, obj);

console.log(newObj2); 
```

결과는 아래와 같다.

[[/resource/yongkwan/18/02.png]]

`Object.assign()`은 얕은 복사하는 데는 효과가 있지만 깊은 복사에는 효과가 없다. 브라우저 콘솔에서 순환 객체 트리를 참고해보자. 그곳에서 많은 흥미로운 작업이 진행되고 있다는 것을 알게 될 것이다.

## 전개 구문 (...) 사용하기

ES6에는 이미 배열 해체 할당을 위한 나머지 요소(rest elements)와 구현된 배열 리터럴을 위한 분산 요소(spread elements)가 있다. 아래 예제에서 배열에 대한 분산 요소 구현을 살펴보자.

```js
const array = [
  "a",
  "c",
  "d", {
    four: 4
  },
];
const newArray = [...array];
console.log(newArray);
// Result 
// ["a", "c", "d", { four: 4 }]
```

객체 리터럴에 대한 확산 속성(spread property)은 현재 [ECMAScript에 대한 3단계 제안](https://github.com/tc39/proposal-object-rest-spread)이다. 객체의 스프레드 속성은 자체 객체의 열거가능 속성을 원본 객체에서 대상 객체로 복사한다. 아래의 예제는 제안이 받아들여지면 개체를 복사하는 것이 얼마나 쉬운지를 보여준다.

```js
let obj = {
  one: 1,
  two: 2,
}

let newObj = { ...z };

// { one: 1, two: 2 }
```

참고: 얕은 복사에만 효과가 있다.

## 결론

JavaScript에서 개체를 복사하는 것은 어렵다. JavaScript를 처음 접하고 언어를 모르는 경우에 매우 힘들 수 있다. 이 글이 객체를 복사할 때 직면할 수 있는 문제를 이해하고 해결하는데 도움이 되기를 바란다. 더 나은 결과를 얻을 수 있는 라이브러리 또는 코드 조각이 있다면 공유하길 바란다. 해피코딩!
