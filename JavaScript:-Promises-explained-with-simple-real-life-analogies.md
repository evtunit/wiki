
# JavaScript: Promises explained with simple real life analogies

번역 : [https://codeburst.io/javascript-promises-explained-with-simple-real-life-analogies-dd6908092138](https://codeburst.io/javascript-promises-explained-with-simple-real-life-analogies-dd6908092138)

## Promise: In Layman terms

두 사람의 대화를 생각해 봅시다. 

**Alex**: 이봐 Promise! 저 길 아래 store에 가서 오늘 요리에 쓰일  `itemA` 좀 가져다 주겠어?

**Mr. Promise**: 그래!

**Alex**: 너가 다녀오는동안 내가 `itemB(비동기 작업)` 을 준비해 놓을게. 대신에 `itemA(promise return value)` 를 찾았는지 안찾았는지 알려줘요.

**Mr. Promise**: 만약 내가 돌아 왔을때 집에 없으면?

**Alex**: 너가 돌아왔을때 메세지로 나에게 알려주고 그 item 좀 가져다 줘 `(success callback)`.  혹시 너가 찾지 못하면, 나에게 즉시 알려줘 `(failure callback)`.

**Mr. Promise** 그거 좋네! 조금 있다봐.

꽤 간단한 이야기 입니다. `promise` 객체는 비동기 함수에 의해 리턴되는 데이터 입니다. 그것은 만약 그 함수가 성공적이라면 `resolve` 가 될 수 있습니다. 또는 실패 했다면 `reject` 가 될 수 있습니다. 

## Promise: The definition

> `Promise` 는 비동기 작업에 대해서 결국 성공과 실패를 대변하는 객체 입니다. 근본적으로 promise는 콜백을 함수에 전달하는 대신 콜백을 첨부하는 객체가 리턴되는 것이다.

## Promise: In JavaScript

처음으로 Javascript 와 그것의 동시성에 대해서 이야기 해보자. 자바스크립트는 싱글 쓰레드 입니다. 순서대로 작성순서대로 모든것은 실행되게 된다. 하지만 비동기 작업은 그것들이 끝나고 나서 발생되게 된다. 

다음 예제에서 어떻게 콘솔에 찍힐거 같습니까?

```javascript
console.log("1");  
setTimeout(function(){console.log("2");},3000);  
console.log("3");  
setTimeout(function(){console.log("4");},1000);
```
결과는 `1 3 4 2` 입니다. 왜 2 전에 4가 찍혀잇는지 궁금해 할것입니다. 

`line 4` 이전에 `line 2` 가 실행이 되더라도 `3000ms` 동안은 실행되지 않습니다. 그러므로 `4` 가 `2` 이전에 로깅 됩니다.

전형적인 web-app의 경우 멀티 비동기 작업들을 수행해야 할 경우가 있습니다. 예를 들어 이미지를 가져온다거나 endpoint 에서 JSON 데이터를 가져오거나, API 통신 등등을 해야 합니다. 

그러면 자바스크립트의 promise가 어떻게 생성되는지 알아봅시다.

```javascript
var promise = new Promise(function(resolve, reject) {  
// do a thing, possibly async, then…  
  
if (/* everything turned out fine */) {  
resolve("Stuff worked!");  
}  
else {  
reject(Error("It broke"));  
}  
});
```

promise 생성자는 하나의 매개변수를 받습니다. 2개의 파라미터를 지니는 `callback` 함수를 받습니다. -- `resolve` 와 `reject`. 이 `promise`는 다음과 같이 사용될 수 있습니다. 

```javascript
promise.then(function(result) {  
console.log("Promise worked");  
}, function(err) {  
console.log("Something broke");  
});
```

만약 `promise`가 성공했다면, `resolve` 가 실행되고 콘솔에 `Promise worked`를 찍게 됩니다. 그렇지않으면 `Something broke` 가 콘솔에 찍히게 됩니다. `resolve` 와 `reject` 사이의 상태는 응답이 받기 전에는 `pending` state. 간단하게 말해서 promise에 3 가지 상태가 존재합니다.

1.  `pending`: promise 응답이 기다릴때
2.  `resolve`  : promise 리턴이 성공했을때
3.  `reject`: 실패 했을때 


## Promise: Example

promise의 전체 컨셉을 이해하기 위해 다음은 image가 로드 되는 app을 만들어 봅시다. 만약 image가 로드 되었을때 image가 화면에 로드되고 그렇지 않으면 에러가 발생합니다. 

먼저, [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)(XHR) 를 사용해서 `promise` 를 만들어 보자.

```javascript
const loadImage = url => {
	return new Promise(function(resolve, reject) {
		//Open a new XHR
		var request = new XMLHttpRequest();
		request.open('GET', url);
		// When the request loads, check whether it was successful
		request.onload = function() {
			if (request.status === 200) {
				// If successful, resolve the promise
				resolve(request.response);
			} else {
				// Otherwise, reject the promise
				reject(Error('An error occurred while loading image. error code:' + request.statusText));
			}
		};
		request.send();
	});
};
```

이제, 이미지가 성공적으로 로드 되었을때 promise는 XHR로 부터 나온 응답을 가지고 resolve가 됩니다. 계속 진행해서 loadImage 함수를 실행해서 이 promise를 사용해봅시다.

```javascript
const embedImage = url => {
	loadImage(url).then(function(result) {
		const img = new Image();
		var imageURL = window.URL.createObjectURL(result);
		img.src = imageURL;
		document.querySelector('body').appendChild(img);
	},
	function(err) {
		console.log(err);
	});
}
```

나쁘지 않네요 
이제 당신이 진행할 차례입니다. promise를 사용해보세요 ~!