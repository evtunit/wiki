<br>

Hello@

스코프와 클로저는 자바스크립트에서 아~주 중요한 역할을 하죠? 그런데 처음 자바스크립트를 처음 시작할땐 진짜 이해하기가 어려운 숙제입니다! 자자, 이제 스코프와 클로저가 뭔지 지금부터 빡세게 설명을 해드리겠습니다

<br>

## #Scope

자바스크립트의 스코프는 변수에 접근할 수 있는데 '글로벌 스코프'와 '로컬 스코프' 범위가 있다.

<br>

### #Global Scope

모든 변수가 function 이나 중괄호 `{}`로 구성된 객체 외부에 선언되었을 때 **global scope**라고 한다.

> 이건 자바스크립트에서만 해당한다. Node.js에서는 글로벌 스코프가 다르게 선언이 되지만, 이 챕터에선 Node.js를 하지 않으니까 패쓰~ 👍 

```js
const globalVariable = 'some value'
```

글로벌 변수로 선언이 되었으면 언제, 어디서든 사용할 수 있다.

```js
const hello = 'Hello CSS-Tricks Reader!'

function sayHello () {
  console.log(hello)
}

console.log(hello) // 'Hello CSS-Tricks Reader!'
sayHello() // 'Hello CSS-Tricks Reader!'
```

전역 범위에서 변수를 선언할 수 있지만, 그러지 않는 것이 좋다. 두 개 이상의 변수를 동일한 이름으로 명명하는 충돌의 가능성이 있기 때문이다. 
`const`, `let`으로 선언했다면 이름 충돌이 일어날 때마다 오류를 받을 것이다. 이렇게 하지 마세요~!

```js
// Don't do this!
let thing = 'something'
let thing = 'something else' // Error, thing has already been declared
```

`var`를 선언하고 두 번째 변수는 선언된 후에 첫번째 변수를 덮어쓴다. 이것은 또한 코드를 디버깅하기 어렵게 만들기 때문에 바람직하지 않다.

```js
// Don't do this!
var thing = 'something'
var thing = 'something else' // perhaps somewhere totally different in your code
console.log(thing) // 'something else'
```

따라서 항상 글로벌 변수가 아니라 로컬 변수를 선언해야 한다.

<br>

### #Local Scope

코드의 특정 부분에서만 사용할 수 있는 변수는 로컬 범위에 있는 것으로 간주한다. 이러한 변수를 **local scope**라고도 한다.

자바스크립트에는 function scope와 block scope라는 두 종류의 local scope가 있다. 먼저 function scope부터 이야기해 봅시다.

<br> 

**Function scope**

함수에 변수를 선언할 때는 함수 내에서만 이 변수에 액세스할 수 있다. 함수 밖에서는 접근을 할 수 없다.

```js
function sayHello () {
  const hello = 'Hello CSS-Tricks Reader!'
  console.log(hello)
}

sayHello() // 'Hello CSS-Tricks Reader!'
console.log(hello) // Error, hello is not defined
```


***

**Block scope**

`const`와 `let`은 중괄호`{}`안에서만 접근이 가능하다.<br>
아래의 예에서는 `hello`가 중괄호 안에서의 스코프를 가지고 있다는 것을 알 수 있다.

```js
{
  const hello = 'Hello CSS-Tricks Reader!'
  console.log(hello) // 'Hello CSS-Tricks Reader!'
}

console.log(hello) // Error, hello is not defined
```

(암묵적 리턴이 있는 화살표 함수를 사용하지 않는 한) 중괄호를 사용하여 함수를 선언해야 하기 때문에 블록 스코프는 함수 스코프의 하위 집합이다.

<br>

### #Function hoisting and scopes

함수는 함수 선언과 함께 선언될 때 항상 현재 스코프의 최상단으로 올라간다. 따라서 이 두 가지는 동등하다.

```js
// This is the same as the one below
sayHello()
function sayHello () {
  console.log('Hello CSS-Tricks Reader!')
}

// This is the same as the code above
function sayHello () {
  console.log('Hello CSS-Tricks Reader!')
}
sayHello()
```

함수 표현식을 사용하여 선언할 때 함수는 현재 스코프 상단에 올리지 않는다.

```js
sayHello() // Error, sayHello is not defined
const sayHello = function () {
  console.log(aFunction)
}
```

이러한 두 가지 변화 때문에 function 호이스팅은 잠재적으로 혼동을 일으킬 수 있으므로 사용해서는 안 된다. function을 사용하기 전에 항상 function을 선언하십시오.

<br>

### #Functions do not have access to each other's scopes

함수는 한 함수가 다른 함수에 사용될 수 있음에도 불구하고 그것들을 별도로 정의할 때 서로의 범위에 접근할 수 없다.<br>
다음 아래의 예와 같이 `second` 에서 `firstFunctionVariable`를 사용할 수 없는 부분을 볼 수 있다.

```js
function first () {
  const firstFunctionVariable = `I'm part of first`
}

function second () {
  first()
  console.log(firstFunctionVariable) // Error, firstFunctionVariable is not defined
}

<br>

### ##Nested scopes

함수가 다른 함수에 정의될 때, 내부 함수는 외부 함수의 변수에 접근할 수 있다. 이러한 행동을 어휘적 ** lexical scoping**이라고 한다.<br>
그러나 외측함수는 내측함수의 변수에 접근할 수 없다.


