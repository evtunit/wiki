# How-to: call() , apply() and bind() in JavaScript

> 원문 [How-to: call() , apply() and bind() in JavaScript](https://www.codementor.io/niladrisekhardutta/how-to-call-apply-and-bind-in-javascript-8i1jca6jp)

이 글에선 간단한 예제를 통해 자바스크립트의 `call()`, `apply()` 그리고 `bind()` 메서드의 차이점을 논할 것이다. 함수는 자바스크립트에서 Object이기 때문에, 이 3가지 메소드는 함수의 호출을 제어하는데 사용된다. ECMAScript3에는 `call()` 및 `apply()`가 도입되었고, ECMAScript5에서 `bind()`가 추가되었다.

## 사용

`call()`/`apply()`를 사용하여 함수를 즉시 호출할 수 있다. `bind()`는 나중에 실행될 때 원래의 함수를 호출하는 올바른 컨텍스트("**this**")를 갖는 바인딩 함수를 반환한다. 그래서 `bind()`는 함수가 특정 이벤트에서 나중에 호출되어야 할 경우에 유용하게 사용할 수 있다.

자바스크립트의 "**this**"를 이해하려면 [Understanding "This" in JavaScript](https://www.codementor.io/dariogarciamoya/understanding--this--in-javascript-du1084lyn?icn=post-8i1jca6jp&ici=post-du1084lyn) 글을 참조해라.

###   `call()`  or  `Function.prototype.call()`

`call()`에 대한 간단한 예제를 확인해 보자.

```js
//Demo with javascript .call()
var obj = {name:"Niladri"};

var greeting = function(a,b,c){
  return  "welcome "+this.name+" to "+a+" "+b+" in "+c;
};

console.log(greeting.call(obj,"Newtown","KOLKATA","WB"));
// returns output as welcome Niladri to Newtown KOLKATA in WB
```

`call()`메서드의 첫번째 파라미터는 함수가 호출되는 대상인 "**this**" 값을 설정한다. 위의 경우에는 "**obj**"이다.

나머지 매개변수는 실제 함수에 대한 대한 인수들이다.

##  `apply()`  or  `Function.prototype.apply()`

`apply()`에 대한 간단한 예제를 확인해 보자.

```js
//Demo with javascript .apply()
var obj = {name:"Niladri"};

var greeting = function(a,b,c){
  return  "welcome "+this.name+" to "+a+" "+b+" in "+c;
};

// array of arguments to the actual function
var args = ["Newtown","KOLKATA","WB"];
console.log("Output using .apply() below ")
console.log(greeting.apply(obj, args));

/* The output will be Output using .apply() below welcome Niladri to Newtown KOLKATA in WB */
```
d
`call()` 메서드와 마찬가지로 `apply()` 메서드의 첫 번째 매개변수는 함수가 호출되는 대상인 **"this"** 값을 설정한다. 위의 경우엔 **"obj"** 객체이다. `apply()`와 `call()`의 유일한 차이는 두 번째 파라미터이다. `apply()`는 *배열*로 실제 함수에 대한 인수를 받아 들인다.

## `bind()`  or  `Function.prototype.bind()`

`bind()`에 대한 간단한 예제를 확인해 보자.

```js
//Use .bind() javascript
var obj = {name:"Niladri"};

var greeting = function(a,b,c){
  return  "welcome "+this.name+" to "+a+" "+b+" in "+c;
};

//creates a bound function that has same body and parameters
var bound = greeting.bind(obj);

console.dir(bound); ///returns a function

console.log("Output using .bind() below ");

console.log(bound("Newtown","KOLKATA","WB"));
//call the bound function

/* the output will be Output using .bind() below welcome Niladri to Newtown KOLKATA in WB */
```

위의 코드 샘플에서 `bind()`는 나중에 호출 될 컨텍스트가 있는 바운드 함수를 반환하고 있다. 다음과 같이 콘솔에서 바운드 함수를 볼 수 있다.

[[/resource/yongkwan/15/01.png]]

bind() 메서드의 첫 번째 매개변수는 바인딩 함수가 호출될 때 대상 함수의 "**this**" 값을 설정한다. 바인딩 함수가 "**new**" 연산자를 사용하여 구성된 경우 첫 번째 파라미터의 값은 무시된다는 점에 유의하라. 나머지 매개 변수는 대상 함수를 호출 할 때 바인드 된 함수에 제공된 인수 앞에 추가되는 인수로 전달된다.

여기까지이다. 읽어주어서 감사 드리며 이 게시물이 자바스크립트의 `apply()`, ` call ()` 및 `bind ()` 메소드와 관련된 문제를 겪고있는 초보자에게 도움이되기를 바란다.