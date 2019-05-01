# 자바스크립트의 스코프에 대한 이해

원문 : https://scotch.io/tutorials/understanding-scope-in-javascript

## 소개
자바스크립트는 Scope라고 하는 특징이 있습니다. 많은 새로운 개발자들이 스코프에 대한 개념을 쉽게 이해하고 있지 않고 있습니다. 스코프에 대해 심플하게 설명을 하려고 합니다. scope를 이해하면 오류를 줄이고 강력한 디자인 패턴을 만들 수 있습니다.

## Scope가 무엇인가?
Scope는 코드가 런타임 중에 코드의 특정 부분에 있는 변수, 함수, 객체에 대한 접근성입니다. 즉, scope는 코드 영역에서 변수 및 다른 리소스의 유효범위(=>[visibility](http://100.daum.net/encyclopedia/view/156XX52200065):가시성을 유효범위로 번역)을 결정합니다. 

## 왜 Scope? 최소 접근의 원리(The Principle of Least Access)
그래서, 변수의 유효범위를 제한하고 코드의 어느 곳에서 사용할 수 있는 것을 모든 것을 가지지 않는다는 것이 무슨 의미일까요?
한가지 장점은 scope가 코드에 일정 수준의 보안을 제공한다는 것입니다. 컴퓨터 보안의 한가지 공통된 원칙은 사용자가 한 번에 필요한 대상(stuff:물근을 대상으로 변경)에만 접근을 해야 한다는 것입니다. 

컴퓨터 관리자들을 생각해봅시다. 컴퓨터 관리자들은 수 많은 컴퓨터 시스템을 제어하고 있기 때문에 그들에게 완전하게 액세스 사용자 계정을 부여하는 것은 괜찮은 것처럼 보일 수 있습니다. 관리자가 세명인 회사가 있다고 가정해봅니다. 모든 시스템에 완전히 접근할 수 있고 모든 것이 순조롭게 작동하고 있습니다. 그러나 갑자기 시스템 중 하나가 악성 바이러스에 감염되는 나쁜 일이 발생합니다. 이제 그게 누구 실수 였는지 모르시나요? 기본 사용자 계정을 입력해야하고, 필요한 경우에는 전체 액세스 권한만 부여 해야 합니다. 이것은 변화를 추적하고 누가 무엇을 했는지에 대한 설명을 할 수 있게 하는데 도움이 됩니다. 이 것이 "최소 접근의 원리"라고 불립니다. 직관적으로 보이시나요? 이 원리는 프로그래밍 언어 디자인에도 적용이 죕니다. 프로그래밍 언어 디자인은 다음에 학습할 Javascript을 포함한 대부분의 프로그래밍 언어에서 scope라고 합니다.

프로그래밍을 계속할 때 코드의 scope을 지정하면 효율성을 높이고, 버그를 추적하는 시간을 줄일 수 있다는 사실을 깨닫게 될 것입니다. scope는 또한 이름이 같지만 scope가 다른 변수를 가지고 있을 때 네이밍 문제를 해결합니다. scope와 context를 혼돈하지마세요. scope와  context 둘다 다른 특징을 가지고 있습니다.

## 자바스크립트에서 scope 
자바스크립트에서 두가지 타입의 scope가 존재합니다. 
- Global Scope
- Local Scope

함수 내부에서 정의된 변수는는 local scope이고 함수 외부에서 정의된 변수는 global scope입니다. 함수가 호출될 때 각 함수는 새 scope을 만듭니다.

## Global Scope
document에 javascript을 쓰기 시작할 때, 이미 전역 범위에 있습니다. Javascript document에는 하나의 global scope만 있습니다. 변수가 함수 외부에서 정의된 경우 Global Scope에 있습니다.

```javascript
// the scope is by default global
var name = 'Hammad';
```
Global scope 내의 변수는 다른 scope에서 접근하고 변경할 수 있습니다.
```javascript
var name = 'Hammad';

console.log(name); // logs 'Hammad'

function logName() {
    console.log(name); // 'name' is accessible here and everywhere else
}

logName(); // logs 'Hammad'
```

## Local Scope
함수 내부에서 정의된 변수가 local scope에 있습니다. 그리고 그 변수들은 모든 호출에 대해 다른 scope을 가지고 있습니다. 즉, 동일한 이름을 가진 변수를 다른 함수에서 사용할 수 있습니다. 이 것은 변수가 각 함수에 바인딩되고 각 함수가 서로 다른 범위이며 다른 함수에서는 접근할 수 없기 때문입니다. 

```javascript
// Global Scope
function someFunction() {
    // Local Scope #1
    function someOtherFunction() {
        // Local Scope #2
    }
}

// Global Scope
function anotherFunction() {
    // Local Scope #3
}
// Global Scope
```

## Block 문
함수와 달리 ```if```, ```switch``` 와 같은 조건문 또는 ```for```, ```while```와 같은 반복문은 새로운 scope을 만들지 않습니다. Block 문 내부에 정의된 변수는 이미 포함된 scope에 남아 있습니다.
```javascript
if (true) {
    // this 'if' conditional block doesn't create a new scope
    var name = 'Hammad'; // name is still in the global scope
}

console.log(name); // logs 'Hammad'
```
ECMAScript 6에서 ```let``` 와 ```const``` 키워드가 소개 되었다. ```let``` 와 ```const```키워드는 ```var``` 키워드 대신 사용할 수 있습니다.

```javascript
var name = 'Hammad';

let likes = 'Coding';
const skills = 'Javascript and PHP';
```
```var``` 키워드와는 달리 ```let``` 와 ```const``` 키워드는 Block 문 내부의 로컬 범위 선언을 지원합니다.

```javascript
if (true) {
    // this 'if' conditional block doesn't create a scope

    // name is in the global scope because of the 'var' keyword
    var name = 'Hammad';
    // likes is in the local scope because of the 'let' keyword
    let likes = 'Coding';
    // skills is in the local scope because of the 'const' keyword
    const skills = 'JavaScript and PHP';
}

console.log(name); // logs 'Hammad'
console.log(likes); // Uncaught ReferenceError: likes is not defined
console.log(skills); // Uncaught ReferenceError: skills is not defined
```

```
Global scope는 애플리케이션이 실행되는 동안 Global scope는 계속 유지됩니다. Local Scope는 함수가 호출되고 실행이 되는 한 계속 유지 됩니다.
```

## Context
많은 개발자들이 종종 동일한 개념으로 언급하여 자주 scope와 context을 혼돈합니다. scope는 위에서 논의한 것이고 context는 코드의 특정 부분에서 ```this```값을 말합니다. scope는 변수의 유효범위를 말하며 context는 같은 scope에서 ```this```의 값을 말합니다.
우리는 또한 함수의 메소드를 사용하여 context을 변경할 수 있으며 나중에 논의할 것 입니다. global scope는 항상 window 객체 입니다.

```javascript
// logs: Window {speechSynthesis: SpeechSynthesis, caches: CacheStorage, localStorage: Storage…}
console.log(this);

function logFunction() {
    console.log(this);
}
// logs: Window {speechSynthesis: SpeechSynthesis, caches: CacheStorage, localStorage: Storage…}
// because logFunction() is not a property of an object
logFunction(); 
```

scope가 객체의 메소드에 있으면, context가 메소드의 일부인 객체가 됩니다. 

```javascript
class User {
    logName() {
        console.log(this);
    }
}

(new User).logName(); // logs User {}
```

```
(new User).logName() 변수에 객체를 저장한 다음 logName함수를 호출하는 짧은 방법 입니다. 여기서 새 변수를 만들 필요가 없습니다. 
```
한 가지는 강조 하고 싶은 것은 ```new```키워드를 사용하여 함수를 호출하면, context의 값이 다르게 작용한다는 것입니다. 그런 다음 context가 호출된 함수의 인스턴스로 설정됩니다. 위 의 예중 하나가  ```new``` 키워드로 함수가 호출이 되었다는 것입니다.

```javascript
function logFunction() {
    console.log(this);
}

new logFunction(); // logs logFunction {}
```

```
strict mode에서 함수가 호출이 되었을 때 context의 기본값은 undefined입니다.
```

## 실행 Context
모든 혼돈과 학습한 것을 제거하기 위해 실행 context의 context라는 단어는 context가 아닌 scope을 나타냅니다. 이 네이밍 규칙은 이상하지만 Javascript의 스펙으로 인해 이렇게 정의할 수 밖에 없습니다. (일부 의역...)

JavaScript는 단일 스레드 언어이므로 한 번에 하나의 작업만 할 수 있습니다. 나머지 작업들은 실행 context의 queue에 있습니다. 앞에서도 말했듯이, 자바스크립트 해석기가 당신의 코드를 실행하기 시작하면, context(scope)코드를 실행하기 시작하면, context(scope)은 기본적으로 글로벌로 설정이 됩니다. 

이 글로벌 context는 실행 context를 시작하는 첫번째 context인 실행 context에 추가 됩니다. 

이 후 각 함수 호출(invocation)은 실행 context에 context을 추가 합니다. 다른 함수가 그 함수 내부 또는 다른 어딘가에서 호출될 때 같은 일이 발생합니다.

```
각각의 함수는 자체적으로 실행 context을 만듭니다.
```

브라우저가 해당 context에서 코드 실행이 완료 되면 해당 context가 실행 context에서 벗어나고, 실행 context의 현재 context가 부모의 context로 전송이 됩니다. 브라우저는 항상 실행 스택의 맨 위에 이는 실행 context를 실행합니다. (실제로 코드의 가장 안쪽 범위 입니다.)

```
하나의 전역 context가 있을 수 있지만 여러가지의 함수 context가 있습니다.
```

실행 context에는 생성 및 코드 실행의 두 단계가 있습니다. 

## 생성 단계
생성 단계인 첫번째 단계는 함수가 호출 되지만 코드가 아직 실행이 되지 않을 때 존재합니다. 생성 단계에서 일어나는 세가지 주요한 일은 다음과 같습니다.

- Variable (Activation) Object (Variable Object : 변수 객체)의 생성
- Scope Chain의 생성
- context의 값(```this```) 설정

### 변수 객체
활성 객체라고도 하는 변수 객체는 실행 context에서 특정 branch에 정의된 모든 변수, 함수 및 기타 선언이 포함됩니다. 함수가 호출되면 해석기는 함수 인수, 변수 및 기타 선언을 포함한 모든 리소스들을 스캔합니다. 

```javascript
'variableObject': {
    // contains function arguments, inner variable and function declarations
}
```

### Scope Chain
실행 context의 생성 단계에서 scope chain은 변수 객체 다음에 작성 됩니다. scope chain에는 변수 객체가 포함됩니다. scope chain은 변수를 resolve하는데 사용합니다. 변수를 resolve하라는 요청을 받으면 Javascript는 항상 코드의 가장 안쪽에서 시작하여 변수 또는 다른 찾고 있는 다른 리소스들을 찾을 때까지 부모 범위로 다시 이동합니다. scope chain은 단순히 자체 실행 context의 변수 객체와 부모의 다른 모든 실행 context를 포함하는 객체로 정의할 수 있습니다. 객체는 다른 객체가 여러 개 있습니다. 

```javascript
'scopeChain': {
    // contains its own variable object and other variable objects of the parent execution contexts
}
```

### 실행 context 객체
```javascript
executionContextObject = {
    'scopeChain': {}, // contains its own variableObject and other variableObject of the parent execution contexts
    'variableObject': {}, // contains function arguments, inner variable and function declarations
    'this': valueOfThis
}
```

### 코드 실행 단계
실행 context의 두번째 단계인 코드 실행 단계에서 다른 값이 할당되고 코드가 마침내 실행됩니다. 

## lexical Scope
lexical Scope는 중첩된 함수 그룹에서 내부 함수가 부모 범위의 변수 및 기타 리소스에 접근할 수 있음을 의미합니다. 즉, 자식 함수가 부모의 실행 context에 lexically하게 묶여 있음을 의미합니다. Lexical scope는 때로는 정적 scope라고도 합니다.

```javascript
function grandfather() {
    var name = 'Hammad';
    // likes is not accessible here
    function parent() {
        // name is accessible here
        // likes is not accessible here
        function child() {
            // Innermost level of the scope chain
            // name is also accessible here
            var likes = 'Coding';
        }
    }
}
```
lexical scope에 대해 강조할 수 있는 것은 앞으로 나아 가는 것인데, 이는 자식들의 실행 context의 ```name```에 접근할 수 있다는 의미합니다. 하지만 부모에게 되돌아 갈 수 없기 때문에 부모가 원하는 변수에 접근할 수 없다는 것도 의미합니다. (일부 의역)

이것은 또한 다른 실행 context에서 동일한 이름을 갖는 변수가 실행 스택의 위에서 아래로 우선순위를 얻는 다는 것을 알려 줍니다. 가장 안쪽 함수(실행 스택의 최상위 context)에서 다른 변수와 비슷한 이름을 갖는 변수는 더 높은 우선 순위를 갖습니다.

## 클로저
클로저의 개념은 위에서 학습한 lexical Scope와 밀접하게 관련이 있습니다. 내부 함수가 immediate lexical scope의 외부 변수를 의미하는 외부 함수의 scope chain에 접근을 시도하려 할 때, 클로저가 생성됩니다. 클로저는 자신의 scope chain 및 global scope가 포함됩니다. 

```
클로저는 외부 함수에 정의된 변수 뿐만 아니라 외부 함수의 인수에도 접근할 수 있습니다.
```

클로저는 함수가 반환된 후에도 외부 함수의 변수에 접근 할 수 있습니다. 이렇게 반환된 함수가 외부 함수의 모든 리소스에 대한 접근이 가능하게 됩니다.

함수에서 내부 함수를 반환하면, 외부 함수를 호출할 때 반환된 함수가 호출되지 않습니다. 먼저 외부 함수 호출을 별도의 변수에 저장한 다음 변수를 함수로 호출해야 합니다. 

```javascript
function greet() {
    name = 'Hammad';
    return function () {
        console.log('Hi ' + name);
    }
}

greet(); // nothing happens, no errors

// the returned function from greet() gets saved in greetLetter
greetLetter = greet();

 // calling greetLetter calls the returned function from the greet() function
greetLetter(); // logs 'Hi Hammad'
```

여기서 주목해야할 핵심은 ```that greetLetter```이 반환된 후에도 greet의 name변수에 접근할 수 있다는 것입니다. 변수 할당 없이 ```greet``` 함수에서 반환된 함수를 호출하는 한 가지 방법은 괄호를 사용하여 ```()```을 ```()()```처럼 두번 쓰는 것입니다. 

```javascript
function greet() {
    name = 'Hammad';
    return function () {
        console.log('Hi ' + name);
    }
}

greet()(); // logs 'Hi Hammad'
```

## Public 그리고 Private Scope
많은 프로그래밍 언어에서 public scope, private scope, protected scope을 사용하여 속성 및 클래스 메소드의 유효범위를 설정할 수 있습니다. 
PHP language의 예

```php
// Public Scope
public $property;
public function method() {
  // ...
}

// Private Sccpe
private $property;
private function method() {
  // ...
}

// Protected Scope
protected $property;
protected function method() {
  // ...
}
```
public(global) scope에서 함수를 캡슐화하면 취약한 공격으로 부터 보호 됩니다. 그러나 자바스크립트에서는 public scope, private scope이 없습니다. 그러나 우리는 클로저의 특징을 사용해서 구현할 수 있습니다. 모든 것을 global과 분리 시키기 위해 먼저 다음과 같은 함수 내에서 우리의 기능을 캡술화해야 합니다. 

```javascript
(function () {
  // private scope
})();
```

함수의 끝 부분에 있는 괄호는 해석기가 호출하지 않고 읽으면 즉시 실행하도록 지시를 내립니다. 함수와 변수를 추가할 수 있으며 외부에서 액세스할 수 없습니다. 하지만 우리가 외부에서 접근하고 싶다면, 그 중 일부는 public 일부는 private로 해야할까요? 우리가 사용할 수 있는 한가지 유형의 클로저를 모듈 패턴이라고 하며 이 패턴은 객체의 public scope 그리고 private scope을 사용하여 함수의 scope을 지정할 수 있게 합니다. 

## 모듈 패턴
모듈 패턴은 이와 같다. 
```javascript
var Module = (function() {
    function privateMethod() {
        // do something
    }

    return {
        publicMethod: function() {
            // can call privateMethod();
        }
    };
})();
```
모듈의 반환문에는 public 함수가 포함되어 있습니다. private 함수는 반환되지 않는 함수들이 있습니다. 반환 함수가 없으면 모듈 네임스페이스 외부에서 접근할 수 없습니다. 하지만 우리의 public 함수들은 helper 함수, ajax 호출 및 기타 등등 들이 private 함수에 접근할 수 있도록 돕는다. 

```javascript
Module.publicMethod(); // works
Module.privateMethod(); // Uncaught ReferenceError: privateMethod is not defined
```

하나의 규칙은 underscore와 함께 private 함수를 시작하고, public 함수이 포함된 익명의 객체를 반환하는 것입니다. 이렇게 하면 객체를 쉽게 관리 할 수 있습니다.

```javascript
var Module = (function () {
    function _privateMethod() {
        // do something
    }
    function publicMethod() {
        // do something
    }
    return {
        publicMethod: publicMethod,
    }
})();
```

## 즉시 실행 함수 표현 (IIFE)
클로저의 다른 타입인 즉시 실행 함수 표현(IIFE)이 있습니다. 이것은 window context에서 호출되는 self-invoked anonymous 함수로 ```this```의 값이 ```window```으로 설정되어 있음을 의미합니다. 이것은 하나의 global 인터페이스를 노출시켜 상호 작용을 합니다. 

```javascript
(function(window) {
    // do anything
})(this);
```

## Changing Context - .call(), .apply() and .bind()
Call, Apply 함수는 함수를 호출하는 동안 context를 변경하는데 사용됩니다. 이 것은 놀라운 프로그래밍 기능을 제공합니다.(그리고 세계를 지배하는 궁극적인 힘..) call 또는 apply함수를 사용하려면 쌍의 괄호를 사용하여 함수를 호출하고 첫번째 인수로 context를 전달하는 대신 함수에서 호출하면 됩니다. 함수의 자체 인수는 context 이후에 전달될 수 있습니다.

```javascript
function hello() {
    // do something...
}

hello(); // the way you usually call it
hello.call(context); // here you can pass the context(value of this) as the first argument
hello.apply(context); // here you can pass the context(value of this) as the first argument
```
```.call()``` 그리고 ```.apply()```의 차이점은 call은 나머지 쉼표로 구분하여 인수의 목록을 전달하고, apply는 배열로 인수를 전달하는 것입니다.

```javascript
function introduce(name, interest) {
    console.log('Hi! I\'m '+ name +' and I like '+ interest +'.');
    console.log('The value of this is '+ this +'.')
}

introduce('Hammad', 'Coding'); // the way you usually call it
introduce.call(window, 'Batman', 'to save Gotham'); // pass the arguments one by one after the contextt
introduce.apply('Hi', ['Bruce Wayne', 'businesses']); // pass the arguments in an array after the context

// Output:
// Hi! I'm Hammad and I like Coding.
// The value of this is [object Window].
// Hi! I'm Batman and I like to save Gotham.
// The value of this is [object Window].
// Hi! I'm Bruce Wayne and I like businesses.
// The value of this is Hi.
```

```
call은 apply보다 성능이 빠릅니다.
```
다음 예제는 문서의 항목의 목록들을 가져와 하나씩 콘솔에 찍습니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Things to learn</title>
</head>
<body>
    <h1>Things to Learn to Rule the World</h1>
    <ul>
        <li>Learn PHP</li>
        <li>Learn Laravel</li>
        <li>Learn JavaScript</li>
        <li>Learn VueJS</li>
        <li>Learn CLI</li>
        <li>Learn Git</li>
        <li>Learn Astral Projection</li>
    </ul>
    <script>
        // Saves a NodeList of all list items on the page in listItems
        var listItems = document.querySelectorAll('ul li');
        // Loops through each of the Node in the listItems NodeList and logs its content
        for (var i = 0; i < listItems.length; i++) {
          (function () {
            console.log(this.innerHTML);
          }).call(listItems[i]);
        }

        // Output logs:
        // Learn PHP
        // Learn Laravel
        // Learn JavaScript
        // Learn VueJS
        // Learn CLI
        // Learn Git
        // Learn Astral Projection
    </script>
</body>
</html>
```
HTML에는 정렬되지 않은 list의 item만 포함됩니다. 그 다음 Javascript는 DOM에서 모든 것을 선택합니다. list는 list의 item 끝까지 반복합니다. loop 내부에서 list의 item의 내용을 콘솔에 찍습니다. 

이 log문은 괄호 안에 싸여 있으며, 그 위에는 ```call``` 함수가 호출 됩니다. 해당 list의 item이 call 함수 호출로 전달되어 콘솔문에 있는 ```this```(```the```을 ```this```로 변경) 키워드에 해당하는 객체의 innerHTML값을  기록합니다. (일부 의역)

객체에는 메소드가 있을 수 있으며, 마찬가지로 객체인 함수도 메소드를 가질 수 있습니다. 사실, Javascript 함수에는 다음과 같은 네가지 내장 메소드가 있습니다.

- Function.prototype.apply()
- Function.prototype.bind() (Introduced in ECMAScript 5 (ES5))
- Function.prototype.call()
- Function.prototype.toString()

```
Function.prototype.toString() returns a string representation of the source code of the function.
```
지금 까지 우리는 ```.call()```, ```.apply()```, 그리고 ```toString()```에 대해 설명을 하였습니다. (toString을 ...설명 했었던가...) Call과 Apply와 달리 Bind는 자체적으로 함수를 호출하지 않으며 함수를 호출하기 전에 컨텍스트 및 기타 인수의 값을 바인딩하는 데만 사용할 수 있습니다. 위의 예 중 하나에서 Bind을 사용합니다.

```javascript
(function introduce(name, interest) {
    console.log('Hi! I\'m '+ name +' and I like '+ interest +'.');
    console.log('The value of this is '+ this +'.')
}).bind(window, 'Hammad', 'Cosmology')();

// logs:
// Hi! I'm Hammad and I like Cosmology.
// The value of this is [object Window].
```
Bind는 ```call```함수와 같으며 나머지 인수를 쉼표로 구분하여 적용하지 않고 배열로 인수를 전달 할 수 있습니다.

## 결론
이러한 개넘은 Javascript에 급진적이며 고급 주제에 접근하는데 있어서 중요 합니다. 자바스크립트의 scope와 scope와 관련된 것들을 더 잘 이해해 주셨으면 좋겠습니다. 