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

<br>

![](https://user-images.githubusercontent.com/21126403/40363067-016bfa96-5dec-11e8-9e32-2a848991af39.png)

<br>

그래서 프로토타입 체인 전체를 만들 수 있다. 여기서 호출을 하게 되면

<br>

```js
console.log(x.toString());
// > function() {
// >
// >  }
```

<br>

여기서 `x`는 `toString()`을 사용할 필요가 없지만 위로 올라가보면 prototype chain에 있다.

<br> <br>

## Prototypes in Depth

Javascipt는 Java와 같은 `Class defination`을 가지고 있지 않는다. 여기서 `Base class`에서 서브 클래스를 만드는 방법을 소개한다.

<br>

```js
//Baseclass
var Bookshop = function() {
  this.cost = 100;
}

Bookshop.prototype.bill = function() {
  console.log('Your Bill is: $'+ this.cost);
}
```

<br>

`Bookshop`은 Base Class로 `bill`이 prototype으로 속해있다.

<br>

```js
//Subclass
 var BuyBook = function(title,cost) {
  Bookshop.call(this); // Statement 1
  
  this.title = title;
  if(cost) 
    this.cost = cost ;
 }

BuyBook.prototype = Object.create(Bookshop.prototype); // Statement 2
BuyBook.prototype.constructor = BuyBook; // Statement 3
```

<br>

`Bookshop`은 Base Class로 `BuyBook`은 그의 Sub Class로 속해있다.

<br> <br>

**Explaination**

1. `Bookshop.call(this)`

이 라인은 생성자를 call할 때 vlaue값으로 `this`를 넘겨주었다. 그래서 `BuyBook : this = { this.cost = 100; }가 된다.

2. `BuyBook.prototype = Object.create(Bookshop.prototype);`

이 라인은 `Bookshop.prototype`의 초기값을 셋팅해준다.

3. `BuyBook.prototype.constructor = BuyBook;`

이 라인은 `BuyBook` 생성자 함수를 셋팅해준다. (BuyBook은 메소드이다)

<br>

```js
 var person_1 = new BuyBook('GOT', 200);
 var person_2 = new BuyBook('Harry Potter');

 person_1.bill();
 //> Your Bill is: $200
 person_2.bill();
 //> Your Bill is: $100
```

<br>

이렇게 하면 상속을 수행할 수 있다. `BuyBook.protype`에서 새 메서드를 정의하여 `bill()` 메서드를 덮어쓸 수도 있다. 예를들면 `BuyBook.prototype.bill = function() { }` 이렇게!

<br>

<End /> 😄