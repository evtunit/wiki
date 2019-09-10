# 자바스크립트 프라미스의 개요

원문 : https://www.sitepoint.com/overview-javascript-promises/

**이 튜토리얼은 자바스크립트 프라미스의 기초를 다루며, 자바스크립트 개발에서 이를 활용하는 방법을 알려줍니다.**

웹개발에서 프라미스 개념은 새로운 것이 아닙니다. Q, wen.js, RSVP.js 등등 에서 이미 많이 사용되어져 왔습니다. jQuery에서 조차도 프라미스와 비슷한 Defferd 객체를 가지고 있습니다. 그러나 이제 자바스크립트에서 프라미스를 기본적으로 지원합니다.

## 개요

프라미스 객체는 아직 사용할 수는 없지만 나중에 특정 시점에서 해결될(resolved) 값을 나타냅니다. 보다 동기적인 방식으로 비동기 코드를 작성할 수 있습니다. 예를 들어 만약 웹서비스에서 비동기 호출을 만들어 내기 위해 프라미스 API를 사용하는 경우, 나중에 웹서비스에서 반환할 데이터인 프라미스 객체를 만듭니다. 주의 사항은 실제 데이터를 아직 사용할 수 없다는 것입니다. 요청이 완료되면 웹서비스에 응답이 반환됩니다. 그러는 동안 프라미스 객체는 실제 데이터에 대한 프록시 역할을 합니다. 또한 콜백을 프라미스 객체에 첨부할 수 있으며 실제 데이터를 사용할 수 있게 되면 호출이 됩니다.

## API

시작하기 위해서 새로운 프라미스 객체를 생성하는 다음 코드를 살펴보겠습니다.:

```javascript
const promise = new Promise((resolve, reject) => {
  //asynchronous code goes here
});
```

새로운 프라미스 객체를 인스턴스화하고 콜백 함수를 전달하는 것으로 시작합니다. 콜백 함수는 resolve와 reject  함수를 두 인자로 전달 합니다.

비동기 코드는 해당 콜백에 들어갑니다. 만약 성공한다면 프라미스는 resolve()함수를 호출하고, 오류가 발생하면 reject() 함수가 Error객체와 함께 호출이 됩니다.

이것은 프라미스가 거부 되었음을 나타냅니다.

프라미스를 사용한 간단한 것을 만들어 봅시다. JSON 포맷으로 반환되는 random joke를 사용한 웹서비스에 비동기 요청을 합니다. 아래의 코드에 프라미스를 사용한 예를 볼 수 있습니다.:

```javascript
const promise = new Promise((resolve, reject) => {
  const request = new XMLHttpRequest();

  request.open('GET', 'https://api.icndb.com/jokes/random');
  request.onload = () => {
    if (request.status === 200) {
      resolve(request.response); // we got data here, so resolve the Promise
    } else {
      reject(Error(request.statusText)); // status is not 200 OK, so reject
    }
  };

  request.onerror = () => {
    reject(Error('Error fetching data.')); // error occurred, reject the  Promise
  };

  request.send(); // send the request
});

console.log('Asynchronous request made.');

promise.then((data) => {
  console.log('Got data! Promise fulfilled.');
  document.body.textContent = JSON.parse(data).value.joke;
}, (error) => {
  console.log('Promise rejected.');
  console.log(error.message);
});
```

이전 코드에서 프라미스 생성자는 웹서비스로 부터 데이터를 가져오는데 사용되는 비동기 코드를 포함가 포함되어 있습니다.

여기에서, https://api.icndb.com/jokes/random에 Ajax 요청으로 생성하여 random joke를 반환합니다. JSON이 웹서버로 부터 응답 받으면, 이것은 resolve() 함수로 보내집니다. 만약 어느 오류라도 발생한다면 reject()는 Error 객체와 함께 호출이 됩니다. 

프라미스 오브젝트가 인스턴스화 되었을 때, 나중에 사용가능한 데이터를 프록시로 부터 받습니다. 앞으로 어느 시점에 웹서비스에서 일부 데이터가 받환 될 것이라고 기대하고 있습니다. 그래서 어떻게 언제 사용 가능한 데이터인지 알 수 있을까요?  언제 사용 가능한 데이터인지 아는 것은 Promise.then() 함수를 사용하는 것입니다. 함수는 성공한 경우의 콜백 함수와 실패한 경우의 콜백함수인 2개의 인자를 가집니다. 이들 콜백함수는 프라미스가 정해지면 (즉, 실행 또는 거부) 호출이 됩니다. 만약 프라미스가 실행이 되어 진다면 성공한 콜백은 resolve()에 전달한 실제 데이터를 받을 수 있습니다. 만약 프라미스가 거부 된다면 실패한 콜백이 호출 됩니다. reject() 함수가 전달한 것은 콜백에 인수로 전달됩니다.

