# 자바스크립트에서 필수로 알아야 할 것: 즉시실행함수 표현법

자바스크립트의 기능을 잘 이해하고 기능을 이용하여 '아름답게' 코드를 작성하는 방법은 아주아주 중요하다

함수와 함께 자주 사용되는 코드 패턴 중 하나는 화려한 이름을 가지고 있다. 즉시실행함수, 혹은 IIFE로 더 많이 알려져 있고 "iffy"로 발음된다.

IIFE가 무엇이며 왜 필요한지 이해하기 전에, 우리는 JavaScript 기능에 대한 몇 가지 기본 개념을 빨리 검토할 필요가 있다.

<br>

## The natural function definition

자바스크립트에 새롭게 도입된 기능을 다룰 때 다음과 같은 구문에 자연스럽게 익숙해진다.

```js
function sayHi() {
    alert("Hello, World!");
}

sayHi(); // shows "Hello, World!" as alert in browser.
```

1. 1-3번 줄에 'sayHi'라는 이름을 가진 함수가 있다
2. 5번 줄에 'sayHi'라는 함수를 호출하는 부분에서는 '()'를 통해 호출하고 있다는 것을 알 수 있다.

이 함수를 만드는 방법을 "함수 정의"또는 "함수 선언"또는 "함수 문" 이라 불린다. 일반적으로 자바 스크립트를 처음 사용하는 개발자는 다른 유명한 프로그래밍 언어의 함수 / 메소드와 매우 흡사하므로이 구문을 사용하는 데 문제가 없다.

> 이 함수는 항상 function 키워드로 시작하며 그 뒤에 항상 함수의 이름이 온다. 잘못된 구문이므로 이름을 생략 할 수 없다.

<br>

## Function expressions

자바 스크립트에서 더 흥미로운 것들이 많다, 함수 표현식이 어떻게 보이는지 보도록 하자.

```js
var msg = "Hello, World!";
var sayHi = function() {
    alert(msg);
};

sayHi(); // shows "Hello, World!" as alert in browser.
```

이 겉으로 보기에 간단한 예제는 자바 스크립트 기술을 한 차원 높여주는 데 도움이 될 수 있다.

1. 1번줄은 `msg`변수를 선언하고 문자열 값을 할당한다.
2. 2~4번줄은 `sayHi`변수를 선언하고 함수유형으로 값을 할당한다.
3. 6번줄은 `sayHi`함수를 호출한다.

1번줄은 이해하기 쉽다. 자바와 같은 다른 프로그래밍을 했던 다른 개발자들은 2~4번 줄의 코드를 보게 된다면 당황할 수 있다.

> 기본적으로 2-4 줄에서 `sayHi`라는 변수에 함수 유형의 값을 할당했다.
> 위의 예에서 대입 연산자의 오른쪽에있는 함수는 "함수 표현식" 이라고 한다. 이 코드는 자바스크립트의 모든 곳에 있습니다. 작성한 대부분의 콜백은  함수 표현식이다.

아마 여러분들은 이 코드를 완전히 이해하지 않고 함수표현식을 사용했을 것이다. 그러나 이 부분을 마스터하면 몇가지 비밀스러운 자바스크립트의 기술을 얻어낼 수 있을 것이다.

> 여기서 기억해야할 중요한 개념은 자바스크립트에서 함수가 다른 값과 거의 비슷하다는 것이다. 대입 연산자의 오른쪽에 있거나 인수로 다른 함수를 전달할 수 있게 된다.

<br>

## 익명 함수 표현식
너는 이미 이 것이 무엇인지 알고 있다. 위의 예는 익명의 함수 표현이었다. function 키워드 다음에 이름이 없어 익명이다.

<br>

### 이름이 있는 함수 표현식 === 명명된 함수 표현식
함수 표현식은 이름이 가질 수 있다. 이러한 명명된 함수 표현식의 가장 지루하고 보편적인 사용법은 재귀를 이용하는 것이다. 명명된 기능 표현식을 이해하지 않고도 IIFE를 익힐 수 있으므로 지금은 이것에 대해 많이 걱정하지 마십시오.

```js
var fibo = function fibonacci() {
    // you can use "fibonacci()" here as this funciton expression has a name.
};

// fibonacci() here fails, but fibo() works.
```

그래서 여기서의 차이점은 함수표현식이 함수표현식 안에서 재귀로 호출할 수 있는 `fibonacci`라는 이름을 가진 함수가 있다는 것이다.
(스택 트레이스 (stack-traces) 등에서 함수 이름이 나타나기 때문에 더 많은 것이 있지만, 이 튜토리얼에서는 그다지 걱정하지 말자.)

<br>

