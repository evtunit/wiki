# Javascript의 고차 함수

> 원문: [Higher Order Functions in JavaScript](https://www.lullabot.com/articles/higher-order-functions-in-javascript)

고차 함수는 처음엔 겁나지만 배우는 건 어렵지 않다. 고차 함수는 단순히 함수를 매개 변수로 받거나 함수를 결과로 반환하는 함수이다. JavaScript에서 가장 유용한 패턴 중 하나이며 *[함수형 프로그래밍](https://bethallchurch.github.io/JavaScript-and-Functional-Programming/)* 에서 특히 중요하다.

먼저 자바스크립트의 편리한 [`Array.map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 메서드를 이용한 예제부터 하겠다. 각각 name 속성을 가진 객체 배열이 있다고 가정 해 보자:

```js
const characters = [
  {name: 'Han Solo'},
  {name: 'Luke SkyWalker'},
  {name: 'Leia Organa'}
];
```

`map` 메서드를 이용하여 각 name들을 콘솔에 찍어 보자. ([실제 예제](https://jsbin.com/fijoxif/1/edit?js,console)).

```js
// ES6 syntax
const names = characters.map(character => character.name);
console.log(names);  // "Han Solo" "Luke SkyWalker" "Leia Organa"

// ES5 syntax
var names2 = characters.map(function(character) { return character.name; });  console.log(names2);  // "Han Solo" "Luke SkyWalker" "Leia Organa"
```

`map` 메서드 (일명 함수)가 다른 함수를 매개 변수로 받아 사용하는 것을 보았는가 ? map 메서드는 고차 함수에 대한 정의에 알맞은 예이다. 고차 함수는 그렇게 어려운 것이 아니다. 실제로, 우리는 map 메서드가 고차 함수라는 것을 깨닫지 못하고 항상 사용했을 수도 있다.

이제 함수형 프로그래밍에서 밀접하게 관련된 다른 개념을 살펴보자. **함수 구성(Function composition)** 은 고차 함수라는 개념을 취하고 매우 강력한 방식으로 작성할 수 있다. 작고 집중된 함수들을 조합하여 복잡한 기능을 만들 수 있다. 이러한 "빌딩 블록"기능을 작게 유지하면 테스트하기가 쉽고 비용을 적게 들게 하며, 재사용 할 수 있다.

이것이 어떻게 보이는지 보려면 다른 예를 살펴 보자. 처음에 코드가 약간 혼란스러워도 걱정하지마라. 잠시 후에 코드를 천천히 살펴볼 것이다. 먼저 [ES6 버전부터](https://jsbin.com/foracux/2/edit?js,console) 시작해 보자.

```js
// ES6 version
const characters =  [
	{name:  'Luke Skywalker', img:  'http://example.com/img/luke.jpg', species:  'human'},
	{name:  'Han Solo', img:  'http://example.com/img/han.jpg', species:  'human'},
	{name:  'Leia Organa', img:  'http://example.com/img/leia.jpg', species:  'human'},
	{name:  'Chewbacca', img:  'http://example.com/img/chewie.jpg', species:  'wookie'}
];

const humans = data => data.filter(character => character.species ===  'human');
const images = data => data.map(character => character.img);
const compose = (func1, func2) => data => func2(func1(data));
const displayCharacterImages = compose(humans, images);

console.log(displayCharacterImages(characters));

/* 
Logs out the following array
	["http://example.com/img/luke.jpg",
	 "http://example.com/img/han.jpg",
	 "http://example.com/img/leia.jpg"
	 ]
*/
```

그리고 이제 같은 코드 의 [ES5 버전](https://jsbin.com/xamayad/5/edit?js,console)을 보자.

```js
// ES5 version
var characters =  [ 
	{name:  'Luke Skywalker', img:  'http://example.com/img/luke.jpg', species:  'human'},
	{name:  'Han Solo', img:  'http://example.com/img/han.jpg', species:  'human'},
	{name:  'Leia Organa', img:  'http://example.com/img/leia.jpg', species:  'human'},
	{name:  'Chewbacca', img:  'http://example.com/img/chewie.jpg', species:  'wookie'}
];
var humans = function(data) {
	return data.filter(function(character) {
		return character.species ===  'human';
	})
}
var images = function(data) {
	return data.map(function(character) {
		return character.img;
	})
}  
function compose(func1, func2) {
	return function(data) {
		return  func2(func1(data));
	};
} 
var displayCharacterImages =  compose(humans, images);  

console.log(displayCharacterImages(characters));
/* 
Logs out the following array
	["http://example.com/img/luke.jpg",
	 "http://example.com/img/han.jpg",
	 "http://example.com/img/leia.jpg"
	 ]
*/
```

자, 이것을 단계별로 살펴보며 어떻게 유용한 지 알아보자. 설명은 가능한 친숙한 ES5 예제를 사용할 것이다.

1. 두 번째 줄에서 객체 배열을 선언한다.(`characters`).  API 호출의 결과로써 이러한 데이터를 수신하고 작업하는 것은 일반 적이다.

2. 그런 다음 우리가 호출할 첫 번째 함수( `humans`)를 선언한다.이 함수에 대해 알아야할 것은 [순수 함수](http://www.nicoespeon.com/en/2015/01/pure-functions-javascript/)라는 것 이다. 즉, 동일한 입력이 주어지면 **항상** 동일한 출력을 반환한다. 함수형 프로그래밍의 핵심 개념이다. 이를 통해 함수를 쉽게 테스트, 리팩토링 할 수 있다.

3. 그런 다음 두 번째 함수(`images`)를 선언한다.  .  `map` 함수 안에 전달 된 콜백 함수를 살펴봐라. 아래에서 설명할 것이다.

```js
function(character) {
	return character.img;
}
```

코드를 더 쉽게 따라할 수 있도록 "character"를 사용했으나 대신 "item"또는 이와 유사한 것 이름으로 사용하면 함수가 코드의 나머지 부분으로 부터 완전히 분리되었는지 확인할 수 있다. `img` 속성 이있는 모든 객체 배열에 사용할 수 있다. 코드 재사용을 할 수 있다는 것이다!

4. 다음으로 두 함수를 매개 변수로 받고 다른 함수를 반환하는 고차 함수인  `compose` 가 있다. 이 함수는 또한 왼쪽에서 오른쪽으로 호출 된 두 함수의 결과를 결합하기 위해 재사용 될 수 있다. 데이터를 전달하고 함수가 필요한 방식으로 변환하면 된다.

5. 우리는 다음 `compose`함수에 `humans` 과 `images` 함수를 전달하여 호출 할 것이고, 변수`displayCharacterImages`에 결과를 할당할 것이다 . 지금  `displayCharacterImages`에 값을 로깅하면,  `compose`에 의해 반환된 내부 함수를 볼 수 있다.

```js
function (data) {
	return func2(func1(data));
}
```

6. 마침내  `displayCharacterImages`를 호출하면 전달된 `characters` 배열은 `func1`(`humans`)에 전달 될 것이다. 이 함수의 배열의 결과 값은 오직 "human" 문자를 포함한 것들 만 반환한다.(츄이 미안!) 그리고 이 결과 값이 데이터 세트에서 human 캐릭터의 이미지 속성을 반환하는 `func2`(`images`)함수로 전달된다. 이 함수를 이용해서 팬 사이트의 일부에 해당 이미지를 표시 해보는 것을 상상해 봐라.

다행스럽게도 고차 함수에 대한 주제를 소개하고 작은 함수를 결합하여 복잡한 동작을 만드는 방법에 대해 했다. 이것을 즉시 이해하지 못해도 걱정하지 말아라. 이것은 함수형 프로그래밍의 일부이지만, 일단 사용할 수 있게 된다면 매우 강력하고 오류가 적은 유연한 코드를 작성할 수 있다.

## 추가 자료

함수형 프로그래밍에 대해 더 배우고 싶다면 몇 가지 좋은 자료가 있다. 첫 번째는 Anjana Vakil의 컨퍼런스 발표 인 [Learning Functional Programming with JavaScript](https://www.youtube.com/watch?v=e-5obm1G_FY)이다. Mattias Petter Johansson (mpj)의 훌륭하고 재미있는 [비디오 튜토리얼 시리즈](https://www.youtube.com/playlist?list=PL0zVEGEvSaeEd9hlmCXrk5yUyqUag-n84)도 있다. 마지막으로 기사 형식을 선호하는 사람들에게는 Raja Rao의 [Functional Programming In JavaScript — With Practical Examples](https://medium.freecodecamp.com/functional-programming-in-js-with-practical-examples-part-1-87c2b0dbc276)과 Peleke Sengstack의 [How to Use Map, Filter, & Reduce in JavaScript](https://code.tutsplus.com/tutorials/how-to-use-map-filter-reduce-in-javascript--cms-26209)이 있다.