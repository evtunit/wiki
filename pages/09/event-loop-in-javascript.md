
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

우리는 이전에 자바스크립트 런타임이 한 번에 한 가지 일을 할 수 있다고 논의했지만, 지금은 동시성에 대해 이야기하고 있다. 자바스크립트 **런타임은 한 번에 한 가지만 할 수 있고**,  다른 코드를 실행하는 동안에는 Ajax 호출을 할 수 없으며, 다른 코드를 실행하는 동안에는 setTimeout 할 수 없지만 **브라우저를 통해 이 모든 것을 할 수 있다.** 브라우저를 사용하면 다른 코드를 실행하는 동안 Ajax 호출을 할 수 있으며 다른 코드를 실행하는 동안 setTimeout을 할 수도 있다.

setTimeout을 하고 Ajax 요청을 하는 것은 Javascript 런타임에 있는 것이 아니라 브라우저가 웹 API로 처리한다. 브라우저는 이러한 것들의 처리과정에 대해 알고 있으며 그것의 콜백 함수를 통해 결과를 얻을 수 있다.

이벤트 루프는 모든 요청마다 새 스레드가 생성되는 스레드 모델과 달리 단일 스레드에서 실행되므로 오버헤드가 발생하지 않는다.

서로다른 일들이 같은 쓰레드에서 일어나고 있기 때문에 차단할 수 없다. 이것은 모든 쓰레드를 차단할 수 있기 때문에 어떤 일이 일어나기를 기다릴 수 없다는 것을 의미합니다. 대신 작업이 완료되면 호출되는 콜백을 정의하면 된다. 이를 보통 non-blocking I/O라고 한다.

blocking I/O에 대한 예: 

```
row = db_query(‘SELECT * FROM users’);

print(row);

print(‘It will print after user result print’);
```

위의 코드에서 실행은 db 쿼리의 결과를 반환할때  때까지(row) 일시 중지되고 그 다음 결과가 반환되면 나머지 코드가 실해된다. 따라서 먼저 row을 출력한 다음(사용자 질의 결과) ‘It will print after user result print' 문자열을 출력한다.

이제 같은 예시지만 약간의 변화를 볼 수 있다.
non-blocking I/O에 대한 예:

```
db_query(‘SELECT * FROM users’, function (row) {

  print(row);

});

print(‘It will print after user result print’);
```

위의 예에서는 항상 Javascript에서 사용했던 것과 같은 익명 함수를 사용한다. JS는 이벤트를 많이 활용하는데, 그것이 바로 콜백에 관한 것이다. 작업이 완료되면 콜백을 트리거하는 이벤트가 실행된다. 이것이 종종 이벤트 모델 또는 비동기 모델이라고도하는 이유이다. 따라서 위 예의 실행결과는 쿼리 결과를 기다리지 않고 쿼리를 실행하고 다른 코드 라인을 계속합니다. 일단 결과가 쿼리에서 생성되면 콜백 및 출력된다. 먼저 ‘It will print after user result print’ 문자열이 출력된 다음 행을 출력한다(사용자 질의 결과).

이 모델의 구현은 이러한 이벤트를 처리하고 실행하는 루프를 사용한다. 그래서 이벤트 큐나 이벤트 루프라고 불린다.

자바스크립트 이벤트 루프(Javascript Event Loop)에 대해 더 자세히 알아보자.

이전 예제로 어떻게 작용하는지 살펴보자.

[[/resource/yongkwan/09/10.png]]

이전에 우리는 setTimeout 함수가 콜백 함수인 printMe1을 사용하여 콜 스택을 밀어넣으면 콜 스택에서 사라짐을 보았다. setTimeout이 실행되면 브라우저가 setTimeout의 콜백 기능을 이벤트 테이블에 배치한다. 그것은 웹 API이기 때문에 현재의 javascript 런타임과는 아무 상관이 없다. 이벤트 테이블로 전달하여 이 함수를 등록하고 특정 이벤트가 발생할 때 실행한다. (이 경우 2초 후)

[[/resource/yongkwan/09/11.png]]

이벤트가 발생하면 이벤트 테이블이 메시지 대기열 테이블(콜백 대기열 테이블)로 함수를 이동시킨다. setTimeout 기능이 실행된 후 어떤 일이 발생하는지 봅시다.

setTimeout 함수가 실행된 후에 어떤 일이 일어나는지 살펴보자.

[[/resource/yongkwan/09/12.png]]

콜백 함수(printMe1())를 이벤트 테이블로 이동하면 콜 스택에서 차단되는 것이 없음을 알 수 있다. 브라우저는 2초 동안 기다리지 않고 콜 스택에서 실행을 계속한다. 다음 실행 코드는  myFunction이고 myFunction이 호출되어 콘솔에서 "It's my function"을 출력하고 콜 스택에서 제거된다. 따라서 콜 스택에는 더 이상 실행할 행이 없으므로 익명 함수도 콜 스택에서 제거되고 콜 스택은 비게된다.

이벤트 테이블은 이벤트가 트리거되는지 여부를 지속적으로 모니터링한다.  만약 트리거 된 경우 해당 함수는 메시지 대기열 또는 호출 대기열로 이동한다. 우리의 예제인 경우, 2초 후엔, printMe1 함수가 트리거되어 메시지 대기열로 이동한다.

2초 후:

[[/resource/yongkwan/09/13.png]]

printMe1() 함수가 이벤트 테이블에서 메시지 큐로 이동된다.

콜 스택이 비어 있는지 지속적으로 확인하는 프로세스 중 하나이다. 콜 스택이 비어 있으면 호출 대기 중인 함수가 있는지 확인한다. 우리의 예제인 경우 메시지 대기열 테이블의 첫 번째 함수가 호출 스택으로 이동하고 호출 스택에서 실행된다.

**이 모든 과정은 EVENT LOOP으로 불린다.**

[[/resource/yongkwan/09/14.png]]

위의 예에서 우리는 printMe1 함수가 호출 스택으로 이동하여 실행되는 것을 볼 수 있다. 콘솔에 Hello javascript’라는 글자가 출력된 것을 볼 수 있다.

## 느낀점

* 첫번째 챕터와 동일한 이벤트 루프에 관한 설명을 다뤘다.
* [자바스크립트 콜스택, 이벤트루프의 이해](https://github.com/Lee-hyuna/33-js-concepts-kr/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%BD%9C%EC%8A%A4%ED%83%9D,-%EC%9D%B4%EB%B2%A4%ED%8A%B8%EB%A3%A8%ED%94%84%EC%9D%98-%EC%9D%B4%ED%95%B4) 참조하면 좋을 듯 하다.