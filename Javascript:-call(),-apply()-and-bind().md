# Javascript: call(), apply() and bind()

번역 : [https://medium.com/@omergoldberg/javascript-call-apply-and-bind-e5c27301f7bb](https://medium.com/@omergoldberg/javascript-call-apply-and-bind-e5c27301f7bb)

# **“this” refresher**

객체 지향 JS에서는 JS에서 모든 것이 하나의 객체라는 것을 알게되었습니다. 모든 것이 객체이기 때문에 함수에 대한 추가 속성을 설정하고 액세스 할 수 있다는 것을 알게되었습니다.

프로퍼티를 함수에 설정하고 프로토 타입을 통해 추가 메서드를 작성하는 것은 정말 대단합니다 ... **하지만 어떻게 액세스 할 수 있습니까?! ??! **

우리는`this` 키워드에 대해 소개되었습니다. 우리는 모든 함수가 이 속성을 자동으로 가져 오는 것을 배웠습니다. 그래서 지금 시점에서 우리가 함수 실행 컨텍스트의 정신 모델을 생성한다면 (나는 이것을하는 유일한 사람이 아닙니다! ... 맞나요?!?!), 다음과 같이 보일 것입니다 :

![https://miro.medium.com/max/700/1*oGDRHlH5QWXTFTenWvMaBw.png](https://miro.medium.com/max/700/1*oGDRHlH5QWXTFTenWvMaBw.png)

`this` 키워드를 사용하는 데는 다소 시간이 걸렸지 만, 일단 우리가 유용하게 사용되면 얼마나 유용한 지 깨닫기 시작했습니다. `this`는 함수 내에서 사용되며, 항상 단일 객체를 참조합니다
[ "this"가 사용되는 함수를 호출 (호출)하는 객체] (http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/).

그러나 인생은 완벽하지 않습니다. 때때로, 우리는`this` 참조를 잃어 버린다. 이런 일이 생기면 혼란스러운 핵을 사용하여 'this'에 대한 참조를 저장합니다. 이 혼란스러운 해킹을 확인하십시오. [localStorage 연습 문제 :] (https://github.com/Arieg419/ITCCodingBootcamp/blob/master/localStorage/eBay.js)
![https://miro.medium.com/max/700/1*aE3Ao2PIEo21WK7C6Ofdfg.png](https://miro.medium.com/max/700/1*aE3Ao2PIEo21WK7C6Ofdfg.png)

31번째 라인

그렇다면 왜이 '참조'를 저장해야합니까? _deleteBtn.addEventListener안 이기 때문에, 'this`는 _deleteBtn_ 객체를 참조합니다. 이것은 불행한 일입니다. 더 나은 솔루션이 있습니까?

# call(), apply() and bind() — a new hope

지금까지 함수를 이름 (선택 사항, 익명의 함수 일 수도 있음)과 호출 될 때 실행되는 코드로 구성되는 객체로 처리했습니다. 그러나 그것은 전체 진실이 아닙니다. 진실한 사랑하는 사람으로서, 나는 실제로 함수가 다음 이미지에 더 가깝게 보인다는 것을 알려야합니다.

![https://miro.medium.com/max/700/1*TkzF3ckhM9Xf_U9XFaCyhA.png](https://miro.medium.com/max/700/1*TkzF3ckhM9Xf_U9XFaCyhA.png)

이것은 무엇입니까 ??????? 걱정마! 이제 예제를 통해 모든 함수에 나타나는 3 가지 유사한 방법을 살펴 보겠습니다. 

# **bind()**

[공식 문서는] (https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)`** bind () **`메소드는 다음과 같은 새로운 함수를 생성합니다. , 호출 될 때`this` 키워드가 제공된 값으로 설정됩니다. (그것은 실제로 더 많은 것들에 대해 이야기하지만, 우리는 그것을 다른 시간 동안 남겨 둘 것입니다 :))

이것은 매우 강력합니다. 함수를 호출 할 때`this`의 값을 명시 적으로 정의 해줍니다. cooooode를 보자.

```javascript
var pokemon = {
	firstname: 'Pika',
	lastname: 'Chu ',
	getPokeName: function() {
		var fullname = this.firstname + ' ' + this.lastname;
		return fullname;
	}
};

var pokemonName = function() {
	console.log(this.getPokeName() + 'I choose you!');
};

var logPokemon = pokemonName.bind(pokemon); // creates new object and binds pokemon. 'this' of pokemon === pokemon now

logPokemon(); // 'Pika Chu I choose you!'
```
Using the  `bind() method on line 14.`

**_파해쳐 보자._**   `bind()`  method를 사용할 때:

1. JS 엔진은 새로운`pokemonName` 인스턴스를 만들고`this` 변수로`pokemon`을 바인딩합니다. ** pokemonName 함수를 복사한다는 것을 이해하는 것이 중요합니다. **

2. `pokemonName` 함수의 사본을 생성 한 후에 그것은 처음에는 포켓몬 객체에 있지 않았지만`logPokemon ()`을 호출 할 수 있습니다. 이제 해당 속성 (_Pika_ 및 _Chu) 및 해당 메서드를 인식합니다 .

그리고 멋진 점은 값을 bind () 한 후에 다른 일반적인 함수처럼 함수를 사용할 수 있다는 것입니다. 매개 변수를 받아들이도록 함수를 업데이트하고 다음과 같이 전달할 수도 있습니다.

```javascript
var pokemon = {
    firstname: 'Pika',
    lastname: 'Chu ',
    getPokeName: function() {
        var fullname = this.firstname + ' ' + this.lastname;
        return fullname;
    }
};

var pokemonName = function(snack, hobby) {
    console.log(this.getPokeName() + 'I choose you!');
    console.log(this.getPokeName() + ' loves ' + snack + ' and ' + hobby);
};

var logPokemon = pokemonName.bind(pokemon); // creates new object and binds pokemon. 'this' of pokemon === pokemon now

logPokemon('sushi', 'algorithms'); // Pika Chu  loves sushi and algorithms
```
# **call(), apply()**


[call ()의 공식 문서] (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) :`** call () **` 메소드는 주어진`this` 값과 인자가 개별적으로 제공되는 함수를 호출합니다.

이것이 의미하는 것은, 우리는 어떤 함수라도 호출 할 수 있고, 이것을 호출 함수 내에서 참조해야하는 것을 명시 적으로 지정하십시오. 정말 `bind ()`메소드와 비슷합니다! 이것은 우리가 해커 코드를 작성하는 것을 확실히 막을 수 있습니다 (비록 우리 모두 여전히 hackerzzz 임에도 불구하고).

`bind ()`와`call ()`의 주요 차이점은 `call ()` 메서드입니다 :

1. 추가 매개 변수도 허용합니다.
2. 즉시 호출 된 기능을 실행합니다.
3. `call ()`메서드는 호출되고있는 함수의 복사본을 만들지 않습니다.

** call ()은 모든 매개 변수가 개별적으로 전달되는 것을 기대하는 반면, apply ()는 개별적으로 전달되는 것을 기대합니다. ** call () 모든 매개 변수의 배열을 기대합니다. 예:

```javascript
  
var pokemon = {
    firstname: 'Pika',
    lastname: 'Chu ',
    getPokeName: function() {
        var fullname = this.firstname + ' ' + this.lastname;
        return fullname;
    }
};

var pokemonName = function(snack, hobby) {
    console.log(this.getPokeName() + ' loves ' + snack + ' and ' + hobby);
};

pokemonName.call(pokemon,'sushi', 'algorithms'); // Pika Chu  loves sushi and algorithms
pokemonName.apply(pokemon,['sushi', 'algorithms']); // Pika Chu  loves sushi and algorithms
```


모든 JS 함수에 존재하는 이러한 내장 메소드는 매우 유용 할 수 있습니다. 하루 종일 프로그래밍에서 사용하지 않더라도 다른 사람 코드를 읽을 때 자주 실행됩니다.

궁금한 점이 있으시면 언제든지 [Instagram] (https://www.instagram.com/omeragoldberg/)을 통해 문의하십시오. ❤