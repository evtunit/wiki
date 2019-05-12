#  A 10 minute primer to JavaScript modules, module formats, module loaders and module bundlers

최신의 JS 개발은 강력합니다.

프로젝트를 진행할 때 왜 최신의 도구와 툴들이 필요한지 궁금할 수 있습니다.

Webpack 과 SystemJS 와 같은 툴들은 무엇인가요 ? 또 AMD, UMD, CommonJS 는 무슨 의미인가요 ?
그것들은 무슨 연관이 있나요? 왜 필요로 한가요 ?

이 글에서는 다른 JS 모듈의 모듈의 형식, 로더 및 번들에 대하여 학습합니다.

다른 도구와 패턴들에 이해뿐만 아니라 최신 JS 개발에 대한 개념도 이해할 수 있을 것 입니다.

## What is a module?

모듈이란 재사용 할 수 있는 코드 조각입니다. 그것 세부 구현을 캡슐화하고 public api 을 노출하고 사용하기 쉽게 합니다.

## Why do we need modules?

모듈 없이 코드를 작성할 수 있습니다.

모듈패턴은 개발자가 60년대 70년대 이후 다양한 형태와 프로그래밍 언어로 사용해온 패턴입니다. 

JS 모듈은 다음과 같은 형태가 이상적입니다:

- 추상화: 라이브러리에 기능을 위임합니다. 구현의 복잡성을 이해할 필요 없습니다.
- 캡슐화: 모듈 내부의 코드를 숨깁니다.
- 재사용: 동일 코드의 반복을 피합니다.
- 의존성: 의존성을 쉽게 변경할 수 있습니다.

## Module patterns in ES5

ES5 이전 버전은 모듈을 염두하여 설계되지 않았습니다. 개발자들은 JS 에서의 모듈패턴을 구현하기위해 다양한 방법을 생각했습니다.

두 가지의 쉬운 패턴을 살펴보겠습니다. 
즉시 실행함수:

```js
(function(){
  // ...
})()
```

IIFE 는 선언 될 때 호출되는 익명 함수입니다.

JS 에서는 function 이라는 단어로 시작하는 줄을 함수 선언으로 판단합니다.

```js
// Function declaration
function(){  
  console.log('test');
}
```

즉시 함수를 호출하면 오류가 발생합니다.

```js
// Immediately Invoked Function Declaration
function(){  
  console.log('test');
}()

// => Uncaught SyntaxError: Unexpected token )
```

함수를 괄호로 감싸주면 함수 표현식이 됩니다.

```js
// Function expression
(function(){
  console.log('test');
})

// => returns function(){ console.log('test') }
```

함수 표현식은 함수를 반환하므로 즉시 호출이 가능해집니다.

```js
// Immediately Invoked Function Expression
(function(){
  console.log('test');
})()

// => writes 'test' to the console and returns undefined
```

즉시 함수를 부르면 다음과 같은 동작이 가능합니다.

IIFE 안에서 코드를 캡슐화합니다. IIFE 내에서 변수를 정의하기 때문에 전역 범위를 오염시키지 않습니다.

그러나, 종속성에 대한 관리를 제공해주지는 않습니다.

## Revealing Module

Revealing Module 패턴은 IIFE 와 유사 하지만 반환된 값을 변수에 할당합니다.

```js
// Expose module as global variable
var singleton = function(){

  // Inner logic
  function sayHello(){
    console.log('Hello');
  }

  // Expose API
  return {
    sayHello: sayHello
  }
}()
```

function 이라는 단어가 줄의 시작에 있지 않기 때문에 괄호가 필요 없습니다.
변수를 통하여 모듈에 접근할 수 있습니다.

```js
// Access module functionality
singleton.sayHello();  
// => Hello
```

singleton 대신 생성자 함수를 노출 할 수도 있습니다.

```js
// Expose module as global variable
var Module = function(){

  // Inner logic
  function sayHello(){
    console.log('Hello');
  }

  // Expose API
  return {
    sayHello: sayHello
  }
}
```

선언시 함수를 실행하지 않는지 주의해야합니다. 생성자 함수를 사용하여 모듈을 인스턴스화 합니다.

```js
var module = new Module();  
```

public api 에 접근하려면 아래와 같이 합니다.

```js
module.sayHello();  
// => Hello
```

IIFE 와 비슷한 이점을 제공합니다. 종속성 관리는 지원하지 않습니다.

JS 가 발전됨에 따라 많은 구문이 만들어집니다. 이는 각각 장점과 단점이 있습니다.

우리는 이것을 모듈 형식이라고 부릅니다.

## Module formats

모듈 형식이란 모듈을 정의하는데 사용할 수 있는 구문입니다.
ES6 이전에는 JS 에 정식 모듈 형식이 없었습니다.
개발자들은 다양한 형식으로 JS 에서 모듈을 정의했었습니다.

많이 채택되고 알려진 형식은 아래와 같습니다.

- Asynchronous Module Definition (AMD)
- CommonJS
- Universal Module Definition (UMD)
- System.register
- ES6 module format

각각의 구문을 간략하게 살펴보겠습니다.