### 충분하다! IIFE를 보여 주거나 떠나라!
인내심과 인내심을 가져 주셔서 감사 드리며 자바스크립트를 익히는 데 필요한 가장 중요한 기술이다.

이제는 함수 정의와 함수 식을 배웠으므로 이제 IIFE의 비밀 세계로 들어가보십시오. 그들은 몇 가지 문체 변형이 있습니다. 정말 이해하기 쉬운 변형을 먼저 보도록하겠습니다.

이제 함수표현식과 함수의 정의에 대해 배웠으니, IIFE의 비밀세계로 들어가 봅시다. 그것들은 몇 가지 변형이 있다. 먼저 정말 이해하기 쉬운 variation을 봅시다.
```js

!function() {
    alert("Hello from IIFE!");
}();
// Shows the alert "Hello from IIFE!"
```

이 코드를 복사하고 브라우저의 콘솔에서 시도하면 2번 째 줄의 코드에서 alert이 뜬다. alert을 다시 표시 할 수는 없다.

> 한번 즉시실행이 되었으니 다시 실행될 일은 없다.

이 코드는 완전 직관적이지는 않는다. 1번 째 줄을 보면 `!`를 확인할 수 있다.
1. 전에 보았듯, function 문은 항상 function 키워드로 시작한다. 자바스크립트는 function키워드를 유효한 문장의 첫 단어로 볼 때마다 함수 정의가 수행될 것으로 예상한ㄷ. 그래서 이런 일이 발생하지 않도록 1번 라인의 function키워드 앞에 `!` 접두사를 붙인다. 기본적으로 자바스크립트는 `!`다음에 오는 모든 것을 표현식으로 처리하도록 한다.
2. 가장 흥미로운 것은 3번째 줄에서 `()`을 통해 함수 표현식을 즉시 실행하는 것입니다.

> 생성 된 후에 즉시 호출되는 함수 표현식을 갖는다. 그리고 이 효과를 얻기 위해 사용된 양식적 변화와 상관없이, IIFE라고 불린다.

위의 문체 변형은 `!`를 `+`, `-`또는 심지어 `~`로 대체하여 사용할 수 있다. 기본적으로 모든 단항 연산자를 사용할 수 있다.

이제, 콘솔에서 시도해보십시오! IIFE와 함께 즐거운 시간을 보내십시오!

> 첫 번째 문자 인 `!`은 함수 문, 정의 대신 해당 함수를 표현식으로 만드는 것이다. 그런 다음 즉시 function을 실행한다.

```js
void function() {
    alert("Hello from IIFE!");
}();
```

다시 void는 기본적으로 함수가 함수표현식으로 취급되도록 한다. 위의 모든 패턴은 IIFE의 리턴 값에 관심이 없는 경우에 유용한다.

IIFE의 반환 가치를 원하고 그 리턴 값을 다른 곳에서 사용하고 싶다면 어떻게 해야할까?

<br>

## Classical IIFE style
위에서 본 IIFE 패턴은 이해하기 쉽다. 그래서 나는 다른 좀더 전통적이고 널리 사용되는 스타일 대신에 먼저 그 스타일로 시작했다.

> 위의 IIFE 예제에서 보았 듯이 IIFE 패턴의 핵심은 함수를 사용하여 이를 표현식으로 바꾸고 즉시 실행하는 것이다.

먼저 함수 표현식을 만드는 또 다른 방법을 살펴 보자.

```js
(function() {
    alert("I am not an IIFE yet!");
});
```

위의 코드에서 함수 표현식은 1-3번째의 줄에 있는 괄호 안에 싸여 있다. 함수 표현식이 절대로 실행되지 않으므로 아직 IIFE는 아니다. 이제이 코드를 IIFE로 변환하기 위해 두 가지 스타일 변형을 사용해보았다.

```js
// Variation 1
(function() {
    alert("I am an IIFE!");
}());

// Variation 2
(function() {
    alert("I am an IIFE, too!");
})();
```

이제 2 개의 IIFE가 작동한다. Variation 1과 Variation 2의 차이를 알아내는 것은 정말 힘들 수도 있다. 설명하자면,

1. Variation 1의 4 행에서 함수 표현식 호출을 위한 괄호`()`는 바깥 괄호 안에 포함된다. 다시 바깥 괄호는 그 함수 밖의 함수 표현식을 만드는 데 필요하다.
2. Variation 의 9 행에서 함수 표현식을 호출하기위한 괄호`()` 는 함수 표현식의 줄 바꿈 밖에 있다.

두 Variation 모두 널리 사용된다. 나는 개인적으로 Variation 1을 선호하는 편이다. 핵심 부분에 들어가면 두 가지 Variation가 어떻게 작동하는지 약간 다르다. 그러나 실질적인 목적을 위해 이 긴 이미 튜토리얼을 짧게 유지하면서, 여러분이 좋아하는 것은 둘 중 하나를 사용할 수 있다고 말할 것이다.

