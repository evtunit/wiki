
# Understanding JavaScript Closures: A Practical Approach

번역 : [https://scotch.io/tutorials/understanding-javascript-closures-a-practical-approach](https://scotch.io/tutorials/understanding-javascript-closures-a-practical-approach)

## What is a JavaScript closure?

**A JavaScript Closure**는  외부 함수 스코프 밖에서 실행 했을 때에도 내부 함수가 외부 함수의 멤버를 접근 할 수 있는 경우 입니다. [lexical scope](http://whatis.techtarget.com/definition/lexical-scoping-static-scoping)

따라서 우리는 기능과 범위를 제외하고 클로저에 대해 이야기 할 여유가 없습니다.

## Scope in JavaScript


스코프는는 프로그램에 정의 된 변수의 가시성 범위를 나타냅니다. JavaScript에서 스코프를 작성하는 방법은 try-catch 블록, 함수, 중괄호가 있는 let 키워드입니다. 전역 범위와 로컬 범위의 두 가지 범위가 있습니다.

```javascript
var initialBalance = 0 // Global Scope

function deposit (amount) {
  /**
   * Local Scope
   * Code here has access to anything declared in the global scope
   */
  var newBalance = parseInt(initialBalance) + parseInt(amount)
  return newBalance
}
```

자바스크립트에서 각 함수는 자신만의 local 스코프를 선언할때 만들어집니다.

이는 함수의 로컬 범위 내에서 선언 된 것은 외부에서 액세스 할 수 없다는것을 의미합니다. 아래 코드를 참조하세요

```javascript
var initialBalance = 300 // Variable declared in the Global Scope

function withdraw (amount) {
  var balance // Variable declared in function scope

  balance = parseInt(initialBalance) - parseInt(amount)
  return balance
}
console.log(initialBalance) // Will output initialBalance value as it is declared in the global scope
console.log(balance) // ReferenceError: Can't find variable: balance
```

## Lexical Scope

자바스크립트의 Lexical Scope 는 컴파일 단계에서 결정됩니다.
변수의 스코프를 설정하여 변수가 정의 된 코드 블록 내에서만 호출/참조 될 수 있도록합니다.

주변 함수 블록 안에 선언 된 함수는 주변 함수의 어휘 범위에있는 변수에 액세스 할 수 있습니다.

```javascript
var initialBalance = 300 // Global Scope

function withdraw (amount) {
  /**
   * Local Scope
   * Code here has access to anything declared in the global scope
   */
  var balance = parseInt(initialBalance) - parseInt(amount)

  const actualBalance = (function () {
    const TRANSACTIONCOST = 35
    return balance - TRANSACTIONCOST /**
     * Accesses balance variable from the lexical scope
     */
  })() // Immediately Invoked Function expression. IIFE

  // console.log(TRANSACTIONCOST) // ReferenceError: Can't find variable: TRANSACTIONCOST
  return actualBalance
}

```

함수 외부에서 내부 함수를 호출하고  둘러싸인 함수의 변수들을 접근 가능하게 유지가 되면 JavaScript 클로저가 생성됩니다.

```javascript
function person () {
  var name = 'Paul'  // Local variable

  var actions = {
    speak: function () {
    //  new function scope
      console.log('My name is ', name) /**
      * Accessing the name variable from the outer function scope (lexical scope)
      */
    }
  } // actions object with a function

  return actions /**
  * We return the actions object
  * We then can invoke the speak function outside this scope
  */
}

person().speak() // Inner function invoked outside its lexical Scope
```

클로저를 사용하면 외부 인터페이스에서 실행 컨텍스트를 숨기고 유지하면서 공용 인터페이스를 노출 할 수 있습니다.

일부 JavaScript 디자인 패턴은 클로저를 사용합니다.

## Module Pattern

이러한 잘 구현 된 패턴 중 하나는 모듈 패턴이며,이 패턴을 사용하면 개인, 공용 및 권한있는 멤버를 에뮬레이션 할 수 있습니다.

```javascript
var Module = (function () {
  var foo = 'foo' // Private Property

  function addToFoo (bam) { // Private Method
    foo = bam
    return foo
  }

  var publicInterface = {
    bar: function () { // Public Method
      return 'bar'
    },
    bam: function () { // Public Method
      return addToFoo('bam') // Invoking the private method
    }
  }

  return publicInterface // Object will contain public methods
})()

Module.bar() // bar
Module.bam() // bam
```

위의 모듈 패턴 코드에서 클로저의 실행 컨텍스트 외부에서 반환 객체의 공용 메서드 및 속성만 사용할 수 있습니다.

실행 컨텍스트가 유지되지만 외부 범위에서 숨겨져 있으므로 모든 private 멤버들은 계속 존재합니다.

## More illustrations on Closures

함수를 setTimeout 또는 모든 종류의 콜백에 전달할 때 함수는 클로저로 인해 어휘 범위를 여전히 기억합니다.

```javascript
function foo () {
  var bar = 'bar'
  setTimeout(function () {
    console.log(bar)
  }, 1000)
}

foo() // bar
```

클로저와 루프일때

```javascript
for (var i = 1; i <= 5; i++) {
  (function (i) {
    setTimeout(function () {
      console.log(i)
    }, i * 1000)
  })(i)
}
/**
* Prints 1 thorugh 5 after each second
* Closure enables us to remember the variable i
* An IIFE to pass in a new value of the variable i for each iteration
* IIFE (Immediately Invoked Function expression)
*/
```

```javascript
for (let i = 1; i <= 5; i++) {
  (function (i) {
    setTimeout(function () {
      console.log(i)
    }, i * 1000)
  })(i)
}
/**
* Prints 1 through 5 after each second
* Closure enabling us to remember the variable i
* The let keyword rebinds the value of i for each iteration
*/
```

이제 클로저를 이해하고 다음을 수행 할 수 있습니다.

- 사용 사례를 보여 주거나 사용하지 않은 상황에서 식별
- 원하는대로 실행 컨텍스트 유지
- JavaScript 모듈 패턴으로 코드 구현
- 명확한 이해와 함께 코드에서 클로저 사용

다음 번까지, 행복한 코딩.