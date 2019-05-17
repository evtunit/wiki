```js
setTimeout(function doSomething() {
  // Do stuff.
}, 0);
```

0밀리초를 기다리는 이유는 무엇입니까? 그냥 직접 `doSomething`을 call하는 게 어때요? 

정답은 이 `setTimeout`이 Javascript 엔진에 0ms 이후 작업을 실행하도록 지시하지 않는다는 것입니다. 

오히려, Javascript 엔진에 최소 0ms 후에 `doSomething`을 실행하라고 지시합니다. 

이것은 Javascript의 작동 방식을 이해하기 위한 핵심 구분입니다. `doSomething`은 거의 즉시 실행되거나 몇 초 후에 실행될 수 있습니다. 

예를 들어, 다음 코드를 고려합니다.


```js
let startTime = Date.now();
setTimeout(function() {
  console.log(Date.now() - startTime);
}, 500);

while (Date.now() - startTime < 1000) {} // Pause for 1 second.
```

<br>

## Javascript’s stack frames
함수를 호출하면 "stack frame"이 생성됩니다.  stack frame은 함수에 선언된 모든 변수를 저장하는 컨테이너입니다. 

기능 실행이 완료되면 해당 stack frame이 삭제됩니다. 기능 내에서 함수를 호출하면 2개의 stack frame이 생성됩니다. 이것들은 스택에 놓입니다. 

첫 번째 함수에 의해 생성된 stack frame을 "initial frame"이라고 합니다. 이 기능은 다른 기능을 재귀적으로 호출할 수 있으며 처음에 하나의 기능만 호출한 후 수백만 개의 기능이 호출될 수 있습니다. 

모든 기능 실행이 완료되면 초기 프레임이 스택에서 제거됩니다. 현재 실행 중인 기능(i.e. none of your code)이 없습니다.

<br>

## Javascript’s message queue
모든 기능이 실행된 후 새 기능을 어떻게 호출합니까? 

메시지 큐에 추가하기만 하면 됩니다! Javascript에는 "메시지"의 대기열이 있습니다. 

각 메시지에는 단일 기능이 포함되어 있습니다. 스택이 비어 있으면(즉, 모든 기능이 실행되고 나면) Javascript 엔진은 메시지 큐가 비어 있는지 확인합니다. 

비어 있지 않으면 엔진이 첫 번째 메시지를 제거하고 내부 기능을 실행합니다. 

기능이 실행되면 새 스택 프레임(초기 프레임)이 생성되어 스택에 추가됩니다. 

기능 실행이 완료되면 초기 프레임이 스택에서 제거되고, Javascript 엔진은 메시지 큐에 다른 메시지가 있는지 확인합니다. 메시지 대기열이 비어 있을 때까지 이 작업이 반복됩니다.

<br>

## Adding to the message queue
이제 Javascript에는 "메시지"가 줄을 잇고 있습니다. 

Javascript는 메시지 큐가 비어 있을 때까지 각 메시지의 기능을 실행합니다. 애초에 어떻게 메시지가 메시지 큐에 있게되나요? 

메시지 큐에 추가하는 가장 일반적인 방법은 이벤트(DOM 요소, XMLHttpRequest, 서버 보낸 이벤트 등), setTimeout 및 setInterval입니다. 

지연 시간이 500ms인 setTimeout을 실행하면 Javascript 엔진에 500ms 내에 메시지 큐에 콜백 기능을 추가하라는 메시지가 표시됩니다. 

스택에 이미 실행 중인 기능도 있을 수 있습니다. 콜백 함수는 스택이 비어 있을 때까지 기다려야 실행할 수 있습니다. 

이것이 함수가 최소 500ms 후에 실행되는 이유입니다. 1000ms 동안 일시 중지한 위의 예에서 setTimeout 콜백은 실행되기 전에 1000ms – 500ms = 500ms 동안 메시지 대기열에 있어야 합니다. 이벤트는 또한 즉시 실행되는 대신 이벤트 대기열에 추가됩니다.

```js
let startTime = Date.now();
document.onclick = function() {
  console.log(Date.now() - startTime);
};

while (Date.now() - startTime < 1000) {} // Pause for 1 second.
```
루프가 순환하는 동안 웹 페이지를 클릭하면 클릭 처리기가 즉시 실행되지 않습니다. 대신 메시지 대기열에 추가됩니다. 

루프가 완료되면 Javascript 엔진이 Click handler를 실행합니다. `console.log`는 1000 또는 그 이상을 출력해야 합니다. 

이전 기능을 종료하고 클릭 핸들러를 시작하는 데 몇 밀리초가 걸릴 수 있기 때문에 약간 더 높을 수 있습니다.

<br>

## The event loop
이벤트 루프는 메시지 큐에 더 많은 메시지가 있는지 확인하는 루프를 나타냅니다. 높은 수준에서 루프는 다음과 같습니다.

```js
while (await messageQueue.nextMessage()) {
  let message = messageQueue.shift();
  message.run();
}
```

메시지 대기열에 메시지가 있는 경우 메시지 대기열에서 다음 메시지가 제거되고 관련 기능이 실행됩니다. 

그렇지 않으면 메시지 대기열에 새 메시지가 추가될 때까지 기다립니다. 이벤트 루프는 Javascript에서 비동기화를 허용하는 기본 모델입니다. 

Javascript는 단일 스레드이기 때문에 Javascript에서 이벤트 루프가 필요합니다. 

다중 스레드 언어에서 이벤트가 트리거되면 해당 이벤트 핸들러가 즉시 실행될 수 있습니다. 

이미 실행 중인 기능이 있는 경우 이벤트 처리기가 새 스레드에서 실행될 수 있습니다. 

웹 작업자를 사용하지 않는 한 Javascript에서는 불가능합니다. 

Javascript는 단일 스레드이기 때문에 이벤트 핸들러를 실행하기 전에 이전 기능이 실행될 때까지 기다려야 합니다. 

여러 이벤트 핸들러가 트리거된 경우 트리거된 순서대로 실행되어야 합니다. 이것이 Javascript가 메시지 큐와 이벤트 루프가 필요한 이유입니다. 

이벤트 루프는 처음에는 혼란스러울 수 있습니다. 어떻게 작동하는지 알아내기 위해 몇 가지 코드를 가지고 노는 것을 추천합니다. 불행하게도 (제가 아는 한) 어떤 브라우저에서도 메시지 큐를 볼 수 없기 때문에 이벤트 루프가 어떻게 작동하는지 유추해야 합니다. 

다음은 이벤트 루프를 보다 잘 이해하기 위해 재생할 수 있는 예제입니다.

```js
document.addEventListener('click', () => {
  setTimeout(console.log.bind(console, 'third'), 0);
  setTimeout(console.log.bind(console, 'fifth'), 100);
  console.log('first');
});

document.addEventListener('click', () => {
  setTimeout(console.log.bind(console, 'fourth'), 0);
  console.log('second');
});
```