코드펜의 예시 입니다. 새로운 random joke를  보려면 코드펜이 삽입된 화면에서 오른쪽 하단에 있는 RETURN 버튼을 누르세요.  또한 코드의 다른 부분이 실행되는 순서를 볼 수 있도록 브라우저 콘솔을 열어주세요.

[코드펜코드](https://codepen.io/SitePoint/pen/eVaXdP)
```javascript
const promise = new Promise((resolve, reject) => {
  const request = new XMLHttpRequest();

  request.open("GET", "https://api.icndb.com/jokes/random");
  request.onload = () => {
    if (request.status === 200) {
      resolve(request.response); 
    } else {
      reject(Error(request.statusText));
    }
  };

  request.onerror = () => {
    reject(Error("Error fetching data."));
  };

  request.send();
});

console.log("Asynchronous request made.");

promise.then(
  data => {
    console.log("Got data! Promise fulfilled.");
    document.body.textContent = JSON.parse(data).value.joke;
  },
  error => {
    console.log("Promise rejected.");
    console.log(error.message);
  }
);
```


프라미스에는 세 가지 상태가 있을 수 있습니다.:

- 보류중 (실행도 아니고 거부도 아님)
- 실행
- 거부

코드에서 접근할 수 없고 비공개인 Promise.status 속성은 이렇나 상태에 대한 정보를 제공합니다. 프라미스가 거부되거나 실행되면 상태는 영구적으로 연관이 되어 집니다. 이 의미는 프라미스는 한 번만 성공하거나 실패할 수 있음을 의미합니다. 만약 프라미스가 이미 실행되었고 나중에 두 개의 콜백으로 then()을 첨부하면 성공 콜백이 올바르게 호출됩니다. 그래서 프라미스의 세계에서는 프라미스가 언제 정해지는지에 관심이 없습니다. 프라미스는 최종 결과에만 관심이 있습니다.

## 프라미스 체이닝

가끔씩은 프라미스를 체이닝하는 것이 바람직합니다. 예를 들어 여러 비동기 작업을 수행할때 입니다. 한 작업에서 데이터를 제공하면 해당 데이터 등에서 다른 작업을 수행하기 시작합니다. 프라미스는 아래의 예시 코드에서 처럼 함께 묶을 수 있을 수 있습니다. 

```javascript
function getPromise(url) {
  // return a Promise here
  // send an async request to the url as a part of promise
  // after getting the result, resolve the promise with it
}

const promise = getPromise('some url here');

promise.then((result) => {
  //we have our result here
  return getPromise(result); //return a promise here again
}).then((result) => {
  //handle the final result
});
```

까다로운 부분은 then()안에 간단한 값을 반환하면 그 다음 반환 값으로 다음 then()이 호출된다는 것입니다. 그러나 만약 then()안에 프라미스를 반환하면 다음 then()가 그 프라미스를 기다렸다가 그 프라미스가 정해지면 호출이 됩니다.

## 에러 핸들링

then()함수는 두 개의 콜백을 인자로 받는다는 것을 알고 있습니다. 프라미스가 거부되면 두번째 프라미스가 호출이 될 것입니다. 그러나 프라미스를 거부를 처리하는 데 사용할 수 있는 catch()함수도 있습니다. 다음 코드를 살펴보십시오.

```javascript
promise.then((result) => {
  console.log('Got data!', result);
}).catch((error) => {
  console.log('Error occurred!', error);
});
```

위의 코드는 아래의 코드와 같습니다.

```javascript
promise.then((result) => {
  console.log('Got data!', result);
}).then(undefined, (error) => {A
  console.log('Error occurred!', erroAr);
});
```

프라미스가 거부되었고 then()에 실패 콜백이 없으면, 제어는 실패 콜백 또는 다음 catch() 그다음 then으로 진행이 됩니다. 명시적은 프라미스 거부와 달리 catch()는 프라미스 생성자 콜백에서 예외가 발생하면 호출됩니다. 그래서 로그 기록을 위해 catch()를 사용합니다. try...catch를 사용하여 오류를 처리할 수 있지만 비동기 또는 동기 오류는 'catch()'에 의해 잡히므로 프라미스에는 필요하지 않습니다.

## 결론

자바스크립트의 새로운 프라미스 API를 소개하였습니다. 분명히 비동기 코드를 매우 쉽게 작성할 수 있습니다.  나중에 비동기 코드에서 어떤 값이 반환될 지 모른 채 평소대로 진행할 수도 있습니다. 여기에서 다루지 않은 API가 더 있습니다. 프라미스에 좀 더 많은 내용을 배우기 원한다면  [A Deeper Dive Into JavaScript Promises](https://www.sitepoint.com/deeper-dive-javascript-promises/) 글을 좀 더 확인하세요.