익명함수를 사용하는 것은 결코 좋은 생각이 아니므로 지금부터 IIFE의 이름을 짓도록 하겠다.

```js
// Valid IIFE
(function initGameIIFE() {
    // 게임을 초기화하는 모든 MAGIC 코드!
}());

// 두가지 IIFE 예제
function nonWorkingIIFE() {
     // 괄호가 필요한 이유를 알았다!
     // 이러한 괄호가 없으면 표현식이 아니라 함수 정의이다.
     // 구문 오류가 발생!
}();

function () {
    // 구문 에러가 발생!
}();
```

이제 IIFE 패턴을 형성하기 위해 함수 표현식을 둘러싼 괄호가 필요한 이유를 알았다.

> 기억하라! IIFE를 형성하려면 함수 표현식이 필요합니다. 함수 문장, 정의는 IIFE를 작성하는 데 사용되지 않는다.

<br>

## IIFEs and private variables
IIFE가 실제로 잘하는 한 가지는 IIFE의 기능 범위를 만드는 능력과 관련이 있다.

IIFE 내부에서 선언 된 변수는 외부에서 볼 수 없다.

예제를 보자.

```js
(function IIFE_initGame() {
    // Private variables that no one has access to outside this IIFE
    var lives;
    var weapons;
    
    init();

    // Private function that no one has access to outside this IIFE
    function init() {
        lives = 5;
        weapons = 10;
    }
}());
```

위의 예제는 IIFE 내부에 두개의 변수를 선언했고 IIFE에서만 사용할 수 있도록 선언을 했다. IIFE 외부에 해당 변수를 접근할 수 없다.

마찬가지로 IIFE외부에 아무도 접근할 수 없는 함수 `init`함수도 있다. 하지만, init 함수는 private 변수에 액세스 할 수 있다.

> 코드 외부에서 사용하지 않도록 IIFE에 해당 변수와 함수를 생성한다. 코드는 계속 작동하지만 이제 전역 범위를 오염시키지 않는다. 또한 실수로 또는 실수로 전역을 변경할 수있는 사람으로부터 코드를 보호한다.

<br>

## IIFEs with a return value
IIFE에서 리턴 값을 필요로하지 않는다면, 단항 연산자 인 `!`, `+`, `void` 등으로 보았던 IIFE 변형을 항상 사용할 수 있다.
그러나 IIFE의 강력한 특징은 변수에 할당 할 수있는 값을 리턴할 수 있다는 것입니다.

```js
var result = (function() {
    return "From IIFE";
}());

alert(result); // alerts "From IIFE"
```

1. variation에서 2번쨰 줄에 return 문이있는 IIFE가 있다.
2. 위의 코드를 실행하면 5번째 줄은 IIFE의 리턴값과 함께 alert을 표시한다.

기본적으로 IIFE는 즉시 실행되고 그 return 값은 `return`변수에 할당된다. 이것은 모듈 패턴의 예를 살펴볼 때 사용하게 될 패턴이다.

<br>

## IIFEs with parameters
IIFE는 값을 리턴 할 수 있을 뿐만 아니라 IIFE도 호출되는 동안 인수를 사용할 수 있다. 간단한 예를 보자.

```js
(function IIFE(msg, times) {
    for (var i = 1; i <= times; i++) {
        console.log(msg);
    }
}("Hello!", 5));
```

1. 위의 예에서 1번째 줄에서 IIFE는 각각 `msg`, `times`라는 두 개의 매개 변수를 갖는다.
2. 5번째 줄에서 IIFE를 실행할 때, 지금까지 보아온 빈 괄호`()` 대신에 IIFE에 인수를 전달한다.
3. 2-3번째 줄은 IIFE 내부의 파라미터를 사용한다.

jQuery 코드와 다른 라이브러리에서도이를 자주 볼 수 있는 유용한 패턴이다.

```js
(function($, global, document) {
    // use $ for jQuery, global for window
}(jQuery, window, document));
```

위의 예제에서 우리는 3번째 줄의 IIFE에 인수로 **jQuery**, **window** 및 **documen**t를 전달합니다. IIFE 내부의 코드는 **$**, **global**, **document**로 각각 참조 할 수 있다.

IIFE에 전달하는 몇 가지 이점이 있다.

1. JavaScript는 항상 현재 함수의 범위에서 범위 조회를 수행하고 식별자를 찾을 때까지 상위 범위에서 계속 검색한다. 3번째 줄로 document를 전달할 때 document의 로컬 scopes를 벗어나는 scopes 조회를 수행하는 유일한 경우이다. 문서화 할 IIFE의 모든 참조 사항은 IIFE의 지역 범위를 넘어 조회 할 필요가 없다. jQuery에도 똑같이 적용된다. 이것에 의한 성능 향상은 IIFE 코드가 얼마나 사소하고 복잡한 지에 따라 거대한 것은 아니지만 여전히 유용한 유용한 트릭이다.

