# 비동기 자바스크립트: 콜백지옥 에서 부터 Async/Await

원문 : https://www.toptal.com/javascript/asynchronous-javascript-async-await-tutorial

성공적인 웹 어플리케이션을 개발하기 위한 키 중에 하나가 페이지당 수십 개의 AJAX를 호출하는 것입니다.

페이지당 수십개의 AJAX를 호출하는 것은 일반적인 비동기 프로그래밍에서 직면하는 문제이고, 비동기 호출을 다루는 방법은 대부분 앱을 만들거나 중단하거나, 잠재적으로 전체를 다시 시작하는 것입니다.

> 자바스크립트에서 비동기작업을 동기화 시키는 작업은 매우 오랜 시간동안 심각한 문제가 되어왔다.

이 문제는 자바스크립트 프레임워크를 사용하는 프론트엔드 개발자만큼 [Node.js](https://www.toptal.com/nodejs)를 사용하는 백엔드 개발자에게도 영향을 줍니다. 

## 비동기 자바스크립트의 (간략한)역사



첫번째 이자 가장 간단한 해결 방법은 중첩된 함수의 형태로의 콜백이었습니다. 이 해결 방법은 콜백지옥을 발생시켰고, 여전히 수 많은 어플리케이션에 손상을 주었습니다.

그러고나서 우리는 프라미스를 알게 되었다. 프라미스 패턴은 좀 더 읽기 쉬운 코드로 만들지만 프라미스 패턴은 `Don’t Repeat Yourself (DRY)` 원칙에는 많이 어긋납니다. 어플리케이션의 흐름을 잘 관리하기 위해 동일한 코드를 반복해야하는 경우가 많습니다. 최근에 추가된 async/await문이 자바스크립트에서 마침내 비동기 코드를 다른 코드 조각 저럼 쉽게 읽고 쓸 수 있게 하였습니다. 

각 해결 방법의 예를 살펴보고 자바스크립트에서 비동기 프로그래밍의 발전에 대해 생각해봅시다. 

이를 위해 다음 단계를 수행하는 간단한 작업을 살펴 보겠습니다.

1. 사용자의 이름과 비밀번호를 확인합니다.
2. 사용자의 애플리케이션에서 역할을 가져 옵니다.
3. 사용자의 애플리케이션에 접근한 시간을 기록합니다.

## 접근법 1 :  (“The Pyramid of Doom”)접근1 : 콜백 지옥("피라미드의 운명")



호출을 동기화 하는 오래된 해결 방법은 중첩된 콜백을 통해 이루어졌습니다. 이것은 간단한 비동기 자바스크립트 작업에 대한 적절한 접근 방법이지만 [콜백 지옥](http://callbackhell.com/)이라는 문제로 인해 완전히 해결되지는 않았습니다.



![Illustration: Asynchronous JavaScript callback hell anti-pattern](https://uploads.toptal.io/blog/image/125145/toptal-blog-image-1515767755732-42c95d08c90e52d1bd534c7c880ea75b.png)



3개의 간단한 작업을 하는 코드는 아래에서 볼 수 있습니다.

```javascript
const verifyUser = function(username, password, callback){
   dataBase.verifyUser(username, password, (error, userInfo) => {
       if (error) {
           callback(error)
       }else{
           dataBase.getRoles(username, (error, roles) => {
               if (error){
                   callback(error)
               }else {
                   dataBase.logAccess(username, (error) => {
                       if (error){
                           callback(error);
                       }else{
                           callback(null, userInfo, roles);
                       }
                   })
               }
           })
       }
   })
};
```

각각의 함수는 이전 작업에 대한 응답의 매개 변수와 함께 호출되는 다른 함수를 인자로 가져옵니다.

대부분의 사람들은 위의 문장을 읽음으로써 뇌가 어는 듯한 경험을 겪을 것이다. 수백 개의 비슷한 코드 블럭을 가진 어플리케이션이 있다면 코드를 직접 작성하더라도 코드를 유지 관리하는 사람에게는 더 많은 문제가 발생할 수 있습니다.

`database.getRoles`가 중첩된 콜백을 가진 또 다른 함수라는 것으 알게 되면 이 예제는 훨씬 더 복잡해집니다.

```javascript
const getRoles = function (username, callback){
   database.connect((connection) => {
       connection.query('get roles sql', (result) => {
           callback(null, result);
       })
   });
};
```

이 경우는 DRY 원칙에서 관리하기 어려운 코드를 갖는 것 외에도 아무런 값을 가지지 않습니다.

DRY 원칙은 관리하기 어려운 코드를 갖는 것 외에도 이 경우에는 아무런 값을 가지지 않습니다. 예를 들어 에러핸들링시 각 함수에서 반복되며 기본 콜백은 각 중첩 함수에서 호출됩니다.

비동히 호출을 통해 반복을 하는 것과 같은 훨씬 더 복잡한 비동기 자바스크립트 작업은 훨씬 더 큰 과제 입니다. 실제로, 콜백과 함께 실행하는 간단한 방법은 없기 때문에   [Bluebird](http://bluebirdjs.com/docs/getting-started.html) 와 [Q](https://github.com/kriskowal/q) 와 같은 프라미스 라이브러리가 큰 관심을 끌었습니다. Bluebird와 Q는 언어 자체가 제공하지 않는 비동기 요청에 대해 공통 작업을 수행할 수 있는 방법을 제공합니다.

이 것이 바로 네이티브 자바스크립트 프라미스입니다.

## 접근법 2 : 자바스크립트 프라미스



[프라미스](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)는 콜백지옥에서 탈출하는 다음 단계입니다. 프라미스 메소드는 콜백의 사용을 제거하지는 않았지만 함수를 간단하게 체이닝하고(연결하고) 코드를 [단순화](https://www.toptal.com/javascript/javascript-es6-cheat-sheet)하고 좀 더 읽기 쉬운 코드가 되었습니다.



![Illustration: Asynchronous JavaScript Promises diagram](https://uploads.toptal.io/blog/image/125146/toptal-blog-image-1515767816714-7aa8dd1b3f99930f924539e4d71fc386.png)

프라미스를 사용한 비동기 자바스크립트의 예제의 코드는 다음과 같습니다.

```javascript
const verifyUser = function(username, password) {
   database.verifyUser(username, password)
       .then(userInfo => dataBase.getRoles(userInfo))
       .then(rolesInfo => dataBase.logAccess(rolesInfo))
       .then(finalResult => {
           //do whatever the 'callback' would do
       })
       .catch((err) => {
           //do whatever the error handler needs
       });
};
```

이러한 종류의 단순성을 적용하기 위해 이 예제에서 사용된 모든 기능은 *Promisified*되어야 합니다. 어떻게  프라미스를 반환하기 위해 `getRoles` 메소드가 어떻게 업데이트 되는지 살펴 봅시다.

```javascript
const getRoles = function (username){
   return new Promise((resolve, reject) => {
       database.connect((connection) => {
           connection.query('get roles sql', (result) => {
               resolve(result);
           })
       });
   });
};
```

두 개의 콜백과 함께 프라미스를 반환하도록  메소드를 수정했으며 프라미스 자체가 메소드에서 작업을 수행합니다. 이제 `resolve` 와 `reject` 콜백은 각각  `Promise.then` 와 `Promise.catch` 메소드에 매핑이 될  것입니다.

`getRoles`메소드는 여전히 내부적으로 운명의 피라미드 현상이 발생하기 쉽다는 것을 알 수 있습니다. 운명의 피라미드 현상은 프라미스를 반환하지 않은 데이터베이스 메소드가 만들어지기 때문입니다. 

만약 데이터베이스가 프라미스가 반환되어진 메소드에 접근을 하게 된다면 `getRoles` 메소드는 다음과 같습니다.

```javascript
const getRoles = new function (userInfo) {
   return new Promise((resolve, reject) => {
       database.connect()
           .then((connection) => connection.query('get roles sql'))
           .then((result) => resolve(result))
           .catch(reject)
   });
};
```



## 접근법3 : Async/Await



자바스크립트는 기본적으로 비동기적입니다. 자바스크립트가 기본적으로 비동기적인 이유가 자바스크립트에서 올바르게 실행되는 동기적인 코드를 얻는 데 시간이 오래 걸린 이유일 수 있습니다. 그러나 결코 늦지 않는 것이 좋습니다. 운명의 피라미드는 프라미스의 도입으로 크게 완화되었습니다. 그러나 여전히 프라미스 메소드`.then` 와 `.catch` 가 전달되어 지는 콜백에 의존하고 있습니다.