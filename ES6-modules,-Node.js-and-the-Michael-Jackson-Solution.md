자바스크립트는 다른 파일로 기능을 가져오고 내보낼 수 있는 표준 방법을 가지고 있지 않다.<br>
음, 있다면 글로벌변수 정도? *예를 들자면:*
```js
<script src="https://code.jquery.com/jquery-1.12.0.min.js"></script><script>
// `$` variable available here
</script>
```

<br>

이상과는 먼 몇 가지 이유가 있다:
* 동일한 변수 이름을 사용하여 다른 라이브러리와 충돌할 수 있다. 그래서 많은 라이브러리가 [noConflict()](https://api.jquery.com/jquery.noconflict/) 방식을 가지고 있는 것이다.
* cyclic references를 할 수 없다. A모듈이 B모듈에 의존하거나 그 반대인 경우, 어떻게 스크립트를 붙이나요?
* <script> 태그를 붙이는 순서는 중요하고 유지하기가 어렵다.

---
<br>

## CommonJS to the rescue
Node.js와 다른 서버측 JavaScript 솔루션이 나타나기 시작했을 때, 문제를 해결하기 위해 CommonJS를 만들었다.<br>
가져오기/내보내기 문제와 관련하여, 이 규격은 런타임에 의해 주입되는 `require()` 함수와 내보내기 기능을 위한 내보내기 변수를 정의한다.

> Note: CommonJS만 규격은 아니다, UMD와 같은 다른 것들은 프런트엔드와 백엔드 둘 다 사용될 수 있다.

시간이 흐를수록 특히 한 페이지짜리 어플리케이션을 만들기 위한 도구들이 폭발적으로 늘어났다.<br>
프론트엔드에 더 큰 코드 베이스가 있고 프론트엔드와 백엔드 간에 코드를 공유해야 할 필요성 때문에, 
좀 더 편한 방법으로 개발하기 위해 웹팩과 같은 많은 툴이 CJS 규격을 구현하고 이해하기 시작했다.

브라우저가 `require()`를 구현하거나 `exports`를 내장하고 있지 않기 때문에 트렌스컴파일러가 하는 일은 이 기능을 구현하는 것이다.

---
<br>

## How ES6 modules work and why Node.js hasn’t implemented it yet

자바스크립트가 많은 변화가 일어나면서 생겼던 문제들이 있는데 ES6는 문제가 있던 부분들을 컴파일 해주게 된다.
ES modules는 어떻게 생겨나게 되었을까

*하지만 import를 어떻게 하는지 비교해보자면:*

```js
const { helloWorld } = require('./b.js') // CommonJS
import { helloWorld } from './b.js' // ES modules
```

이러한 방식으로 기능을 내보내는 방법:

```js
// CommonJS
exports.helloWorld = () => {
  console.log('hello world')
}
// ES modules
export function helloWorld () {
  console.log('hello world')
}
```

엄청 비슷하죠?

Node.js가 ES6를 구현한 지 오래지만, ES6 모듈 지원을 위해서는 2017년 말까지 기다려야 할 것이다. 그리고 런타임 뒤에서만 사용할 수 있을 것이다!<br>
ES6 모듈이 CJS와 비슷하다면 Node.js에서 구현하는 데 왜 그렇게 오래 걸리는가?

이 구문은 두 시스템 모두에서 매우 유사하지만 의미는 매우 다르다. 100% 호환되도록 특별한 노력이 필요한 edge 케이스가 있습니다.

ES 모듈이 Node.js에서 구현되지 않더라도 일부 브라우저에서는 이미 구현되어 있다. 예를 들어, 사파리 10.1에서 그것들을 테스트할 수 있다. 몇 가지 예를 보고 왜 중요한지 알아보자. 나는 이 세 개의 파일을 만들었다

```js
// index.html
<script type="module" src="./a.js"></script>
// a.js
console.log('executing a.js')
import { helloWorld } from './b.js'
helloWorld()
// b.js
console.log('executing b.js')
export function helloWorld () {
  console.log('hello world')
}
```

콘솔을 실행하면 표시되는 내용 그 결과는 다음과 같다.

```js
executing b.js
executing a.js
hello world
```

CJS를 사용하고 Node.js에서 실행하는 동일한 코드:

```js
// a.js
console.log('executing a.js')
import { helloWorld } from './b.js'
helloWorld()
// b.js
console.log('executing b.js')
export function helloWorld () {
  console.log('hello world')
}
```

다음과 같은 것을 줄 것이다.

```js
executing a.js
executing b.js
hello world
```

그래서,… 그것은 다른 순서로 코드를 실행했다. 이는 ES6 모듈을 먼저 구문 분석(실행하지 않고)한 다음 런타임에서 가져오기를 찾아 로드한 후 마지막으로 코드를 실행하기 때문이다. 이것을 비동기 부하라고 한다.

반면 Node.js는 코드를 실행하는 동안 종속성을 로드한다. 많은 경우에 이것은 아무런 차이를 만들지 않을 수도 있지만, 다른 경우에는 전혀 다른 행동이다.

Node.js와 웹브라우저는 이전 코드를 유지하는 새로운 로딩 방식을 구현할 필요가 있다. 언제 시스템을 사용해야 하는지 그리고 언제 다른 시스템을 사용해야 하는지 어떻게 알 수 있을까? 브라우저에서는 유형 속성과 함께 예에서 보았듯이 <script> 수준에서 지정하기 때문에 이 사실을 알고 있다.

```js
<script type="module" src="./a.js"></script>
```

하지만 Node.js는 어떻게 알까? 이것에 대해서 많은 논의가 있었고 (구문을 먼저 확인한 다음에 모듈로 취급해야 하는지 여부를 결정하면서) 규정하는 제안들이 많이 있었다(package.json file,...) 마지막으로 승인된 제안은 Michael Jackson 솔루션이었습니다. 기본적으로 파일을 ES6 모듈로 로드하려면 .js 대신 .mjs라는 다른 확장자를 사용하십시오.

> 확장명(.mjs)은 이를 Michael Jackson 부르는 이유다.

처음에는 좋지않은 결정으로 보였지만 지금은 최선의 해결책이라고 생각한다. 왜냐하면 그것은 쉽고 어떤 도구(텍스트 편집기, IDE, 전처리기)가 파일을 ES6 모듈로 처리해야 할지 말아야 할지 가장 쉬운 방법을 알 것이기 때문이다.