2. 또한 JavaScript minifier는 함수에서 선언 된 매개 변수 이름을 안전하게 축소 할 수 있다. 이러한 매개 변수를 매개 변수로 전달하지 않으면 minifier가이 함수의 범위를 벗어나는 문서 또는 jQuery에 대한 직접 참조를 축소하지 않는다.

<br>

## Classical JavaScript module pattern
이제 IIFE를 마스터 했으므로 IIFE와 클로저를 스테로이드에 적용하는 모듈 패턴의 예를 살펴 보자.

우리는 실수로 현재 시퀀스 값을 손상시키지 않으면서 원활하게 작동하는 고전적인 Sequence 싱글 톤 객체를 구현할 것이다.

점진적으로 어떤 일이 벌어지고 있는지 이해할 수 있도록이 코드를 두 단계로 작성한다.

```js
var Sequence = (function sequenceIIFE() {
    
    // 현재 카운터 값을 저장할 private 변수입니다.
    var current = 0;
    
    // IIFE에서 반환 된 객체입니다.
    return {
    };
    
}());

alert(typeof Sequence); // alerts "object"
```

1. 위의 예에서는 객체를 반환하는 IIFE가 있있다. (7~8번째 줄)
2. 우리는 또한 current라는 IIFE에 지역 변수를 가지고 있다.
3. 이 예제의 객체 인 IIFE의 반환 값은 Sequence 변수에 할당된다. 12번째 줄은 IIFE에서 객체를 반환하기 때문에 "object"에 알맞게 alert으로 뿌려준다

이제 우리가 반환하는 객체에 몇 가지 함수를 추가해서 성능을 향상시킨다.

```js
var Sequence = (function sequenceIIFE() {
    
    // Private variable to store current counter value.
    var current = 0;
    
    // Object that's returned from the IIFE.
    return {
        getCurrentValue: function() {
            return current;
        },
        
        getNextValue: function() {
            current = current + 1;
            return current;
        }
    };
    
}());

console.log(Sequence.getNextValue()); // 1
console.log(Sequence.getNextValue()); // 2
console.log(Sequence.getCurrentValue()); // 2
```

1. 이 예제에서는 IIFE에서 리턴하는 객체에 두 개의 함수를 추가한다.
2. 8-10번째 줄은 `current` 변수의 값을 반환하는 getCurrentValue 함수를 추가한다.
3. 12 ~ 15번째 줄은 현재 값을 1 씩 증가시킨 다음 `current` 값을 리턴하는 getNextValue 함수를 추가한다.

`current` 변수는 IIFE에 전용이므로, 클로저를 통해 액세스 할 수있는 함수 외에는 `current` 변수를 수정하거나 액세스 할 수 없다.

이제는 정말 강력한 JavaScript 패턴을 배웠다. IIFE와 클로저를 응용했기 때문이다!

이것은 모듈 패턴에 대한 매우 기본적인 변형이다. 더 많은 패턴이 있지만 거의 모든 패턴이 IIFE를 사용하여 클로저를 만든다.

<br>

## 괄호를 생략 할 수있는 경우
함수 표현식 주위의 괄호는 기본적으로 함수가 명령문 대신 표현식이 되도록 한다.

그러나 자바스크립트 엔진이 함수 표현식이라는 것이 확실 할 때 기술적으로 다음과 같이 주변 괄호가 필요하지는 않는다.

```js
var result = function() {
    return "From IIFE!";
}();
```

위의 예에서 function 키워드는 명령문의 첫 단어가 아니기 때문에 자바스크립트는 이것을 함수 문, 정의로 취급하지 않는다. 마찬가지로 표현식이라는 것을 알 때 괄호를 생략 할 수있는 다른 장소가 있다.

그러나 항상이 경우에도 괄호를 사용하는 것을 선호한다. 괄호를 사용하면 기능이 IIFE가 될 것이라고 추후 다른 개발자가 처음부터 간략히 암시함으로써 가독성이 향상되기 때문이다. 방금 전에 읽은 것이 IIFE 였음을 깨닫기 위해 함수의 마지막 줄로 스크롤 할 필요가 없으니까!

<br>

# 느낀점
- 생각보다 IIFE를 응용할 수 있는 방법이 많은 것 같았다. 이 부분을 정말 제대로 활용해서 실무에서 사용해보도록 해봐야겠다.
- 하지만 장점만 나열했지 단점을 소개하지 않은 것 같아 조금 아쉬웠다.