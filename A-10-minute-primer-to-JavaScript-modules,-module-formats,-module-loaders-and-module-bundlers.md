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

#### Asynchronous Module Definition (AMD)

AMD 형식은 브라우저에서 사용되며 정의 함수를 모듈을 정의합니다.

```js
//Calling define with a dependency array and a factory function
define(['dep1', 'dep2'], function (dep1, dep2) {

    //Define the module value by returning a value.
    return function () {};
});
```

#### CommonJS format

CommonJs 는 Node 에서 사용되며 require 및 module exports 를 사용하여 종속성과 모듈을 관리합니다.

```js
var dep1 = require('./dep1');  
var dep2 = require('./dep2');

module.exports = function(){  
  // ...
}
```

#### Universal Module Definition (UMD)

브라우저와 Node 에서 모두 사용할 수 있습니다.

```js
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
    // AMD. Register as an anonymous module.
      define(['b'], factory);
  } else if (typeof module === 'object' && module.exports) {
    // Node. Does not work with strict CommonJS, but
    // only CommonJS-like environments that support module.exports,
    // like Node.
    module.exports = factory(require('b'));
  } else {
    // Browser globals (root is window)
    root.returnExports = factory(root.b);
  }
}(this, function (b) {
  //use b in some fashion.

  // Just return a value to define the module export.
  // This example returns an object, but the module
  // can return a function as the exported value.
  return {};
}));
```

#### System.register

ES5 에서 ES6 모듈 구문을 지원하도록 설계되었습니다

#### ES6 module format

ES6 에서 JS 는 기본적으로 모듈 형식을 지원합니다. export token 을 이용하여 api 를 내보냅니다.

```js
// lib.js

// Export the function
export function sayHello(){  
  console.log('Hello');
}

// Do not export the function
function somePrivateFunction(){  
  // ...
}
```

export 된 모듈을 가져오기 위한:

```js
import { sayHello } from './lib';

sayHello();  
// => Hello
```

alias 를 이용하여 가져 올 수도 있습니다.

```js
import { sayHello as say } from './lib';

say();  
// => Hello
```

한번에 전체 모듈을 가져올 수도 있습니다.

```js
import * as lib from './lib';

lib.sayHello();  
// => Hello
```

default export 도 지원합니다.

```js
// lib.js

// Export default function
export default function sayHello(){  
  console.log('Hello');
}

// Export non-default function
export function sayGoodbye(){  
  console.log('Goodbye');
}
```

다음과 같이 가져올 수 있습니다.

```js
import sayHello, { sayGoodbye } from './lib';

sayHello();  
// => Hello

sayGoodbye();  
// => Goodbye
```

함수뿐만아니라 원하는 것을 내보낼 수 있습니다.

```js
// lib.js

// Export default function
export default function sayHello(){  
  console.log('Hello');
}

// Export non-default function
export function sayGoodbye(){  
  console.log('Goodbye');
}

// Export simple value
export const apiUrl = '...';

// Export object
export const settings = {  
  debug: true
}
```

ES6 의 모듈형식은 안타깝게도 모든 브라우저에서 지원되지 않습니다.
ES6 모듈 포맷을 사용하기 위해 ES5 문법으로 변환하기 위한 Babel 같은 변환기가 필요합니다.

## Module loaders

모듈 로더는 특정 모듈을 형식으로 작성된 모듈을 해석하고 읽습니다.

런타임에 모듈 로더가 실행됩니다.

- 브라우저에 모듈 로더를 로드합니다.
- 어떤 파일을 로드할 것인지 알려줍니다.
- 앱 파일을 다운로드하고 해석합니다.
- 필요한 경우 파일을 다운로드합니다.


브라우저의 개발자 콘솔에서 네트워크 탭을 보면 많은 파일이 필요에 따라 모듈 로더에 로드된다는 것을 알 수 있습니다.
많이 사용되어지는 모듈 로더는 아래와 같습니다.

- RequireJS: AMD 형식의 모듈 로더
- SystemJS: AMD, common JS, UMD 또는 System.gister 형식의 모듈 로더

## Module bundlers

모듈 번들러는 모듈 로더를 대체합니다.

모듈 로더와 달리 번들은 빌드시에 실행됩니다.

빌드할 때 모듈 번들을 실행하여 번들 파일을 생성합니다.
브라우저에서 번들파일을 로드합니다.
네트워크 탭을 확인하면 하나의 파일만 로드합니다.
브라우저에는 모듈 로더가 필요하지 않습니다.
모든 코드는 번들파일에 포함되어져있습니다.

많이 쓰이는 모듈 번들은 아래와 같습니다.

- Browserify: bundler for CommonJS modules
- Webpack: bundler for AMD, CommonJS, ES6 modules








