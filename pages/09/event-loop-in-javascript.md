
# 자바스크립트의 이벤트 루프

> 원문: [Event loop in javascriptㄷ](https://code.likeagirl.io/what-the-heck-is-event-loop-1e414fccef49)

자바스크립트를 좋아한다면 이걸 배워야 한다. 자바스크립트의 깊은 측면 중 하나인 이벤트 루프이다. 많은 개발자들이 수년간 일하면서도 여전히 이벤트 루프를 이해하지 못한다. 그러나 이 게시물을 통해 이벤트 루프가 무엇인지, 그리고 실제로 그렇게 복잡하지는 않다는 것을 알려주고 싶다.

## 크롬 V8엔진은 자바스크립트 코드를 어떻게 처리할까?

애플리케이션의 자바스크립트 코드는 한번에 한줄 씩 처리된다. 이는 자바스크립트가 단인 스레드임을 의미한다.

반환하는데 정말로 오랜 시간이 걸리는 자바스크립트 줄은 그 후에 작성된 코드가 실행되는 것을 막는다.

웹 사이트에서 버튼을 클릭했는데, 그냥 멈추는 경우를 상상해보라. 다른 버튼을 클릭해보지만 아무일도 일어나지 않는다. 버그가 없는 것으로 가정했을 때, 이 현상의 가장 큰 원인은 버튼 클릭으로 실행되는 일부 자바스크립트 코드가 트리거되지만 실행되지 않는 것이다.

*콜 스택*은 쇼핑백과 같다. 쇼핑백에 넣은 첫번째 물건은 나갈때는 맨 마지막에 나가게 된다.  자바스크립트 엔진은 모든 라인을 콜 스택에 추가하여 LIFO(Last in First Out) 방식으로 실행한다. 요컨대 콜스택은 우리가 프로그램에 있는 위치를 기록하고 식별하는 데이터 구조이다.

그렇다면 엔진이 어떻게 자바스크립트 코드 라인을 한번에 하나씩 처리할까?

그 답은 콜 스택을 사용하기 때문이다.

*콜 스택*을 엘레베이터에 타는 것이라고 생각하자. 엘리베이터에 첫번째로 탄 사람은 엘리베이터에서 내린땐 마지막으로 내리지만 마지막으로 탄 사람은 첫번째로 내린다.

예제를 보자.

```js
var firstEventLoop = function () {
  console.log("Yo people i am first one!!");
};
var secondEventLoop = function () {
  firstEventLoop();
  console.log("Yo people i am second one!!");
};
secondEventLoop();
```

위의 예제가 콜 스택에서 어떻게 동작하는지 보자. 먼저 메인 함수(익명 함수)은 콜 스택에 push되어 진다. (아래의 그림처럼)

[[/resource/yongkwan/09/01.png]]

그  다음  `secondEventLoop` 함수가 실행 될 것이다.
`secondEventLoop`가 호출되면, 콜스택은 아래처럼 구성되어진다.

[[/resource/yongkwan/09/02.png]]

`secondEventLoop` 실행은 `firstEventLoop` 함수의 실행을 발생시키고, 
`firstEventLoop` 함수가 실행되면 콜스택은 아래처럼 구성된다.

[[/resource/yongkwan/09/03.png]]

firstEventLoop 함수가 실행되면 콘솔 로그가 찍힌다.(`console.log("Yo people i am first one!!");`) 
그럼 이제 firstEventLoop 안에 더 이상 실행할 코드가 없으므로 firstEventLoop 함수는 콜스택에서 제거된다.

[[/resource/yongkwan/09/04.png]]

secondEventLoop 함수가 계속해서 실행되고, secondEventLoop 함수의 콘솔 로그가 찍힌다.(`console.log("Yo people i am second one!!");`) 
마찬가지로 이제 더 이상 실행할 코드가 없으므로 secondEventLoop 함수는 콜스택에서 제거된다.

[[/resource/yongkwan/09/05.png]]

마지막으로 더 이상 실행할 코드가  없으므로 이 예제의 익명 함수는 콜스택에서 사라질 것이다.

자 계속해서 알아가보자.

우리는 setTimeout 함수에 익숙하다. setTimeout 함수에서 함수를 호출할 수 있다. 호출되는 함수는 비동기 콜백 함수이다.

아래의 예를 보자.

```js
function printMe1 () {
  console.log("Hello Javascript");
};
function printMe2(){
  setTimeout(
    printMe1(),
    2000
  );
  console.log("Hey I am the first to be printed");
}
printMe2();
```

위의 예제를 실행하면 콘솔에 "`Hey I am the first to be printed`" 문자열이 먼저 찍힐 것이고, 그 다음에 "`
Hello Javascript`"가 찍힐 것이다.

이제 위의 코드에서 콜 스택에서 진행중인 작업을 확인해보겠다.

[[/resource/yongkwan/09/06.png]]

printMe1을 콜백 함수로 가지는 setTimeout 후 발생할 일들은 콜스택 안에 있다.
printMe1 함수가 즉시 실행되지 않고 2 초 후에 실행되므로, printMe1 함수는 호출 스택에 푸시 할 수 없다. 따라서 호출 스택에서 사라진다.

콜스택은 아래와 같아질 것이다.:

[[/resource/yongkwan/09/07.png]]

printMe2()가 계속해서 실행 될 것이며 콘솔에 문자열이 출력되고 콜스택에서 제거 될 것이다. 이제 더 이상 콜스택 안에 실행할 항목이 ㅇ없기 때문에 익명함수도 콜스택에서 사라질 것이다.

따라서 콜스택은 아래와 처럼 빈 상태가 된다.:

[[/resource/yongkwan/09/08.png]]

2초 뒤 마법처럼 printMe2 함수가 콜스택에 나타날 것이다.

[[/resource/yongkwan/09/09.png]]

printMe2 함수는 "`Hello javascript`" 를 콘솔에 출력한다. 아마도 어떻게 이렇게 동작되는지 놀랄지도 모른다.
여기서 이벤트 루프의 동시성(concurrency)이 드러난다.

[[/resource/yongkwan/09/10.png]]
[[/resource/yongkwan/09/11.png]]
[[/resource/yongkwan/09/12.png]]
[[/resource/yongkwan/09/13.png]]
[[/resource/yongkwan/09/14.png]]