```js
function outerFunction () {
  const outer = `I'm the outer function!`

  function innerFunction() {
    const inner = `I'm the inner function!`
    console.log(outer) // I'm the outer function!
  }

  console.log(inner) // Error, inner is not defined
}
```

스코프가 어떻게 작동하는지를 시각화하기 위해 예를 들어보자 <br>
일방 유리를 예를 들자면, 겉은 볼 수 있지만 겉은 사람이 볼 수 없다.

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1502482479/one-way-glass_l5dg2g.png)

범위 내에 스코프가 있는 경우 여러가지 층의 일방유리를 상상할 수 있다.

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_936,f_auto,q_auto/v1502482500/glass-layers_iohiy2.png)

지금까지 스코프에 대한 모든 것을 이해했다면 클로저가 무엇인지 알아낼 준비가 충분히 되었다.

<br>

## Closures

함수 내에서 내부 함수를 생성할 때마다 클로저를 생성한다. 내부함수는 클로저다. 클로저는 보통 반환되므로 외부함수의 변수를 사용할 수 있다.<br>
클로저를 공부하면 항상 볼 수 있는 예시이다.

```js
function outerFunction () {
  const outer = `I see the outer variable!`

  function innerFunction() {
    console.log(outer)
  }

  return innerFunction
}

outerFunction()() // I see the outer variable!
```

아래예시처럼 내부함수는 리턴되므로 기능을 선언하면서 리턴 작성하여 코드를 약간 단축할 수도 있다.

```js
function outerFunction () {
  const outer = `I see the outer variable!`

  return function innerFunction() {
    console.log(outer)
  }
}

outerFunction()() // I see the outer variable!
```

클로저는 외부함수에 있는 변수에 접근할 수 있기 때문에 일반적으로 두 가지 용도로 사용된다.
1. 사이드 이펙트 컨트롤
2. 변수를 암호화 하려고 할 때

<br>

### #Controlling side effects with closures

사이드 이펙트는 함수의 값을 반환하는 것 외에 다른 것을 할 때 발생한다. <br>
Ajax 요청, 시간 초과 또는 `console.log`와 같은 많은 것들이 부작용일 수 있다.

```js
function (x) {
  console.log('A console.log is a side effect!')
}
```

보통 사이드 이펙트를 해결하기 위해 클로저를 사용할 때 Ajax나 timeout처럼 코드흐름을 해칠까봐 걱정을 많이한다.

상황을 더 명확하게 하기 위해 이 일을 예시로 살펴보자. <br>

친구의 생일을 위해 케이크를 만들고 싶다고 하자. 이 케이크는 만드는 데 1초가 걸리니까 1초 후에 로그를 **Made a cake**라고 찍는다.

> ES6 화살표 기능을 사용하여 예를 더 짧고 이해하기 쉽게 만들고 있다.

```js
function makeCake() {
  setTimeout(_ => console.log(`Made a cake`), 1000)
}
```

케이크 만들기 기능은 시간 초과라는 부작용을 가지고 있다.<br>
더 나아가 당신이 당신의 친구가 케이크의 맛을 선택하기를 원한다고 하자. 그러기 위해서는 `makeCake`에 풍미를 더할 수 있다.

```js
function makeCake(flavor) {
  setTimeout(_ => console.log(`Made a ${flavor} cake!`), 1000)
}
```

function을 실행할 때 케이크에 재료를 넣을 수 있다

```js
makeCake('banana')
// Made a banana cake!
```

여기서 문제는 그 맛을 알고 나서 바로 케이크를 만들고 싶지 않다는 것이다.<br>
이 문제를 해결하기 위해, 케이크의 맛을 저장하는 `prepareCake` function을 만들었다. `prepareCake`내의 `makeCake`를 클로저로 리턴한다.
이때부터 언제든지 원하면 리턴되는 기능을 호출할 수 있으며, 케이크는 1초 안에 만들어질 것이다.

```js
function prepareCake (flavor) {
  return function () {
    setTimeout(_ => console.log(`Made a ${flavor} cake!`), 1000)
  }
}

const makeCakeLater = prepareCake('banana')

// And later in your code...
makeCakeLater()
// Made a banana cake!
```

<br>

### #Private variables with closures

지금까지 알고 있듯이 함수에 생성되는 변수는 함수 밖에서 접속할 수 없다. 접속이 안 되기 때문에 private변수 라고도 부른다.<br>
그런데 특별한 경우에 private변수에 접근해야할 때가 있다. 클로져에서 이 부분을 응용할 수 있다.

```js
function secret (secretCode) {
  return {
    saySecretCode () {
      console.log(secretCode)
    }
  }
}

const theSecret = secret('CSS Tricks is amazing')
theSecret.saySecretCode()
// 'CSS Tricks is amazing'
```

`saySecretCode`는 위의 예에서 원래의 비밀함수 외부에 비밀코드를 노출시키는 유일한 기능(클로저)이다. 이런 기능을 **privileged function**라고도 불린다.

<br>

## #Debugging scopes with DevTools
최근 사용한 스코프에 대해서 크롬이나 파이어폭스의 개발자도구에서 간단하게 디버깅을 할 수 있다.<br>
이 기능을 사용하는 방법에는 두 가지가 있다.<br>
<br>
첫 번째 방법은 코드에 `debugger` 키워드를 추가하는 것이다. 이로 인해 브라우저에서 JavaScript 실행이 일시 중지되어 디버깅이 가능하다.

```js
function prepareCake (flavor) {
  // Adding debugger
  debugger
  return function () {
    setTimeout(_ => console.log(`Made a ${flavor} cake!`), 1000)
  }
}

const makeCakeLater = prepareCake('banana')
```
DevTools를 열고 Chrome(또는 Firefox의 Debugger 탭)의 Source(소스) 탭으로 이동하면 사용할 수 있는 변수가 나타난다.