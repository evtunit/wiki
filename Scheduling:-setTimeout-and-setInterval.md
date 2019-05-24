# 스케쥴링: setTimeout and setInterval

번역 : https://javascript.info/settimeout-setinterval#canceling-with-cleartimeout

우리는 함수가 즉시 실행하지 않고 특정 시간에 실행되길 결정할 수 있다. 이것을 "scheduling a call" 이라 불립니다.

여기 두가지 메서드가 있습니다.

* `setTimeout` 이것은 특정시간 간격 후에 한번 실행됩니다.
* `setInterval` 이것은 특정시간 간격동안 주기적으로 실행됩니다.

이 메서드들은 자바스크립트의 특정항목의 한 파트가 아닙니다. 하지만 대부분 환경들은 내부적인 스케쥴러를 가지고 있고 이런 메서드들을 제공하고 있습니다. 
특히나 이들은 모든 브라우저와 Node.js에서 지원을 하고 있습니다.

## setTimeout

문법
```javascript
let timerId = setTimeout(func|code, [delay], [arg1], [arg2], ...)
```

파라미터들: 

`func|code`

함수 또는 코드뭉치들을 실행합니다. 대게는 함수입니다. 역사적인 이유로 인해 일련의 코드를 전달할 수는 있지만 권장하지는 않습니다.

`delay`

동작하기 전 딜레이 시간입니다. 단위는 밀리세컨드(1000ms = 1 초), 디폴트는 0

`arg1`,  `arg2`…

함수에 전달될 인자들 ( IE9 는 지원을 하지 않습니다. )
예를 들면 `sayHi()` 라는 코드를 1초 뒤에 호출할 것입니다.

```javascript
function sayHi() {
  alert('Hello');
}

setTimeout(sayHi, 1000);
```
인자들과 함께 호출하려면

```javascript
function sayHi(phrase, who) {
  alert( phrase + ', ' + who );
}

setTimeout(sayHi, 1000, "Hello", "John"); // Hello, John
```

만약에 첫번째 인자로 문자열을 넘긴다면 자바스크립트는 이 문자열을 가지고 함수를 만듭니다. 

이렇게 작동될 수 있습니다.
```javascript
setTimeout("alert('Hello')", 1000);
```

하지만 문자열을 사용하는것을 추천하지 않습니다. 그것 대신에 아래처럼 함수를 사용하십시요.

```javascript
setTimeout(() => alert('Hello'), 1000);
```

초보자 개발자들이 때때로 함수를 넘겼는데 작동하지 않는 실수를 범할때가 있습니다. 
함수를 넘기면서 괄호() 를 함께 사용했기 때문입니다.

```javascript
// wrong!
setTimeout(sayHi(), 1000);
```

이것은 작동하지 않습니다. `setTimeout`은 함수의 레퍼런스를 기대 합니다. 
여기선 `sayHi()`를 실행하고 그 실행의 결과를 `setTimeout`의 인자로 넘깁니다. 
여기 우리의 경우에는 `sayHi()`는 `undefined`(함수가 아무것도 반환하지 않았다.) 를 반환해서 아무것도 스케쥴링 되지 않습니다.

## Canceling with clearTimeout

`setTimeout`은 "timer identifier" 라는 것을 반환합니다. 이 `timerId`는 실행을 취소할 수 있습니다.

취소 문법:
```javascript
let timerId = setTimeout(...);
clearTimeout(timerId);
```
아래 코드에서 보면, 우리는 함수하나를 스케쥴 했고 그 후에 취소했습니다.(마음이 바뀌어서) 그 결과 아무일도 일어나지 않습니다.

```javascript
let timerId = setTimeout(() => alert("never happens"), 1000);
alert(timerId); // timer identifier

clearTimeout(timerId);
alert(timerId); // same identifier (doesn't become null after canceling)
```

우리는 alert의 output으로부터 볼수 있습니다. 브라우저 timer identifier는 숫자 입니다. 다른 환경에서도 이것은 무엇인가 될수 있습니다. 예를 들면 Node.js에서는 추가적인 메서드를 지닌 timer 객체를 리턴하게 됩니다.

