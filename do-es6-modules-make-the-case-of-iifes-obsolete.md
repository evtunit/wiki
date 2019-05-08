# ES6 모듈이 IIFE를 구시대의 유물로 만드는가?

> 원문: [Do ES6 Modules make the case of IIFEs obsolete?](https://hashnode.com/post/do-es6-modules-make-the-case-of-iifes-obsolete-civ96wet80scqgc538un20es0)

IIFE (**Immediately Invoked Function Expression**) 는 익명의 클로저를 통해 "privacy"를 제공하는 설계 패턴으로 사용된다.  ES6 모듈의 출현은 IIFE의 사용을 구시대의 유물로 만드는가?

아니면 아직도 IIFE를 위한 사용 사례가 있을까?

IIFE는 함수가 코드의 블록 스코프를 만드는 유일한 방법이었기 때문에,  ES5 표준에서 가장 많이 사용하는 패턴 중 하나였다. 따라서 다음 코드는 `ReferenceError`를 발생시킬 것이다.

```js
(function() {
  var scoped = 42;
}());

console.log(scoped); // ReferenceError
```

ECMAScript는  IIFE 대신에 사용할 블록 스코프가 적용되는 `let`과 `const`를 도입했다. 아래의 코드를 보자.

```js
{
  let scoped = 42;
}

console.log(scoped); // ReferenceError
```

이 코드가 이전 코드보다 더 명확하고 이해하기 쉽다.


IIFE는 노출 모듈 패턴에서 빌딩 블록으로 사용되었다. 이는 public과 private를 명확이 구분하는 것이 장점이다.

```js
var myModule = (function() {
  // private 변수, IIFE 안에서만 접근할 수 있다.
  var counter = 0;

  function increment() {
    counter++;
  }

  // 외부로 노출되는 로직
  return {    
    increment: increment
  }
}());
```

이 패턴을 사용하는 것 대신에, ES6의 모듈을 사용할 수 있다. 모듈은 자신의 스코프를 선언하고 모듈 내부에서 생성 된 변수는 전역 객체를 오염시키지 않는다.

```js
// myModule.js

let counter = 0;

export function increment() {
  counter++;
}    

// logic.js

import {increment} from 'myModule.js';

increment();
```

IIFE를 사용하는 경우는 즉시 호출되는 화살표 함수를 사용할 때이다. 때로는 단일 표현식이 아니라 일련의 구문을 통해서만 결과를 생성할 수 있으며, 이러한 구문을 인라인화 하려면 즉시 함수를 호출해야 한다;

```js
const SENTENCE = 'Hello world, how are you?';
const REVERSE = (() => {
  const array  = [...SENTENCE];
  array.reverse();
  return arr.join('');
})();
```

화살표 함수를 괄호`( )`로 묶어야 함을 명심해라, (전체 함수를 감싸지 않음).

## 느낀점

* 짧아서 좋다.
* 글로벌 변수의 오염(polution)이라는 표현을 자주 쓰는구나.