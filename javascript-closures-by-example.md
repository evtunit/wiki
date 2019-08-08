


# 예제로 살펴보는 자바스크립트 클로저

> 원문: [JavaScript closures by example](https://howchoo.com/g/mge2mji2mtq/javascript-closures-by-example)

클로저를 사용하는 법을 알면 매우 유용하다. 문제는 많은 자바스크립트 개발자가 클로저를 사용하는 법을 모른다는 것이다. 이 가이드를 읽은 후 클로저가 무엇이고 어떻게 작동하는지, 언제 사용하는 지 잘 이해하길 바란다.

## 1. 클로저란 무엇인가?

**MDN**(Mozilla Developer Network)에 나와 있는 클로저의 정의를 인용하자면, "클로저는 독립적인 변수를 참조하는 함수이다. 다시 말해 클로저에 정의 된 함수는 생성된 환경을 '기억'한다." 아래의 예를 보자.

```js
var outerFunc = function() {
    var message = "Hello, World!";
    var innerFunc = function() {
        return message;
    }
    return innerFunc;
}
```

자바스크립트는 다른 언어처럼 블록 레벨 범위 지정(scoping)이 아닌, 함수 레벨 범위 지정을 구현한다. 따라서 위의 예에서 **outerFunc**내에 정의 된 변수 는 해당 함수의 스코프가 로컬이므로 함수 외부에서는 액세스 할 수 없다. 그리고 일반적으로 함수 실행이 끝나면 로컬 변수가 더 이상 존재하지 않게 된다. 그러나 **outerFunc** 함수가 로컬 변수를 참조하는 **innerFunc** 를 반환하기 때문에 위 코드는 흥미롭다. **outerFunc** 가 실행을 마친 후에도 **message** 변수를 사용할 수 있을까?

```js
> var myFunc = outerFunc();
> myFunc();
'Hello, World!'
```

**message** 변수를 사용할 수 있다. 보다시피, **outerFunc**는 **innerFunc**를 반환하고  **myFunc**에 저장한다. **myFunc** 는 클로저가 되어 생성 된 환경을 기억하기 때문에,  **outerFunc** 가 실행될 때 **message** 변수 의 값을 기억한다 . 이런 관계를 모르는 상태에서 클로저를 사용했을 가능성이 있으니 알아두자!

## 2. For loops: 일반적인 실수

for 루프 내에서 함수를 실행할 때 문제가 발생한 경험이 있을 것이다. 이 문제가 나에게 처음 일어 났을 때 나는 때려치우고 싶었다. 아래 예제를 살펴보자.

```js
var names = ['Locke', 'Franklin', 'Smith', 'Mises'];
var logName = function(name) {
    console.log(name);
};
var name;
for (var i=0; i < names.length; i++) {
    name = names[i];
    setTimeout(function(){
        logName(name);
    }, 1000);
}
```

위의 코드에서는 **names** 이라는 배열을 만들어 네 개의 이름 목록을 저장한다. name을 매개 변수로 받고 기록하는 **logName** 함수를 만들었다. 그런 다음 names을 반복하며 **logName**에 name을 전달하고, 해당 함수를  1초 후에 실행하는 for loop를 만들었다 . 이  예제가 정상적으로 동작한다고 생각할 수도 있지만 실제로 "Mises"를 네 번 기록한다. 그 이유는 매우 간단하다. **logName**은 작성된 환경을 기억하는 클로저이다. for 루프에서 **logName** 바로 호출 한 경우엔 모든 name을 기록한다. 그러나 우리는 대신 타임 아웃을 설정하고 클로저(setTimeout에 대한 콜백)를 만들고 있으며, 이 클로저는 환경을 기억하고 있다. 1초가 지난 뒤, **name** 변수가 "Mises"로 설정되었다. 따라서 단순히 "Mises"를 네 번 기록하는 것이다. 이 문제를 어떻게 해결할 수 있을까? 이 문제를 해결하는 한 가지 방법은 다른 클로저를 구현하는 것이다.

```js
var names = ['Locke', 'Franklin', 'Smith', 'Mises'];
var logName = function(name) {
    console.log(name);
};
var makeClosure = function(name) {
    return function() {
        logName(name);
    }
};
for (var i=0; i < names.length; i++) {
    var name = names[i];
    setTimeout(makeClosure(name), 1000);
}
```

그러나 불필요한 클로저를 만드는 것은 좋지 않다. 사실, 원하는 결과를 얻을 훨씬 더 좋은 방법이 있지만 for loop의 클로저 문제와 클로저로 어떻게 해결할 수 있는지 설명하고 싶었다. 위의 코드를 실행하면 1 초 지연 후 4 개의 name이 각각이 기록되어 예상대로 작동하는 것을 볼 수 있다. 우리가 해야 할 일은 **name** 변수가 생성 될 때부터 '기억하는' 클로저를 반환 하는 **makeClosure** 함수를 만드는 것이 었다. 클로저는 즉시 만들어 지므로 각 **name** 의 가치를 기억한다.

## 3. 모듈 패턴

많은 개발자들이 자바스크립트 클로저에 기반한 모듈 패턴을 사용한다. [이 링크](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)에선 모듈 패턴에 대해 자세히 살펴볼 수 있지만, 여기선 기본과 클로저를 어떻게 구현하는지에 대해 설명하고자 한다. 모듈 패턴을 사용하는 목적은 코드를 정리하고, 글로벌 스코프를 깨끗하게 유지하며, 모듈 외부에서 접근해서는 안 되는 private 코드를 유지하는 것이다. 모듈 패턴은 다음과 같다.

```js
var module = (function(){
    var localVar = 1913;
    var localFunc = function() {
        return localVar;
    }
    var otherLocalFunc = function(num) {
        localVar = num;
    }
    return {
        getVar: localFunc,
        setVar: otherLocalFunc
    }
})();
```

위 예제에서 익명 함수 안에 단일 환경을 만들었다. 이 환경에는 로컬 변수 **localVar** 및 로컬 함수 표현식 **localFunc**가 포함 된다. 이 변수 중 어느 것도 익명 함수 외부에서 접근 할 수 없으므로 전역 네임 스페이스를 깨끗하게 유지할 수 있다. 익명 함수 외부에서 접근할 수 있는 방법은 익명 함수에 의해 반환되어야한다. 위 예에서 볼 수 있듯이,  **localFunc**을 값으로 가지는**getVar**라는 key와,  **otherLocalFunc**를 값으로 가지는 **setVar** 라는 key 가진 객체를  반환한다. 이 패턴에 대한 좋은 점은 모듈의 **state**가 애플리케이션 수명 동안 지속된다. 이것은 **localVar**를 외부에서 직접 액세스 할 수 없지만, 반환된 함수는 여전히이 변수에 접근 할 수 있음을 의미한다. 위의 코드를 사용하여 아래 예제를 살펴보라.

```js
console.log(module.getVar());
> 1913
module.setVar(1776);
console.log(module.getVar());
> 1776
```

자바스크립트에서 private 변수 **localVar를** 성공적으로 에뮬레이션하고,  전역 네임 스페이스를 깨끗하게 유지했다 . 여기서 유일한 전역 변수는 **module** 이다. 이 모든 것을 클로저를 사용하여 구현했다. 이 글의 범위에 포함되지는 않지만 이 패턴은 **IIFE** (즉시 호출  함수 표현식)를 사용했다. 익명 함수가  **(function () {...}) ()** 과 같이 괄호로 묶여 있음을 볼 수있다. 괄호가 없으면 이것은 단순히 함수 정의 일 것입니다. 그러나 괄호를 추가하면 (뒤에 나오는 괄호에도 유의) 즉시 호출 함수 표현식이 된다. 이에 대한 자세한 내용은 [여기를](http://benalman.com/news/2010/11/immediately-invoked-function-expression/) 참조 하라.

## 4. 또다른 예제

좀 더 실용적인 예가 있다. 즉석에서 함수를 만들고 싶다고 가정해보자. 함수 팩토리(함수를 만드는 함수)를 만들 수 있다. 함수 팩토리의 결과 함수는 작성된 환경을 기억하는 클로저 이다.

```js
var functionFactory = function(num1) {
    return function(num2) {
        return num1 * num2;
    }
}
```

위의 **functionFactory** 함수에 하나의 숫자를 넘길 수 있다. 그런 다음 **functionFactory**는 원래 전달 된 **num1** 값을 기억하는 클로저를 리턴한다 . 결과 함수는 기존 **num1**에  클로저 호출 시 전달 된 **num2** 값을 곱한다 .

```js
var mult5 = functionFactory(5);
var mult10 = functionFactory(10);
```

위의 간단한 함수는 새로운 함수 **mult5** 및 **mult10**을 만든다 . 이제 5 또는 10을 곱하려는 숫자를 전달하여 이러한 함수 중 하나를 호출 할 수 있다. 이제 결과를 예상 할 수 있다.

```js
> mult5(3)
15
> mult5(5)
25
> mult10(3)
30
> mult10(5)
50
```

이 가이드가 유용하다고 생각되면 아래에 알려주기 바란다. 또한 오류를 발견 하거나 더 독특한 예제를 제공 할 수 있는 경우 의견을 보내주기 바란다.