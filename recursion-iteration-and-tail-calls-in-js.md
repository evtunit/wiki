# JS의 재귀, 반복 그리고 꼬리 호출 (tail call)

> 원문: [Recursion, iteration and tail calls in JS](http://www.jstips.co/en/javascript/recursion-iteration-and-tail-calls-in-js/)

재귀의 가장 표준 예는 주어진 숫자를 1부터 n까지 모두 곱하는 `n! = n * (n - 1) * ... * 1` 팩토리얼 함수 이다.

```js
function factorial(n) {
    if (n === 0) {
        return 1;
    }
    return n * factorial(n - 1);
}
```

위에 나타낸 예제는 팩토리얼 함수의 가장 평범한 구현이다.

`n = 6`일 때 위의 예제가 어떻게 동작하는지 살펴보자,

-   factorial(6)
    -   6 * factorial(5)
        -   5 * factorial (4)
            -   4 * factorial(3)
                -   3 * factorial(2)
                    -   2 * factorial(1)
                        -   1 * factorial(0)
                            -   1
                        -   (resuming previous execution) 1 * 1 = 1
                    -   (resuming…) 2 * 1 = 2
                -   (…) 3 * 2 = 6
            -   … 4 * 6 = 24
        -   5 * 24 = 120
    -   6 * 120 = 720
-   factorial(6) = 720

앞으로 일어날 작업들을 이해할 수 있도록 무슨 일이 일어나고 있는지 신중하게 봐야한다.

함수를 호출하면 한 번에 여러 가지 일이 발생한다. 함수를 호출 한 후 돌아 가야하는 위치는 현재 프레임(콜 스택의)의 정보 (예 : n 값)와 함께 저장됩니다. 그런 다음 새 함수를 위한 공간이 할당되고 새 프레임이 생성된다.

계속해서 프레임을 쌓아 올린 다음 해당 스택을 풀어 함수 호출을 반환 된 값으로 바꾼다.

주목해야 할 또 다른 사항은 함수에 의해 생성 된 프로세스의 모양이다. 이 모양을 *재귀* 라고 부르면 놀라지 않을 것 이다. 따라서 *재귀 프로세스가* 있다. ~~뭔소리여~~

팩토리얼의 두 번째 구현을 살펴보자.

```js
function factorial(n, res) {
    if (n === 0) {
        return res;
    }
    return factorial(n - 1, res * n);
}
```

내부 함수를 정의함으로써 함수를 좀 더 캡슐화할 수 있다.

```js
function factorial(n) {
    function inner_factorial(n, res) {
        if (n === 0) {
            return res;
        }
        return inner_factorial(n - 1, res * n);
    }
    return inner_factorial(n, 1);
}
```

이 함수가 어떻게 실행되는지 살펴보자.

-   factorial(6)
    -   내부 익명 함수(iaf) 는(n = 6, res = 1)
        -   iaf(5, 1 * 6)
            -   iaf(4, 6 * 5)
                -   iaf(3, 30 * 4)
                    -   iaf(2, 120 * 3)
                        -   iaf(1, 360 * 2)
                            -   iaf(0, 720)
                                -   720
                            -   720
                        -   720
                    -   720
                -   720
            -   720
        -   720
    -   iaf (6, 1) = 720
-   factorial(6) = 720

스택을 풀고 나서 계산을 수행 할 필요가 없다는 것을 알 수 있다. 방금 값을 반환했다. 그러나 규칙에 따라 체인에서 나중에 사용하지 않더라도 상태를 스택 프레임으로 저장해야했다.

그러나 규칙은 모든 언어에 적용되는 것은 아니다. 실제로, Scheme에서는 이러한 체인을 tail call 최적화로 최적화해여한다. 이를 통해 스택에 불필요한 프레임이 채워지지 않는다. 우리의 위 예제는 다음과 같이 보일 것이다.

-   factorial(6)
-   iaf(6, 1)
-   iaf(5, 6)
-   iaf(4, 30)
-   iaf(3, 120)
-   iaf(2, 360)
-   iaf(1, 720)
-   iaf(0, 720)
-   720

결과적으로 끔찍하게 보인다.

즉, 재귀를 사용하더라도 *반복적 인 프로세스*가 있다.

좋은 소식은 ES6의 함수이다. 재귀 호출이 테일 위치에 있고 함수에 strict 모드가 있으면 테일 호출 최적화가 시작되고 `maximum stack size exceeded`오류가 발생하지 않는다.

업데이트 2017 년 12 월 1 일 : 테일 콜 최적화 기능이있는 유일한 주요 브라우저는 Safari이다. V8에 영향을 주지만 2 가 아직 나오지 않은 이유는 아래 3가지이다.


1:  [https://kangax.github.io/compat-table/es6/#test-proper_tail_calls_(tail_call_optimisation)](https://kangax.github.io/compat-table/es6/#test-proper_tail_calls_(tail_call_optimisation))

2:  [https://bugs.chromium.org/p/v8/issues/detail?id=4698](https://bugs.chromium.org/p/v8/issues/detail?id=4698)

3:  [https://v8project.blogspot.com/2016/04/es6-es7-and-beyond.html](https://v8project.blogspot.com/2016/04/es6-es7-and-beyond.html)
 