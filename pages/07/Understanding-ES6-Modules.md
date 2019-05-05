# ES6 Modules의 이해

원문 : https://www.sitepoint.com/understanding-es6-modules/#usinges6modulesinnodejs

**이 글은 ES6 Module을 탐구하여, ES6 Module이 어떻게 오늘날 트랜스파일러의 도움을 받아 사용할 수 있는지를 보여줍니다.**

거의 대부분의 언어는 한 파일에 선언된 기능을 다른 파일에 포함시키는 방법인 Module 개념을 가지고 있습니다.

일반적으로 개발자는 각각의 역할들과 관련된 기능을 하는 캡슐화된 코드의 라이브러리들을 만들어냅니다.  
(Typically, a developer creates an encapsulated library of code responsible for handling related tasks. > 의역)
이들 라이브러리는 애플리케이션 혹은 다른 Module에 의해 참조되어집니다.

장점 :

1. 코드는 작은 파일들로 분할될 수 있습니다.
1. 어느 애플리케이션에서(애플리케이션의 어디든지) 동일한 Module을 공유할 수 있습니다.
1. 이상적으로, Module들은 다른 개발자들에 의해 검증될 필요가 없습니다. 왜냐하면 Module들의 기능이 입증되었기 때문입니다.(일부 의역 : works을 기능으로)
1. Module을 참조하는 코드는 의존성을 이해합니다. Module 파일이 변경되거나 이동되면 즉시 문제가 됩니다.
1. Module 코드는 (보통) 네이밍 중복을 줄이는데 도움을 줍니다. module1에서의 함수 x()는 module2에서 함수와 충돌이 되지 않습니다.  네임스페이스와 같은 옵션들이 사용되어서 ```module1.x()``` 와 ```module2.x()```이 됩니다.

## Javascript에서 Module은 어디에 있습니까?
몇 년 전에 웹 개발을 시작하는 누군가는 Javascript에 Module 개념이 없다는 사실을 발견한 것에 대해 충격을 받았습니다. 하나의 Javascript 파일을 다른 파일에 직접 참조하거나 포함하는 것을 불가능했습니다.

