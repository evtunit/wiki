# The JavaScript Event Loop

이벤트 루프는 자바 스크립트를 이해하는 데 가장 중요한 요소 중 하나입니다. 이 게시물은 간단한 용어로 설명합니다.

번역 : [https://flaviocopes.com/javascript-event-loop/](https://flaviocopes.com/javascript-event-loop/)

## Introduction

이벤트 루프는 자바 스크립트를 이해하는 데 가장 중요한 요소 중 하나입니다.

> 필자는 JavaScript로 수년간 프로그래밍 해왔지만, 후드에서 어떻게 작동하는지 완전히 이해하지 못했습니다. 이 개념을 자세히 알지 못하는 것은 완전히 멋지지만 평소와 같이 어떻게 작동하는지 알면 도움이됩니다. 또한이 시점에서 좀 궁금해 할 수도 있습니다.

이 글은 자바 스크립트가 단일 스레드로 어떻게 작동하는지, 그리고 비동기 함수를 처리하는 방법에 대해 설명합니다.

JavaScript 코드는 단일 스레드로 실행됩니다. 한 번에 한 가지 일이 발생합니다.

이는 동시성 문제에 대해 걱정하지 않고 프로그래밍하는 방법을 단순화하므로 실제로 매우 유용합니다.

코드 작성 방법에 주의를 기울여야하며 동기식 네트워크 호출이나 무한 루프와 같이 스레드를 차단할 수있는 요소는 피하십시오.

일반적으로 모든 브라우저 탭에 대한 이벤트 루프가있어 모든 프로세스를 격리하고 무한 루프 또는 무거운 처리가있는 웹 페이지가 전체 브라우저를 block 시키는 것을 피할 수 있습니다.

이 환경은 여러 개의 동시성(concurrent) 이벤트 루프를 관리합니다. 예를 들어 API 호출을 다룹니다. 웹 워커는 자체 이벤트 루프에서 실행됩니다.

주로 코드가 단일 이벤트 루프에서 실행되고 코드를 차단하지 않도록이 코드를 염두에 두어야합니다.

## Blocking the event loop

이벤트 루프로 컨트롤을 되돌리기 위해 너무 오래 걸리는 JavaScript 코드는 페이지의 모든 JavaScript 코드 실행을 차단하고 UI 스레드를 차단하며 사용자는 클릭하거나 페이지를 스크롤 할 수 없습니다.

JavaScript의 거의 모든 I / O 프리미티브는 비 블로킹입니다. 네트워크 요청, Node.js 파일 시스템 작업 등. 이런 블로킹이 예외이므로 JavaScript는 콜백에 기반하고 있으며, 최근에는 [promises](https://flaviocopes.com/javascript-promises/) 및 [async/await](https://flaviocopes.com/javascript-async-await/) 에 기반합니다.

## The call stack

호출 스택은 LIFO 대기열 (마지막 들어가고, 첫번째가 나온다.)입니다.

이벤트 루프는 호출 스택을 계속 검사하여 실행해야하는 function이 있는지 확인합니다.

그렇게하는 동안 호출 스택에 찾은 모든 함수 호출을 추가하고 각각을 순서대로 실행합니다.

디버거 또는 브라우저 콘솔에서 익숙한 오류 스택 추적을 알고 있습니까? 브라우저는 호출 스택에서 함수 이름을 찾아 어떤 함수가 현재 호출을 시작했는지 알려줍니다.

![https://flaviocopes.com/javascript-event-loop/exception-call-stack.png](https://flaviocopes.com/javascript-event-loop/exception-call-stack.png)

## A simple event loop explanation

예제를 선택해 보겠습니다.

> 나는`foo`,`bar`와`baz`를 _random names_로 사용합니다. 그들을 대체 할 이름을 입력하십시오

```js
const bar = () => console.log('bar')

const baz = () => console.log('baz')

const foo = () => {
  console.log('foo')
  bar()
  baz()
}

foo()
```

프린트 된거는 아래와 같다.

```
foo
bar
baz
```
이렇게 될것이다.

이 코드가 실행되면, 먼저`foo ()`가 호출됩니다. `foo ()`내부에서 먼저`bar ()`를 호출 한 다음`baz ()`를 호출합니다.

이 시점에서 호출 스택은 다음과 같습니다.

![https://flaviocopes.com/javascript-event-loop/call-stack-first-example.png](https://flaviocopes.com/javascript-event-loop/call-stack-first-example.png)


모든 반복에서 이벤트 루프는 호출 스택에 무언가가 있는지 찾아서 실행합니다.

![https://flaviocopes.com/javascript-event-loop/execution-order-first-example.png](https://flaviocopes.com/javascript-event-loop/execution-order-first-example.png)

콜 스택이 비어질때 까지 실행된다.

## Queuing function execution

위의 예는 정상적으로 보입니다. 특별한 것은 없습니다. JavaScript는 실행해야 할 것을 찾고, 순서대로 실행합니다.

스택이 깨끗해질 때까지 함수를 연기하는 방법을 살펴 보겠습니다.

`setTimeout (() => {}), 0)의 유스 케이스는 함수를 호출하는 것이지만, 코드의 다른 모든 함수가 실행되면 함수를 호출하는 것이다.

이 예제를 보자.

```js
const bar = () => console.log('bar')

const baz = () => console.log('baz')

const foo = () => {
  console.log('foo')
  setTimeout(bar, 0)
  baz()
}

foo()

```

이 코드는 아마 놀랍게 인쇄 할 수 있습니다.

```
foo
baz
bar
```

이 코드가 실행되면 먼저 foo ()가 호출됩니다. foo () 내부에서 우리는 먼저 `bar`를 인수로 전달하여 setTimeout을 호출하고 타이머가 0이 될 때까지 가능한 한 빨리 실행하도록 지시합니다. 그런 다음 baz ()를 호출합니다.

이 시점에서 호출 스택은 다음과 같습니다.

![https://flaviocopes.com/javascript-event-loop/call-stack-second-example.png](https://flaviocopes.com/javascript-event-loop/call-stack-second-example.png)

다음은 프로그램의 모든 함수에 대한 실행 순서입니다.

![https://flaviocopes.com/javascript-event-loop/execution-order-second-example.png](https://flaviocopes.com/javascript-event-loop/execution-order-second-example.png)

왜 이런 일이 발생할까요?

## The Message Queue

setTimeout ()이 호출되면 Browser 또는 Node.js가 타이머를 시작합니다. 타이머가 만료되면이 경우 즉시 0으로 설정하여 콜백 함수가 Message Queue에 저장됩니다.

Message Queue는 클릭 이벤트 나 키보드 이벤트 또는 응답 가져 오기와 같은 사용자가 시작한 이벤트가 코드에 응답 할 기회가 오기 전에 대기합니다. 또는 onLoad와 같은 DOM 이벤트.

루프는 호출 스택에 우선 순위를 부여하고 호출 스택에서 찾은 모든 것을 먼저 처리하고 거기에 아무 것도 없으면 메시지 대기열에서 항목을 선택합니다.

우리는 setTimeout, fetch 또는 다른 것들을 브라우저가 제공하기 때문에 자신의 작업을 수행 할 때까지 기다릴 필요가 없습니다. 예를 들어, setTimeout 시간 초과를 2 초로 설정하면 2 초를 기다릴 필요가 없습니다. 대기는 다른 곳에서 발생합니다.

## ES6 Job Queue

ECMAScript 2015는 Promises가 사용하는 작업 대기열 개념을 도입했습니다 (ES6 / ES2015에서도 소개 됨). 이것은 호출 스택의 끝에 놓기보다는 가능한 한 빨리 비동기 함수의 결과를 실행하는 방법입니다.

현재 함수가 끝나기 전에 해결되는 약속은 현재 함수 바로 다음에 실행됩니다.

나는 유원지에서 롤러 코스터 타기의 비유를 잘 찾았습니다. 메시지 큐는 대기열의 뒤쪽에, 다른 모든 사람들 뒤에서 당신을 기다립니다. 당신의 차례를 기다려야하며, 작업 대기열은 패스트 패스 티켓입니다 당신이 이전의 것을 끝내 자마자 당신은 다른 타는 것을 곧 취할 수있다.

```js
const bar = () => console.log('bar')

const baz = () => console.log('baz')

const foo = () => {
  console.log('foo')
  setTimeout(bar, 0)
  new Promise((resolve, reject) =>
    resolve('should be right after baz, before bar')
  ).then(resolve => console.log(resolve))
  baz()
}

foo()
```

출력은 다음과 같다

```
foo
baz
should be right after baz, before bar
bar
```

이는 promises (약속에 기반한 Async / await)와 setTimeout () 또는 다른 플랫폼 API를 통한 평범한 오래된 비동기 함수의 큰 차이점입니다.