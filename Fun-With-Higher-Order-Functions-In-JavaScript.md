> 원문: https://derickbailey.com/2015/10/21/fun-with-higher-order-functions-in-javascript/

### 고차함수는 재밌..다?!

JavaScript는 "huge order functions"으로 함수형 프로그래밍이 가능해졌다. 이 언어, 단어 자체는 너무 막연한 생각이 들긴 하다..

![](https://derickbailey.com/wp-content/uploads/2015/10/unwrapping.jpeg)

<br>

선물을 열어보고 안에서 또 다른 선물을 찾는 것 같다, 이 좋은 아이디어로 일하는 방법을 터득한다면 재미있고 흥미롭게 일을 할 수 있을 것이다.

<br>

## Common Examples

`underscore`, `lodash` 라이브러리에 있는 "`debounce`" 또는 "`throttle`"과 같은 많은 일반적인 기능을 포함하여 고차함수는 정말 유용하게  많이 쓰인다

```js
window.onResize = _.throttle(function(){
  // do stuff when the window resizes, here
  // but only do it once every 500 milliseconds
  // and no more than that
}, 500);
```
<br>

이 예제는 throttle 함수를 사용하여 윈도우 브라우저 리사이즈 이벤트를 지속적으로 사용하는 것을 방지하는 역할을 한다.
JavaScript에 내장된 다른 예도 볼 수 있다. 혹시, 함수에 "`bind`" 기능을 사용한 적이 있나요?

<br>

```js
var fn = someFunction.bind(someObject);

fn(someParam, 2, "...");
```

<br>

이것은 고차함수를 만드는 방법이다. 일반적으로 JavaScript 코드에는 다양한 예가 있다. this를 쓰기도 쉽다.

<br>

## Build Your Own Bind Function

브라우저에서 ES5 기반 JavaScript가 보편화되기 전에는 "`bind`" 함수를 직접 작성해야 하는 경우가 많았다. 물론, 한 빌드를 사용하여 `underscore`, `lodash`를 할 수 있지만, 모두가 그렇게 하기를 원하지는 않는다.

기본 아이디어는 함수 및 컨텍스트 객체를 전달하여 해당 함수 내에서 "`this`"를 나타내는 것이다.

<br>

```js
function bind(fn, ctx){
  return function(){
    var args = Array.prototype.slice.apply(arguments);
    return fn.apply(ctx, args);
  }
}
```

"bind" 함수 자체는 다른 함수를 반환하는 것 이상의 역할을 하지 않는다. 일부 고차함수는 내부 함수를 반환하기 전에 일부 계산을 수행하고 다른 코드를 설정한다. 중요한 것은 바인딩 함수가 다른 함수를 반환한다는 것이다.

이 경우 정말 중요한 것이 바로 내부 함수이다. 함수를 반환하게되면 변수에 할당된다. 이 변수는 이제 다음과 같은 함수를 가리킨다.

1. 현재 인수 개체를 올바른 배열로 분할한다.
2. 원래 함수를 호출한다 ("`fn`" 매개 변수로 전달됨).
3. "`ctx`" 변수를 원래 기능에 대한 컨텍스트("`this`")로 적용한다.

<br>

예를 들어 다음과 같다.

