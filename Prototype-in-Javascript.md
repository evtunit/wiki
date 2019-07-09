![](https://process.filestackapi.com/cache=expiry:max/resize=width:1050/4JH9hR06Q2Sm1Ftf7kfa)

<br>

기본적으로 모든 함수는 **EMPTY**(기본값)인 프로토타입으로 속성 이름을 가지고 있다. 거기에 특성과 방법을 추가할 수 있다.

<br>

> Note: 모든 함수는 생성자 함수이다.

<br>

함수 `x`가 있고 `x1`이 `x`의 prototype을 이어받으므로 `x`의 개체를 만든다고 하자.

<br>

```js
var x = function(j) {
  this.i = 0;
  this.j = j;
  
  this.GetJ =  function() {
  	return this.j;
  }
}

var x1 = new x(2);

console.log(x1); 
  // > x {i: 0, j: 2, GetJ: ƒ}
 
console.log(x1.GetJ());
  // > 2
```

<br>

> Note: 생성자 함수에 관한 `this`는 생성자 함수가 포함된 함수를 지칭하는 것이 아니라 생성자 함수에 의해 생성될 객체를 가리킨다.

<br>

이제 원하는 객체로 만들 수 있으며 `x`에서 생성된 모든 객체에는 `GetJ()` 메서드가 부착되어 있으며, `GetJ()` 메서드는 불량 상태이다. 이에 대한 해결책은 프로토타입이다. `GetJ()` 방법을 `x`의 프로토타입으로 가져간다.

<br>

```js
var x = function(j) {
  this.i = 0;
  this.j = j;
}

x.prototype.GetJ =  function() {
  	return this.j;
 }

var x1 = new x(2);

console.log(x1); 
  // > x {i: 0, j: 2}
 
console.log(x1.GetJ());
  // > 2
```

<br>

하지만 `x1`에는 `GetJ()`이 없다는 것이 유일한 차이점 이전과 같은 방식으로 작동하므로 여전히 정확한 출력을 얻을 수 있다. 따라서 프로토타입 체인을 찾아보고(부모 부모까지 올라가서!) `GetJ()`을 찾는다.

<br>

> Note: **Prototype Chain** Javascript의 모든 기능은 마스터 객체에서 생성된다.

<br>

여기서 주목할 점은 `x -> function -> object`
따라서 `x`에서 어떤 방법을 호출하면, 그 perticular함수에 `x`가 있지 않으면, 나중에 찾을 때까지 위아래로 표시된다.
함수에 prototype chain이 존재하지 않는 경우, `undefined` 상태로 표시됩니다.

예를 들면,

<br>

```js
var x = function() {
}

console.dir(x);
```