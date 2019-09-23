# 최근에 쓰이고있는 비동기 방식인 Async, Await
> 원문: https://flaviocopes.com/javascript-async-await/

JavaScript에서 비동기에 대한 접근 방식을을 소개합니다.  
JavaScript는 콜백에 매우 짧은 시간 내에 성장했으며, Async, Await 구문을 사용하면 ES2017 비동가 훨씬 더 간단합니다.


## Introduction
비동기 기능은 Promise과 Generators의 조합이며, 기본적으로 Promise보다 더 높은 수준의 추상화입니다.  
다시 한 번 말씀드리지만, Async/Await는 Promise에 따라 만들어집니다.

## Why were async/await introduced?
ES2015 Promise가 도입되었을 때, 비동기 코드로 문제를 해결하기 위한 것입니다.  
그러나 ES2015와 ES2017을 분리한 2년 동안 Promise는 최종 해결책이 될 수 없다는 것이 명백했습니다.

유명한 콜백 지옥 문제를 해결하기 위해 Promise들이 도입되었지만, 코드를 설계하다 보면 자연스럽게 코드는 복잡성과 구문 복잡성을 갖게됩니다. 
개발자들은 더 나은 구문필요한 시기였습니다. 그래서 적절한 시기에 비동기 기능을 사용하게 되었습니다.  

코드를 동기식으로 만들지만, 뒤에서 비동기식으로 막고 차단하지 않습니다. 


## How it works
비동기 기능은 다음과 같은 Promise를 반환합니다.

```js
const doSomethingAsync = () => {
  return new Promise(resolve => {
    setTimeout(() => resolve('I did something'), 3000)
  })
}
```

이 기능을 호출하려면 미리 기다리십시오.  
그러면 Promise가 해결되거나 거부될 때까지 call이 중지됩니다.  

한 가지 주의 사항: 클라이언트 기능은 비동기식으로 정의되어야 합니다. 다음은 다음과 같은 예입니다.

```js
const doSomething = async () => {
  console.log(await doSomethingAsync())
}
```


## A quick example
다음은 비동기식으로 기능을 실행하는 데 사용되는 Async/await의 간단한 예입니다.

```js
const doSomethingAsync = () => {
  return new Promise(resolve => {
    setTimeout(() => resolve('I did something'), 3000)
  })
}

const doSomething = async () => {
  console.log(await doSomethingAsync())
}

console.log('Before')
doSomething()
console.log('After')
```

위의 코드는 브라우저 콘솔에 다음을 출력합니다.

```bash
Before
After
I did something //after 3s
```

## Promise all the things

모든 기능에 비동기 키워드를 준비한다는 것은 기능이 Promise를 반환한다는 것을 의미합니다.

명시적으로 안 하고 있다 하더라도 내부적으로 Promise를 return 할 것입니다. 이 코드가 유효한 이유는 다음과 같습니다.

```js
const aFunction = async () => {
  return 'test'
}

aFunction().then(alert) // This will alert 'test'
```

아래의 코드와 동일합니다.

```js
const aFunction = async () => {
  return Promise.resolve('test')
}

aFunction().then(alert) // This will alert 'test'
```


## The code is much simpler to read

위의 예에서 볼 수 있듯이 비동기 코드는 매우 단순해 보입니다.  
Promise, chaining 및 callback을 사용하여 코드와 비교합니다.

코드가 훨씬 더 복잡할 때 주요 이점이 발생할 것입니다.

예를 들어, promise를 사용하여 JSON 리소스를 가져와서 구문 분석하는 방법은 다음과 같습니다.

```js
const getFirstUserData = () => {
  return fetch('/users.json') // get users list
    .then(response => response.json()) // parse JSON
    .then(users => users[0]) // pick first user
    .then(user => fetch(`/users/${user.name}`)) // get user data
    .then(userResponse => userResponse.json()) // parse JSON
}

getFirstUserData()
```

다음은 Async/Await를 사용하여 제공되는 기능과 동일한 기능입니다.

```js
const getFirstUserData = async () => {
  const response = await fetch('/users.json') // get users list
  const users = await response.json() // parse JSON
  const user = users[0] // pick first user
  const userResponse = await fetch(`/users/${user.name}`) // get user data
  const userData = await userResponse.json() // parse JSON
  return userData
}

getFirstUserData()
```


## Multiple async functions in series
비동기함수는 체이닝을 이용해 쉽게 연결하여 사용할 수 있고, 가독성도 좋습니다.

```js
const promiseToDoSomething = () => {
  return new Promise(resolve => {
    setTimeout(() => resolve('I did something'), 10000)
  })
}

const watchOverSomeoneDoingSomething = async () => {
  const something = await promiseToDoSomething()
  return something + ' and I watched'
}

const watchOverSomeoneWatchingSomeoneDoingSomething = async () => {
  const something = await watchOverSomeoneDoingSomething()
  return something + ' and I watched as well'
}

watchOverSomeoneWatchingSomeoneDoingSomething().then(res => {
  console.log(res)
})
```

출력

```
I did something and I watched and I watched as well
```