# Javascript의 Async/Await 함수 탐구

> 원문: [Exploring Async/Await Functions in JavaScript](https://alligator.io/js/async-functions/)

[프라미스](https://alligator.io/js/promises-es6/)는 비동기적 코드를 순차적으로 쉽게 처리하는 방법을 제공한다. 우리의 뇌가 비동기성을 효율적으로 처리하도록 설계되지 않았다는 것을 고려하면, 매우 환영받을 만한 추가사항이다. **Async / await 함수**는 ES2017  `ES8` ) 의 새로운 추가 기능이다. 내부적으론 비동기 작업을 수행하면서, 표면적으론 완전히 동기식 코드를 작성할 수 있도록 도와준다.

`async` 함수를 사용하여 얻은 기능은 `프라미스`와 [`제너레이터`](https://alligator.io/js/generator-functions-es6/)를 결합하여도 구현할 수 있지만 `async` 함수는 추가 상용구 코드없이 필요한 것을 제공한다.

## 간단한 예제

아래 예제에서는, 먼저 2  초 후에 resolve로 🤡 값을 넘기는 프라미스를 반환하는 함수를 선언한다.  그런 다음 `async` 함수를 선언한다.
`async`함수는 메시지를 콘솔에 로깅하기 전에 프라미스가 reosolve 되기까지 기다리기 위해 `await` 를 사용한다.

```js
function scaryClown() {
	return  new  Promise(resolve => {
		setTimeout(() => {
			resolve('🤡');
		}, 2000);
	});
} 
async  function msg() { 
	const msg = await scaryClown();
	console.log('Message:', msg); 
}
msg(); // Message: 🤡 <-- after 2 seconds
```

> **await**는 프라미스가 resolve되거나 reject 될 때까지 기다리는 데 사용되는 새로운 연산자이다. async 함수 내에서만 사용할 수 있다.

여러 단계가 관련된 경우 async 함수의 힘이 더 분명해진다.

```js
function who() {
	return  new  Promise(resolve => {
		setTimeout(() => {
			resolve('🤡');
		}, 200);
	});
}

function what() {
	return  new  Promise(resolve => {
		setTimeout(() => {
			resolve('lurks');
		}, 300);
	});
}

function where() {
	return  new  Promise(resolve => {
		setTimeout(() => {
			resolve('in the shadows');
		}, 500);
	});
}

async  function msg() {
	const a = await who();
	const b = await what();
	const c = await where(); 
	console.log(`${ a } ${ b } ${ c }`);
} 

msg(); // 🤡 lurks in the shadows <-- after 1 second
```

주의 할 점은, 위의 예에서 각 단계는 순차적으로 수행되며 각 단계가 resolve,  reject 될때까지 기다린다. 단계를 동시에 수행하려면 `Promise.all` 을 사용하여 모든 프라미스가 이행 될 때까지 기다리면 된다.

```js
// ...

async  function msg() {
	// array destructuring
	const [a, b, c] = await Promise.all([who(), what(), where()]); 
	console.log(`${ a } ${ b } ${ c }`);
}

msg(); // 🤡 lurks in the shadows <-- after 500ms
```

`Promise.all` 은 전달 된 모든 프라미스가 resolve 되면 resolve 된 값이 있는 배열을 반환한다.

또한 위의 예제에선 코드를 간결하게 만들기 위해 [array destructuring](https://alligator.io/js/object-array-destructuring-es2015/) 를 사용했다.

## 프라미스 반환

Async 함수는 항상 프라미스를 반환하기에, 아래 코드는 원하는 결과를 얻지 못한다.

```js
async function hello() {
	return  'Hello Alligator!';
}
const b = hello();
console.log(b); // [object Promise] { ... }
```

반환되는 값은 프라미스이기 때문에, 대신 다음과 같이 할 수 있다.

```js
async function hello() {
	return  'Hello Alligator!';
}

const b = hello();

b.then(x =>  console.log(x)); // Hello Alligator!
```

단순히 변경하면 아래와 같다.

```js
async function hello() {
	return  'Hello Alligator!';
}

hello().then(x =>  console.log(x)); // Hello Alligator!
```

## 다른 모양

지금까지 예제로 async 함수를 function 선언으로만 했지만, 화살표 함수로도 정의할 수 있다.

## 함수 표현식으로 만든 async

함수 표현식으로 정의된 첫번째 예제를 보자.

```js
const msg = async function() {
	const msg = await scaryClown();
	console.log('Message:', msg);
}
```

## 화살표 함수로 만든 async

여기 똑같은 예제가 또 나오지만 이번엔 화살표 함수로 정의했다.

```js
const msg = async () => {
	const msg = await scaryClown();
	console.log('Message:', msg);
}
```

## 에러 처리

async 함수의 좋은 점은  **try ... catch** 문을 사용하여 오류 처리가 완전히 동기적으로 수행된다는 것이다 . 절반을 reject하는 프라미스를 사용하여 알아보자.

```js
function yayOrNay() {
	return new Promise((resolve, reject) => {
		const val = Math.round(Math.random() * 1); // 0 or 1, at random
		val ? resolve('Lucky!!') : reject('Nope 😠');
	});
} 
async function msg() {
	 try {
		 const msg = await yayOrNay();
		 console.log(msg);
	} catch(err) { 
		console.log(err);
	}
}
msg(); // Lucky!! 
msg(); // Lucky!! 
msg(); // Lucky!! 
msg(); // Nope 😠 
msg(); // Lucky!! 
msg(); // Nope 😠 
msg(); // Nope 😠 
msg(); // Nope 😠 
msg(); // Nope 😠 
msg(); // Lucky!!
```

async 함수가 항상 프라미스를 반환하기에  `catch`를 사용하여  처리되지 않은 오류도 처리할 수 있다.

```js
async function msg() {
	const msg = await yayOrNay();
	console.log(msg);
}

msg().catch(x =>  console.log(x));
```

이 오류 처리는 프라미스가 reject 될 때뿐만 아니라 실제 런타임 또는 구문 오류가 발생하는 경우에도 작동한다. 아래 예제에서 `msg` 함수를 두번째 호출할때 프로토타입 체인의 `toUpperCase` 메서드가 없는`number` 값을 넘긴다. **try… catch** 블록은 그 에러를 잡아 낸다 :

```js
function caserUpper(val) {
	return new Promise((resolve, reject) => {
		resolve(val.toUpperCase());
	});
} 
async function msg(x) {
	try {
		const msg = await caserUpper(x);
		console.log(msg);
	} catch(err) {
		console.log('Ohh no:', err.message);
	}
} 
msg('Hello'); // HELLO
msg(34); // Ohh no: val.toUpperCase is not a function
```

## 프라미스 기반 API를 통한 Async 함수

[Fetch API](https://alligator.io/js/fetch-api#fetch--asyncawait) 입문서에서 보았 듯이, 프라미스 기반의 웹 API는 async 함수와 잘 어울린다.

```js
async function fetchUsers(endpoint) {
	const res = await fetch(endpoint);
	let data = await res.json();
	
	data = data.map(user => user.username);
	
	console.log(data);
} 

fetchUsers('https://jsonplaceholder.typicode.com/users'); // ["Bret", "Antonette", "Samantha", "Karianne", "Kamren", "Leopoldo_Corkery", "Elwyn.Skiles", "Maxime_Nienow", "Delphine", "Moriah.Stanton"]
```