자바스크립트는 다이나믹한 언어입니다. 그리고 여러가지 작업을 할 수 있는 가변적인 언어입니다. 객체 또는 클래스가 상위로부터 상속받을 수 있는 언어이며, 이 작업은 `call`, `apply`, `bind` 세가지 방법 중 하나를 사용하면 됩니다.

![](https://miro.medium.com/max/700/1*37QCmu-xcR2FMtzk8hyEuA.png)

그럼, 뭐가 다른지 알아볼까요?

<br>

## Call

예를 들어, 우리는 `obj`라고 불리는 객체를 가지고 있습니다. things라는 key에 값이 3이 들어가있는 오브젝트를 가지고 있습니다. 이 객제와는 무관한 addThings라는 function을 만들어보겠습니다.

```js
let obj = {things: 3};
let addThings = function(a, b, c){
 return this.things + a + b + c;
};
```

여기서 `this.things`는 무엇일까요, `addThings`를 호출할 때엔 things라는 것이 없는데 왜 return값에 넣었을까요? 이 구문은 잠시 넘어갈 필요가 있습니다. `call`을 이용하여 다음과 같은 코드를 사용하면 위의 코드는 사용가능한 코드가 되게 됩니다

```js
console.log( addThings.call(obj, 1,4,6) )
```

return값으로 숫자 14가 나왔습니다.

그럼 첫번째 parmeter를 call로 받았을 경우 위의 obj를 넘겼을 때 this는 무엇을 위해 있는 걸까요? things 자체의 값을 인자값으로 넘겼기 때문에 해당 구문을 통과한 것입니다.

```js
return this.things + a + b + c;
```

이 구문을 풀어쓰자면 아래와 같습니다.

```js
return 3 + 1 + 4 + 6
```

그래서 return 값은 14가 된 것 입니다.
이제 `call`은 끝났습니다! `apply`로 넘어가보죠

<br>

## Apply
`apply`는 `call`과 비슷합니다. 

가장 큰 차이점은 우리가 arguments를 통과할 수 있는 방법입니다. 우리는 그것들을 배열로 전달할 수 있습니다. 이전 코드를 삭제하고 다시 시작하겠습니다.

```js
let obj = {things: 3};

let addThings = function(a, b, c){
 return this.things + a + b + c;
};
let arr = [1,4,6];
console.log( addThings.apply(obj, arr) );
```

<br>

## Bind

```js
let obj = {things: 3};
let addThings = function(a, b, c){
 return this.things + a + b + c;
};

console.log( addThings.bind(obj, 1,4,6) );
```

bind를 사용하지 않고 진행했다면 14라고 뜨지 않을 것 입니다. bind는 function의 복사본을 return 하지만 다른 컨텍스트를 사용하여 작동합니다. 위의 코드에서는 obj를 통과시켰지만 실행하지는 않습니다.

```js
console.log( addThings.bind(obj, 1,4,6)() );
```

bind를 통하여 obj의 this를 전달 해준 후 그 다음 인자들을 전달해보겠습니다.

```js
console.log( addThings.bind(obj)(1,4,6) );
```

이제 적용이 되었어요! 잘 실행이 되죠?

<br>

Call / Apply / Bind에 대해 알아보는 시간이었습니다!

Happy coding.