### 다중 HTML ```<script>``` 태그
HTML은 다중 ```<script>```태그를 사용하여 여러 개의 Javascript 파일들을 읽을 수 있습니다. (일부 의역)
```html
<script src="lib1.js"></script>
<script src="lib2.js"></script>
<script src="core.js"></script>
<script>
console.log('inline code');
</script>
```
[average web page in 2018 uses 25 separate scripts](https://httparchive.org/reports/state-of-the-web#bytesJS&reqJS)은 아직 실용적인 솔루션이 아닙니다.

각각 스크립트는 페이지 성능에 영향을 주는 새로운 HTTP 요청을 시작합니다.

[HTTP/2](https://www.sitepoint.com/what-is-http2/)는 이 문제를 어느 정도 줄여주지만 , CDN과 같은 다른 도메인에서 참조되는 스크립트에는 도움이 되지 않습니다. 

모든 스크립트는 실행되는 동안 더 이상의 처리를 진행하지 않습니다.
의존성 관리는 수동적으로 이루어집니다. 
위의 코드에서 만약 ```lib1.js```가 ```lib2.js``` 의 코드를 참조했다면, 코드가 로드되지 않았기 때문에 실패하게 됩니다. 그러면 Javascript 처리가 추가로 일어나지 않습니다.

적절하게 [module patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#designpatternsjavascript)을 사용하지 않는 한 함수는 다른 함수를 재정의할 수 있습니다.

초기 Javascript 라이브러리는 글로벌 함수 이름을 사용하거나 네이티브 메소드를 재정의하는 것으로 악명이 높았습니다.

### 스크립트와 연결
다중의 ```<script>``` 태그의 문제를 해결하는 한 가지 해결책은 모든 Javascript 파일을 하나의 큰 파일로 연결하는 것입니다.

이로 인해 일부 성능 및 의존성 관리 문제가 해결되지만 수동 구축 및 테스트 단계가 발생할 수 있습니다. 

### Module 로더
[RequireJS](https://requirejs.org/) 그리고 [SystemJS](https://github.com/systemjs/systemjs)와 같은 시스템은 런타임에 다른 Javascript 라이브러리를 로드하고, 네임스페이스를 지정할 수 있는 라이브러리를 제공합니다.

필요한 경우에 Ajax 메소드를 사용하여 로드합니다. 시스템은 도움이 되지만 ```<script>``` 태그를 더 큰 코드 베이스 또는 사이트에 추가하면 복잡해질 수 있습니다. 

### Module 번들러, 전처리 및 트랜스파일러

번들러는 컴파일 단계를 도입하므로 빌드 시에 Javascript 코드가 생성됩니다.
코드는 의존성을 포함하도록 처리되고 어느 브라우저에서 호환이 되도록 하나로 연결된 ES5 파일을 생성하기 위해 처리됩니다.(Code is processed to include dependencies and produce a single ES5 cross-browser compatible concatenated file.> 의역). 인기 있는 옵션으로는 [Babel](https://babeljs.io/), [Browserify](http://browserify.org/), [webpack](https://webpack.github.io/) 및 [Grunt](https://gruntjs.com/) and [Gulp](https://gulpjs.com/)와 같은 task runner가 있습니다.

Javascript 빌드 프로세스는 약간의 노력을 해야 하지만 장점이 있습니다.

- 처리가 자동화되어 있어서 사람이 실수할 수 있는 가능성이 작습니다.
- 추가적인 처리는 코드를 삽입하고, 디버깅 명령들을 제거하며, 최종 결과의 파일을 압축하는 등의 작업을 수행할 수 있습니다. (minify => 압축으로 의역) 
- [TypeScript](https://www.typescriptlang.org/) 또는[CoffeeScript](http://coffeescript.org/)와 같은 대체 문법을 사용할 수 있습니다.

## ES6 Module
위의 옵션은 다양한 competing module definition 형식이 도입되었습니다. 널리 채택된 문법은 이와 같습니다. 

- CommonJS — Node.js에서 ```module.exports```와 ```require```문법 사용
- Asynchronous Module Definition (AMD)
- Universal Module Definition (UMD).

따라서 단일, 네이티브 Module 표준은 ES6에서 제안되었습니다.

모든 ES6 Module의 내부는 기본적으로 private이고, strict 모드에서 실행이 됩니다. (```'use strict'``` 코드를 필요로 하지 않습니다.)

public 변수, 함수, 그리고 클래스는 ```export```을 사용해서 Module에 내보내집니다. (일부 의역)

예를 들어

```javascript
// lib.js
export const PI = 3.1415926;

export function sum(...args) {
  log('sum', args);
  return args.reduce((num, tot) => tot + num);
}

export function mult(...args) {
  log('mult', args);
  return args.reduce((num, tot) => tot * num);
}

// private function
function log(...msg) {
  console.log(...msg);
}
```

하나의```export```문으로도 사용하여 Module로 변수, 함수, 클래스들을 내보낼 수 있습니다.
(일부 의역)

예를 들어

```javascript
// lib.js
const PI = 3.1415926;

function sum(...args) {
  log('sum', args);
  return args.reduce((num, tot) => tot + num);
}

function mult(...args) {
  log('mult', args);
  return args.reduce((num, tot) => tot * num);
}

// private function
function log(...msg) {
  console.log(...msg);
}

export { PI, sum, mult };
```

```import```는 Module에서 다른 스크립트나 Module로 다른 대상을 가지고 오는 데 사용합니다.

```javascript
// main.js
import { sum } from './lib.js';

console.log( sum(1,2,3,4) ); // 10
```

이와 같은 케이스는 ```lib.js```는 ```main.js```와 같은 폴더에 있습니다. 
파일 경로는 절대 파일 참조(```/```로 시작), 상대 파일 참조 (```./``` 또는 ```../```) 또는 전체 URL을 사용할 수 있습니다. (일부 의역)

한 번에 여러 대상을 가져올 수 있습니다.

```javascript
import { sum, mult } from './lib.js';

console.log( sum(1,2,3,4) );  // 10
console.log( mult(1,2,3,4) ); // 24
```

```import```는 별칭(aliase)을 사용하여 네이밍 충돌을 해결할 수 있습니다.(일부 의역)

```javascript
import { sum as addAll, mult as multiplyAll } from './lib.js';

console.log( addAll(1,2,3,4) );      // 10
console.log( multiplyAll(1,2,3,4) ); // 24
```

마지막으로, 네임스페이스를 제공함으로써 모든 공용의 대상들을 가져올 수 있습니다.

```javascript
import * as lib from './lib.js';

console.log( lib.PI );            // 3.1415926
console.log( lib.add(1,2,3,4) );  // 10
console.log( lib.mult(1,2,3,4) ); // 24
```

## 브라우져에서 ES6 Module을 사용 하는 방법
해당 글을 작성 당시에 [ES6 Module의 지원범위](https://caniuse.com/#feat=es6-module)는 크롬 기반 브라우져(v63+), Safari 11+, Edge 16+, Firefox 60(v58+의 ```about:config``` 플래그 뒤에 있음) 이었습니다.

Module을 사용하는 스크립트는 ```<script>``` 태그에 ```type="module"``` 속성을 설정하여 로드 해야 합니다.

예를 들자면,

```html
<script type="module" src="./main.js"></script>
```
또는 인라인으로

```html
<script type="module">
  import { something } from './somewhere.js';
  // ...
</script>
```

Module은 페이지 또는 다른 Module에서 참조되는 Module에서 횟수에 상관없이 한 번 파싱이 됩니다.

### 서버에서의 고려사항
Module는 MIME 타입으로 ```application/javascript```이 함께 제공되어야 합니다. 대부분의 서버는 자동으로 MIME 타입으로 ```application/javascript```이 함께 제공하는 작업을 수행하지만, 동적으로 생성된 스크립트나 ```.mjs``` 파일에서는 주의하세요. (하단의 Node.js 섹션에서 볼 수 있습니다.)

일반적으로 ```<script>``` 태그가 다른 도메인에서 스크립트를 fetch 할 수 있지만, Module은 CORS을 사용하여 스크립트가 fetch 되어집니다. 

따라서 서로 다른 도메인 Module은 ```Access-Control-Allow-Origin: *```와 같은 적절한 HTTP 헤더를 설정해야 합니다.

끝으로, Module은 ```crossorigin="use-credentials"``` 속성이 ```<script>```에 추가되고 응답에 ```Access-Control-Allow-Credentials: true``` 헤더가 포함되어 있지 않는다면 쿠키나 다른 헤더 자격 증명을 보내지 않을 것입니다.

### Module 실행 지연
```<script defer>``` 속성은 문서에 로드되고 파싱 될 때까지 스크립트 실행을 지연시킵니다.
인라인으로 스크립트를 포함한 Module은 기본적으로 defer입니다.

예를 들자면 

```html
<!-- runs SECOND -->
<script type="module">
  // do something...
</script>

<!-- runs THIRD -->
<script defer src="c.js"></script>

<!-- runs FIRST -->
<script src="a.js"></script>

<!-- runs FOURTH -->
<script type="module" src="b.js"></script>
```

### Module [Fallback](https://terms.naver.com/entry.nhn?docId=751891&cid=50324&categoryId=50324)

Module 지원이 없는 브라우저는 ```type="module"``` 스크립트를 실행하지 않습니다. fallback 스크립트는 ```nomodule``` 속성으로 Module 호환 브라우저에서 무시할 수 있게 제공할 수 있습니다. (A fallback script can be provided with a ```nomodule``` attribute which module-compatible browsers ignore. > 의역)

예를 들면

```html
<script type="module" src="runs-if-module-supported.js"></script>
<script nomodule src="runs-if-module-not-supported.js"></script>
```

### 브라우저에서 Module을 사용해야 한다면?
브라우저 지원 범위는 증가하고 있지만 ES6 Module로 전환하는 것은 아직 시기상조 일 수 있습니다. 현재로써는 Module 번들러를 사용해서 어디에서나 작동하는 스크립트를 만드는 것이 더 나을 것입니다.

## Node.js 에서 ES6 Module 사용
2009년 Node.js가 출시되었을 때 Module을 제공하지 않는 런타임은 상상할 수 없었을 것입니다.
CommonJS가 채택되었는데, 이는 노드 패키지 매니저인 npm이 개발될 수 있다는 것을 의미했습니다. 그 시점부터 사용량이 기하급수적으로 증가했습니다.

CommonJSModule은 ES2015 Module과 유사한 방법으로 코드화 할 수 있습니다.

```module.exports``` is used rather than ```export```:

```javascript
// lib.js
const PI = 3.1415926;

function sum(...args) {
  log('sum', args);
  return args.reduce((num, tot) => tot + num);
}

function mult(...args) {
  log('mult', args);
  return args.reduce((num, tot) => tot * num);
}

// private function
function log(...msg) {
  console.log(...msg);
}

module.exports = { PI, sum, mult };
```

```require```(```import``` 보다)은 이 모듈을 다른 스크립트 또는 모듈로 끌어오기 위해 사용됩니다.

```javascript
const { sum, mult } = require('./lib.js');

console.log( sum(1,2,3,4) );  // 10
console.log( mult(1,2,3,4) ); // 24
```

```require```은 모든 대상을 가지고 올 수 있습니다.

```javascript
const lib = require('./lib.js');

console.log( lib.PI );            // 3.1415926
console.log( lib.add(1,2,3,4) );  // 10
console.log( lib.mult(1,2,3,4) ); // 24
```

그래서 ES6 Module은 Node.js에서 쉽게 구현이 가능했습니다. 맞나요? Er, 아니.

ES6 Module은 [Node.js 9.8.0+](https://nodejs.org/api/esm.html) 플래그 뒤에 있으며, 적어도 버전 10까지는 완전히 구현되지 않을 것입니다.
CommonJS와 ES6 Module은 유사한 문법을 사용하지만 근본적으로 다른 방식으로 작동합니다.

- ES6 module은 코드가 실행되기 전에 좀 더 많은 import를 resolve하기 위해 미리 구문을 파싱 합니다. (ES6 modules are pre-parsed in order to resolve further imports before code is executed. > 의역)

- CommonJS module은 코드를 실행하는 동안 필요에 따라 의존성을 로드합니다.

위의 예에서는 아무런 차이가 없었지만, 다음 ES2015 module 코드를 봐주세요. (일부 의역)

```javascript
// ES2015 modules

// ---------------------------------
// one.js
console.log('running one.js');
import { hello } from './two.js';
console.log(hello);

// ---------------------------------
// two.js
console.log('running two.js');
export const hello = 'Hello from two.js';
```

ES2015의 결과 코드

```
running two.js
running one.js
hello from two.js
```

CommonJS을 사용하여 작성한 비슷한 코드

```javascript
// CommonJS modules

// ---------------------------------
// one.js
console.log('running one.js');
const hello = require('./two.js');
console.log(hello);

// ---------------------------------
// two.js
console.log('running two.js');
module.exports = 'Hello from two.js';
```

ES2015의 결과 코드

```
running one.js
running two.js
hello from two.js
```

일부 애플리케이션에서는 실행 순서가 중요할 수 있으며, ES2015 and CommonJS Module이 같은 파일에서 혼합된 경우에는 어떻게 되겠습니까? 이 문제를 해결하기 위해 Node.js는 확장자가 ```.mjs```인 Module만 허용합니다.  

```.js``` 확장자를 가진 파일은 CommonJS로 기본 설정이 됩니다. 이 설정은 복잡성을 많이 제거하고 코드 에디터 그리고 코드 linter를 돕는 간단한 옵션입니다. 

### Node.js에서 ES6 module을 사용 해야 한다면?
ES6 module은  Node.js v10부터만 사용할 수 있습니다(2018년 4월 출시). 기존 프로젝트를 변환해도 장점이 없으며 이전 버전의 Node.js와 호환되지 않는 애플리케이션이 됩니다.

새로운 프로젝트의 경우 ES6 module은 CommonJS의 대안을 제공합니다. 문법은 클라이언트 측 코딩과 동일하며, 브라우저나 서버에서 실행될 수 있는 isomorphic Javascript([동형 자바스크립트](https://love2dev.com/blog/what-is-isomorphic-javascript-and-when-should-it-be-used/))에 대한 쉬운 경로(접근 방법)를 제공할 수 있습니다. (The syntax is identical to client-side coding, and may offer an easier route to isomorphic Javascript, which can run in the either the browser or on a server....어떻게 번역해야할까...)

## Module Melee(Melee:영어 뜻으로 혼잡, 난잡, 난투...;)
표준화된 Javascript module 시스템으로 되기까지 수년이 걸렸으며, 구현까지 더 오래 걸렸지만, 문제는 해결되었습니다. 2018년 중반의 모든 주류 브라우저와 Node.js는 ES6 module을 지원하지만, 모든 사용자가 업그레이드하는 동안 switch-over 지연이 예상됩니다.

지금 ES6 module을 배우면 미래에 Javascript 개발에 도움이 됩니다. 
(Learn ES6 modules today to benefit your Javascript development tomorrow.>의역)