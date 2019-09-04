# Javascript 프라미스 

> 원문: [JavaScript Promises for Dummies](https://scotch.io/tutorials/javascript-promises-for-dummies)

**자바스크립트  프라미스**는 어렵지 않다. 그러나 많은 사람들은 처음에 이해하기가 조금 어렵다는 것을 안다. 따라서 프라마스를 이해하는 방법을 더 쉬운 방법으로 적으려 한다.

## 프라미스 이해하기 

프라미스는 간단히 말해서 아래와 같다.

"당신이 **어린아이**라고 상상해보자. 엄마가 다음 주에 **새로운 휴대전화**를 사주겠다고 **약속(promise)** 했다"

다음 주가 되기전까지 휴대전화를 받을 수 있을지 알수가 없다. 엄마가 정말로 새 휴대전화를 사줄 수도 있고, 사주지 않고 보류할 수도 있다.

이게 바로 **프라미스**이다. 프라미스는 3가지의 상태 값을 가진다.

1.  Pending: 새로운 휴대전화를 받을지도 모르는 상태 
2.  Fulfilled: 엄마가 새로운 휴대전화를 사준 상태
3.  Rejected: 엄마가 구매를 보류한 상태

## 프라미스 만들기 

자, 이제 이것들을 실제 자바스크립트로 구현해 보자.

```js
/_ ES5 _/
var isMomHappy = false;

// Promise
var willIGetNewPhone = new Promise(
    function (resolve, reject) {
        if (isMomHappy) {
            var phone = {
                brand: 'Samsung',
                color: 'black'
            };
            resolve(phone); // fulfilled
        } else {
            var reason = new Error('mom is not happy');
            reject(reason); // reject
        }

    }
);
```

이 코드는 이 자체로 꽤 표현력이 있다.

```js
// 프라미스 구문은 아래와 같다.
new Promise(function (resolve, reject) { ... } );
```

## 프라미스 사용하기

이제 프라미스를 얻었으니, 사용해 보자. 위의 예제와 이어진다.

```js
/_ ES5 _/
...

// call our promise
var askMom = function () {
    willIGetNewPhone
        .then(function (fulfilled) {
            // 새로운 휴대전화가 생겼다!
            console.log(fulfilled);
         // output: { brand: 'Samsung', color: 'black' }
        })
        .catch(function (error) {
            // 이런, 엄마가 사주지 않았어..
            console.log(error.message);
         // output: 'mom is not happy'
        });
};

askMom();
```

예제를 돌려보고 결과를 확인해 보자!

Demo: [https://jsbin.com/nifocu/1/edit?js,console](https://jsbin.com/nifocu/1/edit?js,console)

![Result](https://scotch-res.cloudinary.com/image/upload/w_800,q_auto:good,f_auto/media/272/nB0ffh3NQHaHKJRuBNpH_promise-true-false.mp4)

## 프라미스 체이닝

프라미스는 chainable하다. 이어서 호출이 가능하다는 이야기이다.

다시 상상해보자. 당신은 어린아이이고, 친구에게 엄마가 새로운 **휴대전화를 사주면 보여주겠다**고 **약속**한다.

이제 또다른 프라미스가 생겼다. 작성해보자.

```js
...

// 2nd promise
var showOff = function (phone) {
    return new Promise(
        function (resolve, reject) {
            var message = 'Hey friend, I have a new ' +
                phone.color + ' ' + phone.brand + ' phone';

            resolve(message);
        }
    );
};
```

축약하자면 :

```javascript
// 위의 예제를 축약해보자
...

// 2nd promise
var showOff = function (phone) {
 var message = 'Hey friend, I have a new ' +
                phone.color + ' ' + phone.brand + ' phone';

    return Promise.resolve(message);
};
```

프라미스를 체이닝 해보자. `willIGetNewPhone` 프라미스 후에 `showOff` 프라미스를 이행할 할 것이다.

```javascript
...

// 우리의 프라미스를 호출해보자.
var askMom = function () {
    willIGetNewPhone
    .then(showOff) // chain it here
    .then(function (fulfilled) {
            console.log(fulfilled);
         // output: 'Hey friend, I have a new black Samsung phone.'
        })
        .catch(function (error) {
            // 이런, 엄마가 사주지 않았어..
            console.log(error.message);
         // output: 'mom is not happy'
        });
};
```

얼마나 프라미스를 체이닝하는게 쉬운지 알겠는가?

## 프라미스는 비동기

프라미스는 비동기이다. 우리가 프라미스를 호출 하기 전과 후에 로그 메시지를 보자.

```javascript
// 프라미스를 호출
var askMom = function () {
    console.log('엄마에게 묻기 전'); // log before
    willIGetNewPhone
        .then(showOff)
        .then(function (fulfilled) {
            console.log(fulfilled);
        })
        .catch(function (error) {
            console.log(error.message);
        });
    console.log('엄마에게 물은 후'); // log after
}
```

예상 출력 순서는 어떠한가? 아마도 다음을 예상할 것이다.

```txt
1. before asking Mom
2. Hey friend, I have a new black Samsung phone.
3. after asking mom
```

그러나 실제 출력 순서는 아래와 같다.

```txt
1. before asking Mom
2. after asking mom
3. Hey friend, I have a new black Samsung phone.
```

![Output](https://scotch-res.cloudinary.com/image/upload/w_800,q_auto:good,f_auto/media/272/X8Q1iZGeSqAEjksPdbQQ_bZ3MNiH8g1.mp4)

### 왜 그럴까? 세월은 사람을 기다려주지 않는다.

어린아이는 엄마가 약속(새로운 휴대전화)을 지킬때 까지 기다리면서 가만히 있지 않는다. 그렇지? 이 것을 **비동기**라고 부르는 것이다.  프라미스가 진행 될때까지 기다려야하는 것은 `then`에 넣어야 한다.


##  ES5, ES6/2015, ES7/Next 의 프라미스

### ES5 - 다수의 브라우저

[Bluebird](http://bluebirdjs.com/docs/getting-started.html) 프라미스 라이브러리를 include 하면, 예제들은 ES5 환경에서 동작할 것이다. ES5는 프라미스를 지원하지 않는다. 또다른 유명한 프라미스 라이브러리는 [Q](https://github.com/kriskowal/q)이다.

 ### ES6 / ES2015 - 모던 브라우저, NodeJs v6

예제들은 잘 동작할 것이다. ES6는 자체적으로 프라미스를 지원한다. 추가로 ES6에선 `const`와 `let`을 이용하여 함수를 화살표로 이용하여 표현할 수 있다.

여기 ES6 예제 코드가 있다.

```javascript
/_ ES6 _/
const isMomHappy = true;

// Promise
const willIGetNewPhone = new Promise(
    (resolve, reject) => { // fat arrow
        if (isMomHappy) {
            const phone = {
                brand: 'Samsung',
                color: 'black'
            };
            resolve(phone);
        } else {
            const reason = new Error('mom is not happy');
            reject(reason);
        }

    }
);

const showOff = function (phone) {
    const message = 'Hey friend, I have a new ' +
                phone.color + ' ' + phone.brand + ' phone';
    return Promise.resolve(message);
};

// call our promise
const askMom = function () {
    willIGetNewPhone
        .then(showOff)
        .then(fulfilled => console.log(fulfilled)) // fat arrow
        .catch(error => console.log(error.message)); // fat arrow
};

askMom();
```

모든 `var` 들은 `const`로 대체되었다. 모든 `function(resolve, reject)`는 단순하게 `(resolve, reject) =>`로 변했다.

### ES7 - Async Await: 코드가 더 예쁘게 보이도록 만들기

ES7는 `async`및 `await` 구문을 소개했다. 이 것들은 `.then`하고 `.catch`를 사용하지 않고 비동기 구문을  이쁘게 보이게 하며 이해하기 쉽게 만든다.

우리의 예제를 ES7 구문으로 만들어 보자.

```javascript
/_ ES7 _/
const isMomHappy = true;

// Promise
const willIGetNewPhone = new Promise(
    (resolve, reject) => {
        if (isMomHappy) {
            const phone = {
                brand: 'Samsung',
                color: 'black'
            };
            resolve(phone);
        } else {
            const reason = new Error('mom is not happy');
            reject(reason);
        }

    }
);

// 2nd promise
async function showOff(phone) {
    return new Promise(
        (resolve, reject) => {
            var message = 'Hey friend, I have a new ' +
                phone.color + ' ' + phone.brand + ' phone';

            resolve(message);
        }
    );
};

// call our promise
async function askMom() {
    try {
        console.log('before asking Mom');

        let phone = await willIGetNewPhone;
        let message = await showOff(phone);

        console.log(message);
        console.log('after asking mom');
    }
    catch (error) {
        console.log(error.message);
    }
}

(async () => {
    await askMom();
})();
```

## 왜 프라미스이며 언제 써야할까?

왜 프라미스가 필요할까? 프라미스 이전에는 코드를 어떻게 작성했을까? 이 질문에 대답하기 전에 기본으로 돌아가보자.

### 일반 함수 vs Async 함수 

이 두가지 예를 살펴보자. 두 예 모두 두개의 숫자를 더한다. 하나는 일반적인 함수 통해 추가하고, 다른 하나는 원격으로 추가 한다.

#### 두개의 숫자를 더하는 일반 함수 

```js
// add two numbers normally

function add (num1, num2) {
    return num1 + num2;
}

const result = add(1, 2); // you get result = 3 immediately
```

#### 두개의 숫자를 더하는 Async 함수 

```js
// add two numbers remotely

// get the result by calling an API
const result = getAddResultFromServer('http://www.example.com?num1=1&num2=2');
// you get result  = "undefined"
```

일반 함수를 이용하여 숫자를 더하면 결과가 즉시 나타난다. 그러나 Async 함수를 이용해 원격 호출을 하면 기다려야하며 결과를 즉시 얻을 수 없다.

이런 식으로 작업하게 되면 서버가 다운되거나 응답 속도가 느려져 결과를 얻을 수 있을지 보장할 수 없다. 또한 결과를 기다리는 동안 전체 프로세스가 차단되는 것을 원하지 않을 것이다.

API 호출, 파일 다운로드, 파일 읽기는 일반적인 비동기 작업 중 하나이다.

#### 프라미스 이전의 세계: Callback

비동기 호출에 꼭 프라미스를 사용해야할까?  그건 아니다. 프라미스가 소개되기 이전엔 콜백을 사용했다. 콜백은 반환 결과를 얻을 때 호출하는 함수이다. 이전 예제를 콜백을 이용하도록 수정 해 보자.

```js
// add two numbers remotely
// get the result by calling an API

function addAsync (num1, num2, callback) {
    // use the famous jQuery getJSON callback API
    return $.getJSON('http://www.example.com', {
        num1: num1,
        num2: num2
    }, callback);
}

addAsync(1, 2, success => {
    // callback
    const result = success; // you get result = 3 here
});
```

위의 구문은 괜찮아보인다. 그럼 왜 프라미스를  써야할까?

#### 비동기 작업 이후 또다른 비동기 작업을 수행하려는 경우엔 어떡하지?

숫자를 한번만 더하지 않고 3번을 더해보려고한다. 일반 함수에선 다음과 같이한다.

```js
// add two numbers normally

let resultA, resultB, resultC;

 function add (num1, num2) {
    return num1 + num2;
}

resultA = add(1, 2); // you get resultA = 3 immediately
resultB = add(resultA, 3); // you get resultB = 6 immediately
resultC = add(resultB, 4); // you get resultC = 10 immediately

console.log('total' + resultC);
console.log(resultA, resultB, resultC);
```

콜백을 사용하면 어떻게 될까?

```js
// add two numbers remotely
// get the result by calling an API

let resultA, resultB, resultC;

function addAsync (num1, num2, callback) {
    // use the famous jQuery getJSON callback API
    return $.getJSON('http://www.example.com', {
        num1: num1,
        num2: num2
    }, callback);
}

addAsync(1, 2, success => {
    // callback 1
    resultA = success; // you get result = 3 here

    addAsync(resultA, 3, success => {
        // callback 2
        resultB = success; // you get result = 6 here

        addAsync(resultB, 4, success => {
            // callback 3
            resultC = success; // you get result = 10 here

            console.log('total' + resultC);
            console.log(resultA, resultB, resultC);
        });
    });
});
```

예제: [https://jsbin.com/barimo/edit?html,js,console](https://jsbin.com/barimo/edit?html,js,console)

이 구문은 친숙하지 않다. 피라미드 처럼 보이지만 사람들은 주로 이것을 "콜백 지옥"이라 부른다. 콜백은 또다른 콜백에 중첩되어있기 때문이다. 콜백이 10개가 있다고 가정하면, 콜백은 10번 중첩된다!

### 콜백 지옥에서 벗어나기

프라미스는 콜백 지옥을 벗어나게해줄 구원자이다. 같은 예제를 프라미스로 구현해 보자.

```js
// add two numbers remotely using observable

let resultA, resultB, resultC;

function addAsync(num1, num2) {
    // use ES6 fetch API, which return a promise
    return fetch(`http://www.example.com?num1=${num1}&num2=${num2}`)
        .then(x => x.json());
}

addAsync(1, 2)
    .then(success => {
        resultA = success;
        return resultA;
    })
    .then(success => addAsync(success, 3))
    .then(success => {
        resultB = success;
        return resultB;
    })
    .then(success => addAsync(success, 4))
    .then(success => {
        resultC = success;
        return resultC;
    })
    .then(success => {
        console.log('total: ' + success)
        console.log(resultA, resultB, resultC)
    });
```

예제: [https://jsbin.com/qafane/edit?js,console](https://jsbin.com/qafane/edit?js,console)

프라미스를 사용하면  콜백을 `then`로 평탄화한다. 콜백 중첩이 없기 때문에 매우 깔끔해 보인다. 물론 ES7 비동기 구문을 사용하면이 예제를 더욱 향상시킬 수는 있지만 그건 여러분의 몫으로 남긴다 :)

## 새로운 녀석: Observables

프라미스로 정착하기 전에, `Observables`라고 불리는 비동기 데이터를 훨씬 더 쉽게 다룰 수 있게하는 것이 있다.

> Observables는 0 개 이상의 이벤트를 생성 또는 완료 또는 완료되지 않을 수 있는 lazy 이벤트 스트림이다. [source](https://cycle.js.org/streams.html)

프라미스와 Observables의 주요 차이점은 다음과 같다.

두려워 말고 Observables로 작성된 동일한 예제를 보자. 이 예제에서는 Observable를 위해 [RxJS](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html)를 사용한다.

```javascript
let Observable = Rx.Observable;
let resultA, resultB, resultC;

function addAsync(num1, num2) {
    // use ES6 fetch API, which return a promise
    const promise = fetch(`http://www.example.com?num1=${num1}&num2=${num2}`)
        .then(x => x.json());

    return Observable.fromPromise(promise);
}

addAsync(1,2)
  .do(x => resultA = x)
  .flatMap(x => addAsync(x, 3))
  .do(x => resultB = x)
  .flatMap(x => addAsync(x, 4))
  .do(x => resultC = x)
  .subscribe(x => {
    console.log('total: ' + x)
    console.log(resultA, resultB, resultC)
  });
```

예제:  [https://jsbin.com/dosaviwalu/edit?js,console](https://jsbin.com/dosaviwalu/edit?js,console)

Observables은 좀 더 펑키하고 쉽게 수행할 수 있다. 예를 들어 코드 한 줄 만으로 add 함수를 `3초 지연`하거나 특정 횟수의 호출을 재시도할 수 있도록 설정할 수 있다.

```js
...

addAsync(1,2)
  .delay(3000) // delay 3 seconds
  .do(x => resultA = x)
  ...
```

다음 글에서 Observables에 대해 이야기해 보자!

## 요약

콜백 및 프라미스에 익숙해 져라. 이것들을 이해하고 사용해라. Observables에 대해서는 아직 걱정하지 말고! 세 가지 모두 상황에 따라 개발에 영향을 줄 수 있다.

이 글이  JavaScript 프라미스를 배우는 것에 도움이 됐으면 좋겠다. Happy coding!