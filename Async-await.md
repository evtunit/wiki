# Async/await

번역 : [https://javascript.info/async-await](https://javascript.info/async-await)

여기 promises와 함께 작동되는 "async/await" 이라고 불리는 문법이 있다. 매우 놀랍도록 이해하기와 사용하기가 쉽다. 

## Async functions

`async` 키워드에 대해서 알아보자. 이 키워드는 다음과 같이 함수 앞부분에 위치한다. 
```javascript
async function f() {
  return 1;
}
```

함수 앞의 `async`  단어는 한가지 뜻을 지닌다: 이 함수는 항상 promise를 리턴합니다. 다른 값들은 자동적으로 resolve된 promise로 감싸집니다.

예를 들어, 이 함수는 결과가 1인 resolve된 promise를 반환합니다. 
```javascript
async function f() {
  return 1;
}

f().then(alert); // 1
```

우리는 암묵적으로 promise를 리턴합니다. 이는 다음과 같습니다. 

```javascript
async function f() {
  return Promise.resolve(1);
}

f().then(alert); // 1
```

그래서 `async` 는 함수가 promise를 리턴 하는 것을 보장하고, promise가 아닌 값을 포장합니다. 매우 간단하지 않습니까? 이게 끝이 아닙니다. `await` 은 오직 `async` 함수 안에서 동작합니다. 이것은 매우 쿨합니다. 

## Await

문법은 다음과 같습니다.
```javascript
// works only inside async functions
let value = await promise;
```
`await` 키워드는 자바스크립트가 promise가 셋팅되고 그 결과 값이 리턴될 때까지 기다립니다. 

여기 1초 후에 resolve가 되는 promise 예제가 있습니다. 

```javascript
async function f() {

  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("done!"), 1000)
  });

  let result = await promise; // wait till the promise resolves (*)

  alert(result); // "done!"
}

f();
```

이 함수 실행은 (*) 표시 해둔 곳에서 잠시 기다립니다. 그리곤 promise가 해결 되었을때 다시 재개 합니다. 그래서 위 코드에서 1초 뒤에 "done!" 이라는 메세지를 보게 됩니다. 

강조합시다: `await` 은 문자 그대로  promise가 해결될 때까지 자바스크립트를 기다리게 만듭니다. 그리고 결과가 나오면 계속 진행이 됩니다. 이 작업은 CPU 비용(낭비)이 들지 않습니다. 왜냐하면 엔진은 그 동안에 다른 작업을 할 수 있습니다. : 다른 스크립트나 다른 이벤트를 다룰 수 있습니다.

이것은 promise를 얻기에 `promise.then` 보다 매우 우아한 문법입니다. 그리고 읽고 쓰고가 쉽습니다.

---
### Can’t use `await` in regular functions

만약 `await` 을 동기식 함수에서 사용한다면 문법 에러가 날것 입니다. 
```javascript
function f() {
  let promise = Promise.resolve(1);
  let result = await promise; // Syntax error
}
```

함수 앞에 `async`를 붙이지 않았다면 에러를 마주할 것입니다. 다시 말하지만 `await` 은 오직 `async` 함수 안에서 작동 됩니다. 

---

Promises chaining 챕터에서 썼던 `showAvarar()`를 가져옵시다. 그리고 `async/await` 을 사용해서 다시 작성해 봅시다. 

1. 우리는 `.then` 호출을 `await`으로 바꿀 필요가 있습니다.
2. 또한 우리는 이 작업을 수행하기 위해 `async` 함수로 만들어야 합니다.

```javascript
async function showAvatar() {

  // read our JSON
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();

  // read github user
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();

  // show the avatar
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = "promise-avatar-example";
  document.body.append(img);

  // wait 3 seconds
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));

  img.remove();

  return githubUser;
}

showAvatar();
```

매우 읽기 쉬워지지 않았습니까? 이전보다 훨씬 나아졌습니다.

---
### `await`  won’t work in the top-level code

`await`을 처음 쓰는 사람들은 다음과 같은 사실을 잊어버릴 수 있습니다. 우리는 코드의 최상단에 `await`을 사용할 수 없습니다. 예를 들면 다음과 같은 코드는 작동하지 않습니다.

```javascript
// syntax error in top-level code
let response = await fetch('/article/promise-chaining/user.json');
let user = await response.json();
```

우리는 비동기 함수로 감싸서 할 수 있습니다.
```javascript
(async () => {
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();
  ...
})();
```

---

### `await`  accepts “thenables”

`promise.then` 처럼, `await`는 then 메서드가 있는 객체를 사용할 수 있습니다. 이 아이디어는 서드파티 객체가 만약에 promise가 아니지만 promise 처럼 움직일수 있는: 만약 `.then` 을 지원하는 객체라면 충분히 `await` 을 사용할 수 있습니다. 

여기 데모는 `Thenable` 클래스와 `await`이 이 인스턴스와 함께 사용하는 것입니다.

```javascript
class Thenable {
  constructor(num) {
    this.num = num;
  }
  then(resolve, reject) {
    alert(resolve);
    // resolve with this.num*2 after 1000ms
    setTimeout(() => resolve(this.num * 2), 1000); // (*)
  }
};

async function f() {
  // waits for 1 second, then result becomes 2
  let result = await new Thenable(1);
  alert(result);
}

```

만약에 `await`이 `.then` 을 갖는 promise 객체가 아닐경우 그것은 네이티브 함수 `resolve`, `reject` 를 매개변수로 제공받을 수 있습니다. 그리고 `await`은 그중 하나가 호출 될때까지 기다립니다. 

---

### Async class method

비동기 클래스 메서드를 선언하기 위해서는 단지 `async`만 함수 앞에 붙여주면 됩니다.

```javascript
class Waiter {
  async wait() {
    return await Promise.resolve(1);
  }
}

new Waiter()
  .wait()
  .then(alert); // 1
```

이것의 의미는 똑같습니다. 반환받는 값이 promise이고 `await` 을 사용할 수 있다는 보장을 하는 것입니다.

## Error handling

만약 promise 가 평범하게 귀결이 되었다면, `await promise` 는 값을 리턴 할 것입니다. 하지만 거절의 케이스에서는 error를 던질 것입니다. 만약 해당 라인에 `throw` 문을 만나게 된다면 어떻게 될까

```javascript
async function f() {
  await Promise.reject(new Error("Whoops!"));
}
```

이것은 다음과 같습니다.

```javascript
async function f() {
  throw new Error("Whoops!");
}
```

실제 상황에서 promise는 아마 reject 하기전에 몇분의 시간을 보낼 것입니다. 이 경우 `await` 이 에러를 던지기 까지 delay 가 있을 건데요.

우리는 이때 에러를 `try...catch` 문으로 잡아낼 수 있습니다.

```javascript
async function f() {

  try {
    let response = await fetch('http://no-such-url');
  } catch(err) {
    alert(err); // TypeError: failed to fetch
  }
}

f();
```

에러의 경우 catch 블럭으로 넘어가게 됩니다. 우리는 또한 여러줄을 감싸 줄 수도 있습니다.

```javascript
async function f() {

  try {
    let response = await fetch('/no-user-here');
    let user = await response.json();
  } catch(err) {
    // catches errors both in fetch and response.json
    alert(err);
  }
}

f();
```

만약 `try...catch`문을 가지지 않는다면 f()를 호출해 생성한 promise는 reject 될것입니다. 그러면 우리는 `.catch`에서 그것을 다룰 수 있습니다.

```javascript
async function f() {
  let response = await fetch('http://no-such-url');
}

// f() becomes a rejected promise
f().catch(alert); // TypeError: failed to fetch // (*)
```

만약 `.catch` 를 추가하는 것을 잊어버렸다면 이 promise의 에러를 핸들링 할 수 없습니다. 우리는 [Error handling with promises](https://javascript.info/promise-error-handling) 이 챕터에서 설명한 전역 이벤트 핸들러를 사용해서 몇몇 에러를 잡아낼 수 있습니다. 

--- 
### `async/await`  and  `promise.then/catch`

 우리가 `async/await`을 사용할 때,  `.then`이 거의 필요가 없다, 왜냐하면  `await`  는 우리를 위해 기다려주기 때문이다. 그리고 우리는 대게   `.catch` 대신에 `try..catch`를 사용하기 때문이다.  이 방법은 항상 그렇진 않을 수 있지만 대게 편리합니다.
하지만 코드 최상위에서 `async` 함수 밖에서 사용할때 우리는 문법적으로 `await`을 사용할수가 없습니다. 그래서 우리는 최종 결과 또는 에러를 다루기 위해 `.then/catch` 문을 연습해두어야 합니다. 

위 예제의 (*) 문에서 보는 것과 같이요.

---

### `async/await`  works well with  `Promise.all`

우리가 여러 promise를 기다려야 할때, 우리는 이것들을 `Promise.all` 과 `await` 로 감쌀 수 있습니다. 

```javascript
// wait for the array of results
let results = await Promise.all([
  fetch(url1),
  fetch(url2),
  ...
]);
```

에러가 발생됬을 경우 실패가 `Promise.all` 로 전파가 됩니다. 그래서 발생되는 예외를 호출하는 부분을 `try...catch` 로 감싸 잡아냅니다.

## Summary

함수 전에  `async`  키워드를 적는것은 다음 두가지 효과를 갖는다.

1.  항상 promise를 반환한다. 
2.  안쪽에  `await` 를 사용할 수 있다. 

promise 전에  `await`  키워드를 다는 것은 자바스크립트를 promise 가 해결될때 까지 기다리게 만든다. 그 후에 

1. 만약 에러가 발생해 예외가 생성될 수 있다. 마치  `throw error` 를 호출하는 것과 같이 
2.  반면에 값이 리턴 될 수 있다. 

이들은 함께 읽고 쓸 수있는 비동기 코드를 작성하기위한 훌륭한 프레임 워크를 제공합니다.

async / await를 사용하면 `promise.then / catch` 를 작성할 필요가 거의 없지만 promise을 기반으로한다는 것을 잊지 말아야합니다. 때로는 (예 : 가장 바깥 쪽 범위에서) 이러한 방법을 사용해야하기 때문입니다. 또한 Promise.all 은 많은 작업을 동시에 기다릴 수있는 좋은 방법입니다.