다시말해 이 메서드들은 유니버셜한 명세가 없습니다. 그래도 괜찮습니다.
브라우저들은 timer들을 HTML5 표준의 [timers section]([https://www.w3.org/TR/html5/webappapis.html#timers](https://www.w3.org/TR/html5/webappapis.html#timers))에서 설명하고 있습니다. 

## setInterval

`setInterval` 메서드는 `setItmeout`과 동일한 문법을 지니고 있습니다. 

```javascript
let timerId = setInterval(func|code, [delay], [arg1], [arg2], ...)
```

모든 인자값들은 같은 의미를 지니고 있습니다. 하지만 `setTimeout`과른 다르게 그것은 함수를 한번만 동작시키지 않습니다. 시간의 간격이 주어지면 규칙적으로 실행하게 됩니다.

추가적인 호출을 막으려면, 우린 `clearInterval(timerId)`를 호출해야 합니다.

다음 예제에는 매 2초마다 메세지를 보여주는 예제 입니다. 5초 후엔 output이 멈추게 됩니다.

```javascript
// repeat with the interval of 2 seconds
let timerId = setInterval(() => alert('tick'), 2000);

// after 5 seconds stop
setTimeout(() => { clearInterval(timerId); alert('stop'); }, 5000);
```

경고 창이 보여지는 동안에도 시간은 갑니다.

크롬과 파이어폭스를 포함하는 대부분의 브라우저들은 `alert/confirm/prompt`가 보여지는 동안 내부적인 timer 가 계속 "똑딱"(진행된다) 됩니다.

그래서 만약 위 코드를 실행하고나서 몇몇시간 동안 `alert`창을 닫지 않는다면, 다음 행위를 하자마자 alert창을 즉시 보여줄것입니다. 실제 alert들의 사이 간격은 5초 보다도 더 짧을 것입니다.

## Recursive setTimeout

규칙적으로 뭔가를 실행의 2가지 방법이 있습니다.

`setInterval`이 한가지이고 다른 한가지는 재귀적인 `setTimeout`입니다. 

```javascript
/** instead of:
let timerId = setInterval(() => alert('tick'), 2000);
*/

let timerId = setTimeout(function tick() {
  alert('tick');
  timerId = setTimeout(tick, 2000); // (*)
}, 2000);
```

`setTimeout`은 (*) 이 부분에서는 현재 호출의 끝에서 바로 다음 호출을 예약합니다.

재귀적인 `setTimeout`은 `setInterval` 보다 좀 더 유연합니다. 이렇게하면 다음 호출이 현재 호출의 결과에 따라 다르게 스케줄 될 수 있습니다.

예를 들면, 우리는 매 5초마다 서버에 요청하는 서비스를 기록해야 합니다. 하지만 지나치게 부하가 걸리는 서버의 경우에는 이 간격을 10, 20, 40초로 늘려야 합니다.

여기 수도 코드를 보십시요

```javascript
let delay = 5000;

let timerId = setTimeout(function request() {
  ...send request...

  if (request failed due to server overload) {
    // increase the interval to the next run
    delay *= 2;
  }

  timerId = setTimeout(request, delay);

}, delay);
```

그리고 우리가 계획하고있는 기능이 CPU 사용이 많은 경우, 실행에 걸린 시간을 측정하고 조만간 다음 호출을 계획 할 수 있습니다.

**재귀적인 `setTimeout` 은 실행 사이의 지연을 보장하지만, `setInterval`은 그렇지 못합니다.**

여기 두가지의 코드 조각이 있습니다. 처음엔 `setInterval` 사용입니다.

```javascript
let i = 1;
setInterval(function() {
  func(i);
}, 100);
```

두번째 경우는 재귀적인 `setTimeout` 사용입니다.

```javascript
let i = 1;
setTimeout(function run() {
  func(i);
  setTimeout(run, 100);
}, 100);
```

`setInterval`의 내부적인 스케쥴링은 `func(i)`를 매 100ms 마다 실행합니다.
![https://javascript.info/article/settimeout-setinterval/setinterval-interval.png](https://javascript.info/article/settimeout-setinterval/setinterval-interval.png)

보이시나요?
**`setInterval`로 함수 `func()` 호출되는 실제 딜레이간격은 코드상에서 보는거보다 훨씬 덜하다.**
`func`의 실행도 interval의 한 부분으로 시간이 소비되기 때문이다.

이것은 `func`의 실행이 우리가 기대하고 주어진 100ms 초보다 더 길어질수 있습니다.

이 경우 엔진은 `func`이 완료 될 때까지 기다린 다음 스케줄러를 검사하고 시간이 끝나면 즉시 다시 실행합니다.
드문 경우로, 함수가 항상 지연 ms보다 오래 실행되면 호출은 일시 중지없이 발생합니다.

그리고 여기 재귀적인 `setTimeout` 의 그림이 있습니다.

![https://javascript.info/article/settimeout-setinterval/settimeout-interval.png](https://javascript.info/article/settimeout-setinterval/settimeout-interval.png)

**재귀 setTimeout은 고정 지연 (여기서는 100ms)을 보장합니다.**

새로운 콜은 이전의 작업이 끝나고 계획되기 때문입니다.

### Garbage collection

함수를 `setInterval/setTimeout`에 넘길때, 내부 참조가 함수안에 생겨나고 스케쥴러 안에 저장됩니다. 이것은 garbage collected 로 부터 함수를 지켜내는데 심지어 다른 참조가없는 경우에도 마찬가지입니다.

```javascript
// the function stays in memory until the scheduler calls it
setTimeout(function() {...}, 100);
```

`setInterval`의 경우에는 `clearInterval`이 호출 될때까지 메모리에 남아있게 됩니다.

이것은 부작용이 있습니다. 함수는 외부 lexical 환경을 참조합니다. 그래서 이 함수가 살아있는 동안, 외부 변수들 또한 살아있게 됩니다. 그것들은 함수 자체보다 더많은 메모리를 차지 할 수 있습니다. 그래서 더이상 함수 스케쥴링이 필요없을 때에는 취소하는게 좋습니다. 그것이 작은 함수라도 말입니다.

## setTimeout(...,0)

여기 아주 특별한 경우가 있습니다.:  `setTimeout(func, 0)`, 또는 `setTimeout(func)`.

이 `func` 의 실행 스케쥴은 곧 가능합니다. 그러나 스케쥴러는 오직 현재 코드가 다 완료 된 이후에 실행을 시킵니다.
그래서 함수는 현재 코드가 다 실행된 이후에 동작하라고 스케쥴 됩니다. 다시 말해서 _비동기적_ 인 것입니다.
예를들어 이 결과는 "Hello" 나오고나서 즉시 "World"가 나올겁니다.

```javascript
setTimeout(() => alert("World"));

alert("Hello");
```

첫 번째 라인은 "0ms 후에 해당 실행을 캘린더에 넣습니다." 그러나 스케줄러는 현재 코드가 완료된 후에 만 ​​"캘린더를 확인"하므로 "Hello"가 먼저오고 "World"가 뒤에옵니다.

## Splitting CPU-hungry task

여기 CPU-hungry(cpu 자원을 많이 먹는) 수행을 `setTimeout`을 사용해서 분리시키는 트릭이 있습니다. 

예를 들어 구문 강조 표시 스크립트 (이 페이지의 코드 예제를 색칠하는 데 사용)는 CPU가 많이 소모됩니다. 코드를 강조 표시하기 위해 분석을 수행하고, 많은 색상 요소를 만들고, 문서에 추가합니다. 브라우저가 "멈추는"경우가 발생할 수 있습니다. 이는 받아 들일 수 없습니다.

그래서 우리는 긴 텍스트를 조각들로 나눌 수 있습니다. 처음으로 100 라인을 작성한 다음`setTimeout (..., 0)`을 사용하여 또 다른 100 라인을 계획하십시오.

명확성을 위해보다 간단한 예제를 고려해 보겠습니다. '1'에서 '1000000000'까지 계산하는 기능이 있습니다.

실행하면 CPU가 멈춥니다. 분명히 눈에 띄는 서버 측 JS의 경우와 브라우저에서 이 코드를 실행 시킨후에 페이지의 다른 버튼을 클릭을 시도해봐라. 전체 JavaScript가 실제로 일시 중지 된 것을 확인할 수 있으며 완료 될 때까지는 다른 작업을 수행 할 수 없습니다.

```javascript
let i = 0;

let start = Date.now();

function count() {

  // do a heavy job
  for (let j = 0; j < 1e9; j++) {
    i++;
  }

  alert("Done in " + (Date.now() - start) + 'ms');
}

count();
```

브라우저는 "스크립트가 너무 오래 걸린다"라는 경고를 표시 할 수도 있습니다 (그러나 숫자가 그리 크지 않기 때문에 잘되지 않습니다.)

중첩 된 setTimeout을 사용하여 작업을 분할합시다.

```javascript
let i = 0;

let start = Date.now();

function count() {

  // do a piece of the heavy job (*)
  do {
    i++;
  } while (i % 1e6 != 0);

  if (i == 1e9) {
    alert("Done in " + (Date.now() - start) + 'ms');
  } else {
    setTimeout(count); // schedule the new call (**)
  }

}

count();
```

이제 브라우저 UI는 "계산 중"프로세스에서 완벽하게 작동합니다.

`(*)` 로 표시된 부분의 동작을 보자:

1. 처음 동작 : `i=1...1000000`.
2. 두번째 동작 : `i=1000001..2000000`
3. 그리곤 계속.., `while`은 `i`가 `1000000`으로 균등하게 분할되는지 검사합니다.

`(**)` 표시 된 곳에서 아직 작업이 완료가 되지 않았다면 다음 호출을 스케쥴링을 합니다.

카운트 실행 사이의 일시 중지는 자바 스크립트 엔진이 다른 작업을 수행하고 다른 사용자 작업에 반응 할 수있을만큼 충분한 "호흡"을 제공합니다.

주목할만한 점은 setTimeout으로 작업을 분할하거나 분할하지 않은 두 가지 상황 모두 속도가 비슷하다는 점입니다. 전체 계산 시간에는 별다른 차이가 없습니다.

그것들을 더 가깝게 만들기 위해서, 개선해 보겠습니다.

count ()의 시작 부분을 스케쥴링으로 이동시키겠습니다.

```javascript
let i = 0;

let start = Date.now();

function count() {

  // move the scheduling at the beginning
  if (i < 1e9 - 1e6) {
    setTimeout(count); // schedule the new call
  }

  do {
    i++;
  } while (i % 1e6 != 0);

  if (i == 1e9) {
    alert("Done in " + (Date.now() - start) + 'ms');
  }

}

count();
```

이제 우리가`count ()`를 시작하고 더 많은 것을 `count ()` 할 필요가 있음을 알게되면, 작업을 수행하기 전에 즉시 그것을 계획합니다.

실행하면 훨씬 적은 시간이 걸리는 것을 알 수 있습니다.

### 중첩 된 타이머의 브라우저 지연 최소화

브라우저에는 중첩 타이머를 실행할 수있는 빈도의 한계가 있습니다. HTML5 표준에 따르면 "5 개의 중첩 된 타이머 이후에 간격은 최소 4 밀리 초가되어야합니다."

아래 예를 통해 의미를 설명해 보겠습니다. 그것의 setTimeout 호출은 0ms 후에 스스로를 재 스케쥴 합니다. 각 호출은 times 배열의 이전 호출로부터 실시간을 기억합니다. 실제 지연은 어떻게 생겼습니까? 봅시다 :

```javascript
let start = Date.now();
let times = [];

setTimeout(function run() {
  times.push(Date.now() - start); // remember delay from the previous call

  if (start + 100 < Date.now()) alert(times); // show the delays after 100ms
  else setTimeout(run); // else re-schedule
});

// an example of the output:
// 1,1,1,1,9,15,20,24,30,35,40,45,50,55,59,64,70,75,80,85,90,95,100
```

첫 번째 타이머는 즉시 실행되며 (사양과 마찬가지로) 지연이 발생하고 9, 15, 20, 24가 표시됩니다.

그러한 제한은 고대부터 왔으며 많은 스크립트가이 스크립트를 사용하므로 역사적인 이유로 존재합니다.

서버 측 JavaScript의 경우 이러한 제한이 존재하지 않으며 Node.js의 process.nextTick 및 setImmediate와 같은 즉각적인 비동기 작업을 예약하는 다른 방법이 있습니다. 따라서이 개념은 브라우저마다 다릅니다.

## Allowing the browser to render

브라우저 스크립트에 많은 작업을 나누는 또 다른 이점은 사용자에게 진행률 막대 또는 무언가를 표시 할 수 있다는 것입니다.

일반적으로 브라우저는 현재 실행중인 코드가 완료된 후 모든 "다시 그리기"를 수행합니다. 따라서 많은 요소를 변경하는 하나의 거대한 함수를 수행하면 변경 사항이 완료 될 때까지 변경되지 않습니다.

데모는 다음과 같습니다.

```markup
<div id="progress"></div>

<script>
  let i = 0;

  function count() {
    for (let j = 0; j < 1e6; j++) {
      i++;
      // put the current i into the <div>
      // (we'll talk about innerHTML in the specific chapter, it just writes into element here)
      progress.innerHTML = i;
    }
  }

  count();
</script>
```

그것을 실행하면 전체 카운트가 끝난 후에 i에 대한 변경 사항이 표시됩니다.

그리고 setTimeout을 사용하여 조각으로 분할하면 변경 사항이 실행 사이에 적용되므로 더 잘 보입니다.

```markup
<div id="progress"></div>

<script>
  let i = 0;

  function count() {

    // do a piece of the heavy job (*)
    do {
      i++;
      progress.innerHTML = i;
    } while (i % 1e3 != 0);

    if (i < 1e9) {
      setTimeout(count);
    }

  }

  count();
</script>
```

이제 <div>는 i의 값이 증가하는 것을 보여줍니다.

## Summary

-`setInterval (func, delay, ... args)`메소드와`setTimeout (func, delay, ... args)`메소드는`func`를`delay` 밀리 세컨드 후에 정기적으로 / 한번 실행하도록 허용한다.
- 실행을 취소하려면`setInterval / setTimeout`에 의해 반환 된 값으로`clearInterval / clearTimeout`을 호출해야합니다.
- 중첩 된`setTimeout` 호출은`setInterval`에 대한보다 유연한 대안입니다. 또한 그들은 사형 집행 동안 최소한의 시간을 보장 할 수 있습니다.
- 제로 타임 아웃 스케쥴링`setTimeout (func, 0)`(`setTimeout (func)`과 동일)은 "가능한 한 빨리, 그러나 현재 코드가 완료된 후에"호출을 스케쥴하는데 사용됩니다.

`setTimeout (func)`의 몇 가지 사용 케이스 :
-   CPU가 많이 사용되는 작업을 여러 조각으로 나누어 스크립트가 "정지"하지 않도록 하기.
- 프로세스가 진행되는 동안 브라우저가 다른 작업을 수행하게 되도록 하기 (진행률 막대를 그립니다).

모든 스케줄링 방법은 정확한 지연을 보장하지 않습니다. 우리는 예정된 코드에 의존해서는 안됩니다.

예를 들어, 브라우저 내부 타이머가 여러 가지 이유로 느려질 수 있습니다.

- CPU에 과부하가 있습니다.
- 브라우저 탭이 백그라운드 모드에 있습니다.
- 노트북이 배터리로 작동합니다.

최소한 브라우저 와 설정에 따라 최소 타이머 해상도 (최소 지연 시간)를 300ms 또는 1000ms까지 늘어날 수 있습니다.