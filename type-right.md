


# typeof 와 instanceof 살펴보기 

> 원문: [Beyond  `typeof`  and  `instanceof`: simplifying dynamic type checks](https://2ality.com/2017/08/type-right.html)

이 포스팅은 instanceof를 더 많은 값(피연산자)에 적용할 수 있는 기법을 설명한다. 특히 원시 값에 관해 집중할 것이다.

## 1. 배경지식: `typeof`  vs.  `instanceof`

Javascript에서 값의 타입 확인을 언제 해야할까? 대략 적인 규칙은 다음과 같다.

* `typeof`는 값이 원시 타입의 요소인지 확인한다.

	```js
	if (typeof value === 'string')
	```
	
*  `instanceof`는 값이 클래스 또는 생성자 함수의 인스턴스인지 확인한다.
	
	```js
	if (value instanceof Map)
	```

	(또한 value.constructor 및 value.constructor.name도 유용하다)

하지만 이것은 이상적이지 못하다. 왜냐하면 원시 값과 객체의 차이점이 종종 모호해지기 때문이다. 추가로 몇가지 이상한 점이 상황을 더욱 복잡하게 만든다.

*  `typeof null`은  `'object'`이다.  `'null'`이 아니다. 이것은 버그로 간주된다.
*  `typoeof`는 객체와 함수를 구분할 수 있다.

	```bash
	> typeof {}
	'object'
	> typeof function () {}
	'function'
	```
	
	따라서 `typeof`를 통해 개체성을 확인할 수 있는 간단한 방법이 없다는 것을 의미한다.

* [모든 객체가 Object의 인스턴스는 아니다.](http://speakingjs.com/es5/ch17.html#_pitfall_objects_that_are_not_instances_of_object)

	```bash
	> Object.create(null) instanceof Object
	false
	```

## 2. 원시 값에 `instaceof` 적용해보기

PrimitiveNumber 클래스가 주어지면, 아래의 코드는 `x instanceof PrimitiveNumber` 표현식이 true를 반환하는 값 x를 구성한다. PrimitiveNumber에 대한 정적 메서드를 구현하면 키가 공용 심볼 인 [Symbol.hasInstance](https://exploringjs.com/es6/ch_oop-besides-classes.html#_property-key-symbolhasinstance-method)가 된다.

```js
class PrimitiveNumber {
    static [Symbol.hasInstance](x) {
        return typeof x === 'number';
    }
}
console.log(123 instanceof PrimitiveNumber); // true
```

## 3. TypeRight library를 통한 동적 타입 확인

[TypeRight](https://github.com/rauschma/type-right)은 동적 타입 확인을 위한 라이브러리이다. 다른 기능 중에서도 다음 예제를 구현하기 위해 이전 예제에서 사용한 방법을 이용한다. 이것의 유일한 목적은 instanceof 연산자의 오른쪽 있는 것이다.

-   `PrimitiveUndefined`
-   `PrimitiveNull`
-   `PrimitiveBoolean`
-   `PrimitiveNumber`
-   `PrimitiveString`
-   `PrimitiveSymbol`

TypeRight는 값이 개체인지 확인하는 클래스를 제공하지 않지만 쉽게 추가할 수 있다.

이러한 기초를 바탕으로 TypeRight를 사용하여 함수의 매개 변수에 적절한 타입이 있는지 확인할 수 있다.

```js
import * as tr from 'type-right';

function dist(x, y) {
    tr.force(x, tr.PrimitiveNumber, y, tr.PrimitiveNumber);
    return Math.hypot(x, y);
}
dist(3, 4); // 5
dist(3, undefined); // TypeError
```

## 4. 간단한 타입 확인을 위한 다른 접근 방법

유형 검사를 처리하기위한 두 가지 제안은 현재 statge-0 단계에 있다. 즉, 향후에 더 이상 탐구되지 않을 수도 있는 뜻이다.

### 4.1. 패턴 매칭

Brian Terlson과 Sebastian Markbåge에 의해 제안된 "[ECMAScript Pattern Matching Syntax](https://github.com/tc39/proposal-pattern-matching)"이 있다. `Symbol.matches` 가 존재하며 값을 "machable"하게 만든다.

```js
match (val) {
    MyClass:
        console.log('val is an instance of MyClass');
}
```

이 속성은 다음과 같이 자동으로 생성되거나 수동으로 추가 될 수 있다. (A 행).

```js
class PrimitiveNumber {
    static [Symbol.matches](x) { // (A)
        return x instanceof this;
    }
}
```

### 4.2. `Builtin.is()`  and  `Builtin.typeOf()`

James M. Snell의 "[Builtin.is and Builtin.typeOf](https://github.com/jasnell/proposal-istypes)" 제안은 타입 검사를 위한 몇 가지 메커니즘을 소개한다.

`Builtin.is(value1, value2)`는 `value1`과 `value2`가 동일한 생성자를 참조하는지 확인한다. `value1`과 `value2`는 현재 영역이나 다른 영역에서 가져올 수 있다.

```bash
> Builtin.is(Date, vm.runInNewContext('Date'))
true
> Builtin.is(Date, Date)
true
```

`Builtin.typeOf()`는 원시 값과 내장 클래스 모두에서 작동하는 `typeof`의 확장으로 볼 수 있다.

```js
Builtin.typeOf(undefined); // 'undefined'
Builtin.typeOf(null); // 'null'
Builtin.typeOf(123); // 'number'

Builtin.typeOf(new Number()); // 'Number'
Builtin.typeOf([]); // 'Array'
Builtin.typeOf(new Map()); // 'Map'

// The builtin counts, not the user-defined class
class MyArray extends Array {}
Builtin.typeOf(new MyArray()); // 'Array'
```

## 5.추가 읽을 거리

-   Sect. “[The  `constructor`  property of instances](http://speakingjs.com/es5/ch17.html#constructor_property)” in “Speaking JavaScript”
-   Sect. “[Pitfall:  `typeof null`](http://speakingjs.com/es5/ch09.html#isobject_typeof)” in “Speaking JavaScript”
-   Sect. “[`typeof`: Categorizing Primitives](http://speakingjs.com/es5/ch09.html#typeof)” in “Speaking JavaScript”
-   Sect. “[Pitfall: objects that are not instances of Object](http://speakingjs.com/es5/ch17.html#_pitfall_objects_that_are_not_instances_of_object)” in “Speaking JavaScript”
-   Sect. “[Property key  `Symbol.hasInstance`](http://exploringjs.com/es6/ch_oop-besides-classes.html#_property-key-symbolhasinstance-method)” in “Exploring ES6”