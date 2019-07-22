# Basic Inheritance with Object.create

번역 : [http://adripofjavascript.com/blog/drips/basic-inheritance-with-object-create.html](http://adripofjavascript.com/blog/drips/basic-inheritance-with-object-create.html)

_[JavaScript 뉴스 레터] (http://adipofjavascript.com/default.htm)에 처음으로 게시되었습니다 ._

몇 가지 문제는 우리가 [생성자를 사용하여 기본 상속을 구현하는 방법] (http://adipofjavascript.com/blog/drips/basic-inheritance-with-javascript-constructors.html)에서 살펴 보았습니다. 이번 호에서는 새로운 `Object.create` 를 사용하여 동일한 작업을 수행하는 방법을 살펴 보겠습니다.

상속을 위해 생성자를 사용할 때, 우리는 생성자의`prototype` 속성에 다음과 같이 속성을 첨부합니다 :

여기 약간의 재충전이 있습니다.

```javascript
function SuperHuman (name, superPower) {
    this.name = name;
    this.superPower = superPower;
}

SuperHuman.prototype.usePower = function () {
    console.log(this.superPower + "!");
};

var banshee = new SuperHuman("Silver Banshee", "sonic wail");

// Outputs: "sonic wail!"
banshee.usePower();

```


`SuperHuman` 생성자는 초기화 로직을 포함하고,`SuperHuman.prototype`은 모든`SuperHuman` 인스턴스들에 걸쳐 공유되는 메소드들을 포함합니다.

`Object.create`를 사용하여 같은 기본 로직을 구현한다면, 약간 다르게 보일 것입니다 

```javascript
var superHuman = {
    usePower: function () {
        console.log(this.superPower + "!");
    }
};

var banshee = Object.create(superHuman, {
    name: { value: "Silver Banshee" },
    superPower: { value: "sonic wail" }
});

// Outputs: "sonic wail!"
banshee.usePower();

```

이 경우 먼저 프로토 타입 객체  `superHuman` 을 정의한 다음 `Object.create` 를 사용하여 `superHuman` 을 상속받은 새로운 객체를 만듭니다. 두 번째 인수는 약간 이상해 보일지 모르지만 간단하게 속성 설명자 객체 일뿐입니다. [Object.defineProperty`를 사용하여 객체의 속성을 미세 조정합니다.] (http://adripofjavascript.com/blog/drips /creating-unwritable-properties-with-object-defineproperty/default.htm).

자, 우리 자신의 기능을 추가하는 동안 `superHuman` 으로부터 상속받은 새로운 타입을 만들고 싶다면 어떻게해야할까요? 그게 어떻게 생겼어?

```javascript
var  superHero  =  Object.create(superHuman, {
	allegiance: { value:  'Good' },
	saveTheDay: {
		value:  function() {
		console.log(this.name  +  ' saved the day!')
		},
	},
})

var  marvel  =  Object.create(superHero, {
	name: { value:  'Captain Marvel' },
	superPower: { value:  'magic' },
})
// Outputs: "Captain Marvel saved the day!"

marvel.saveTheDay()
```


여태까지는 그런대로 잘됐다. 그러나 캡틴 마블 (Captain Marvel)은`슈퍼 인간`프로토 타입 방법에 접근 할 수 있습니까?

```javascript
// Outputs: "magic!"
marvel.usePower();
```

네, 그렇습니다!

`Object.create`를 사용하면 객체를 프로토 타입으로 사용할 수 있기 때문에 상속 체인을 간단하게 설정할 수 있습니다. 그러나`Object.create`에 의해 관리되는 상속은`instanceof`에 의해 감지 될 수 없습니다. 대신에 다음과 같이`isPrototypeOf` 메서드를 사용할 필요가 있습니다 :

```javascript
// Outputs: true
console.log(superHero.isPrototypeOf(marvel));

// Outputs: true
console.log(superHuman.isPrototypeOf(marvel));

```


`superHero`와`superHuman` 모두`marvel`의 프로토 타입 체인의 일부이기 때문에`isPrototypeOf` 호출은 각각 true를 리턴합니다.

우리가 검토 한 다른 JavaScript 기능과 마찬가지로`Object.create`는 ECMAScript 5의 기능이며 IE8과 같은 구형 브라우저에서는 사용할 수 없습니다.

이것이`Object.create` 사용에 대한 간단한 소개입니다. 읽어 주셔서 감사합니다!

조슈아 클랜튼
