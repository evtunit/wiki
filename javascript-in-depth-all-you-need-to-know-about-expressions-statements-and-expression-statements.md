# Javascript의 Expressions, Statements 및 Expression Statements에 대해 알아야 할 모든 것

> 원문: [ All you need to know about Javascript's Expressions, Statements and Expression Statements]([https://dev.to/promhize/javascript-in-depth-all-you-need-to-know-about-expressions-statements-and-expression-statements-5k2](https://dev.to/promhize/javascript-in-depth-all-you-need-to-know-about-expressions-statements-and-expression-statements-5k2))

이 포스팅을 다보고 나면  아래  코드가 작동하는 방식과 작동 원리에 대해 자세히 설명 할 수 있어야한다.

```js
{} + 1 // 1
{2} + 2 // 2
{2+2} + 3 // 3
{2+2} - 3 // -3
```

Javascript에는 크게  두 가지 문법 카테고리가 있다.

1.  Statements (구문)
2.  Expressions (표현식)

표현식은 구문처럼 동작 할 수 있기에 구별 하는 것은 중요하다. 표현식 구문도 존재한다. 반면에 구문은 표현식 처럼 동작할 수 없다.

## EXPRESSIONS (표현식)

### **표현식은 단일 값을 생성한다**

표현식은 단일 값이 되는 자바스크립트 코드 스니펫이다. 표현식은 원하는 만큼 길 수 있지만, 항상 단일 값이된다.

```js
2 + 2 * 3 / 2

(Math.random() * (100 - 20)) + 20

functionCall()

window.history ? useHistory() : noHistoryFallback()

1+1, 2+2, 3+3

declaredVariable

true && functionCall()

true && declaredVariable
```

위의 모든 표현식은 단일 값을 반환한다. 따라서 아래의 `console.log` 인자는 단일 값을 반환하게 되고 화면에 출력된다.

```js
console.log(true && 2 * 9) // 18
```

### **표현식이 반드시 상태를 변경하지는 않는다**

예를 들어,

```js
const assignedVariable = 2; //this is a statement, assignedVariable is state

assignedVariable + 4 // expression

assignedVariable * 10 // expression

assignedVariable - 10 // expression

console.log(assignedVariable) // 2
```

위의  예제에 있는 모든 표현식에도 불구하고 `assignedVariable`의 값은 여전히 ​​2이다.  표현식이 무조건 상태(변수)를 변경하는 것이 아닌 것이다.

하지만 함수는 다르다. 함수 호출은 표현식이지만, 함수는 상태를 변경하는 구문을 포함 할 수 있다. 

```js
foo () => {
  // ...
}
```

위의 예제에선 `foo()`함수는 원하는 내용을 채운다면 값을 반환하거나 변수 값을 변경할 수 있다. 그러나 아래와 같이 작성된다면 어떨까?

```js
const foo = foo () => {
  assignedVariable = 14
}
```

여전히 `foo()` 함수는 표현식이지만, 호출을 한다면 상태 변경이 가능하다. 즉 표현식과 구문의 경계가 모호하다. `foo()` 함수를 더 나은 방식으로 바꾸어 보자.

```js
const foo = foo () => {
  return 14 //explicit return for readability
}
assignedVariable = foo()
```

추가로 더 나은 방법은 아래와 같다.

```js
const foo = foo (n) => {
  return n//explicit return for readability
}
assignedVariable = foo(14)
```

이렇게하면 코드가 더 읽기 쉽고 호환되며, 표현식과 구문 사이에 명확한 구별과 분리가 있다. 이것은 기능적이고 선언적인 자바스크립트의 기본이다.

## STATEMENTS (구문)

구문은 함수형 프로그래밍의 골칫거리이다. 기본적으로 구문은 행동을 수행하는 역할을 가진다.

자바스크립트에선 값이 기대되는 곳에서는 구문을 사용할 수 없다. 함수의 인수, 할당의 오른쪽(right-side), 피연산자, 반환 값 등으로 사용할 수 없다.

```js
foo(if () {return 2}) //js engine mind = blown
```

자바스크립트에서의 구문은 아래의 형태로 존재한다.

1.  if
2.  if-else
3.  while
4.  do-while
5.  for
6.  switch
7.  for-in
8.  with (deprecated)
9.  debugger
10.  variable declaration

브라우저의 콘솔에 아래의 스니펫을 입력하고 엔터를 눌러보자.

```js
if (true) {9+9}
```

이 스니펫이 `18`을 반환하는 결과를 볼 것이다. 그럼에도 불구하고 표현식으로 사용할 수 없으며, 값을 기대하는 곳에서 사용할 수 없다.  구문이 아무것도 반환을 하지 않는다고 예상하기 때문에 이상하다. 반환 값은 사용할 수 없다면 거의 쓸모가 없다. 이상하다.

### 함수 선언, 함수 표현식 및 이름이 있는 함수 표현식 

함수 선언은 구문이다.

```js
function foo (func) {
  return func.name
}
```

함수 표현식은 표현식이다. 아래는 익명 함수라고 불리는 함수다.

```js
console.log(foo(function () {} )) // ""
```

이름이 있는 함수 표현식은 익명 함수와 같은 표현식이지만 이름이 잇다는 차이 점이 있다.

```js
console.log(foo(function myName () {} )) // "myName"
```

표현식으로서의 함수와 선언문으로서의 함수 사이의 구별을 할 줄 알면 아래의 내용도 이해할 수 있을 것이다.

**자바스크립트가 값을 기대하는 함수를 선언할 때마다 값을 값으로 사용할 수 없다면, 오류가 발생하게 된다.
블록 구문 (즉, 값을 기대하지 않는 경우)의 스크립트, 모듈 또는 최상위 레벨의 글로벌 수준에서 함수를 선언하면 함수 선언이 발생한다.**

예를 들어

```js
if () {
  function foo () {} // top level of block, declaration
}

function foo () {} //global level, declaration

function foo () {
  function bar() {} //top level of block, declaration
}

function foo () {
  return function bar () {} // named function expression
}

foo(function () {}) // anonymous function expression

function foo () {
  return function bar () {
    function baz () {} // top level of block, declaration
  }
}

function () {} // SyntaxError: function statement requires a name

if (true){
  function () {} //SyntaxError: function statement requires a name
}
```

### 표현식을 구문으로 변환: 표현식 구문

이것은 매우 간단하다.

```js
2+2; //expression statement
foo(); //expression statement
```

표현식을 표현식 구문으로 변환할 수 있다. 단지 라인 끝에 세미콜론을 추가하거나 작업을 수행할 수 있는 [자동 세미콜론 삽입](https://dev.to/promhize/what-you-need-to-know-about-javascripts-automatic-semi-colon-insertion-78a)을 허용하면 된다. `2+2` 자체는 표현식이지만 완전한 라인은 문장이다.


```js
2+2 // on its own is an opposition

foo(2+2) //so you can use it anywhere a value is expected

true ? 2+2 : 1 + 1

function foo () {return 2+2}

2+2; //expression statement
foo(2+2;) //syntaxError
```

### 세미콜론 vs 쉼표 연산자

세미콜론으로 여러 구문을 같은 줄에 유지할 수 있다.

```js
const a; function foo () {}; const b = 2
```

쉼표 연산자는 다중 표현식을 연결하고, 마지막 표현식식 반환할 수 있도록 한다.

```js
console.log( (1+2,3,4) ) //4

console.log( (2, 9/3, function () {}) ) // function (){}

console.log( (3, true ? 2+2 : 1+1) ) // 4
```

> Javascript 엔진에 값을 기대하는 한 가지 방법은 괄호없이 ()를 사용하는 것이다. 각 표현식은 console.log의 인수로 처리된다.

```js
function foo () {return 1, 2, 3, 4}
foo() //4
```

모든 표현식은 왼쪽에서 오른쪽으로 계산되고 마지막 표현식이 반환된다.

### IIFEs (즉시 실행 함수 표현식)

익명함수는 표현식이 될 수 있다  자바스크립트가 값을 기대하는 곳에서 익명함수를 사용한다면, 괄호로 값을 기대 하도록 자바스크립트에 지시할 수 있다면,  익명함수를 해당 값으로 전달할 수 있다는 것을 의미한다.

```js
function () {}
```

그래서 위의 예제는 유효하지 않지만 아래의 예제는 유효하다.

```js
(function () {}) // this returns function () {}
```

익명의 함수를 괄호 안에 넣으면 익명의 함수가 즉시 실행되어 반환된다. 즉, 다음과 같이 즉시 호출할 수 있다.

```js
(function () {
  //do something
})()
```

그래서 아래 예제들은 가능하다.

```js
(function () {
  console.log("immediately invoke anonymous function call")
})() // "immediately invoke anonymous function call"

(function () {
  return 3
})() // 3

console.log((function () {
  return 3
})()) // 3

//you can also pass an argument to it
(function (a) {
  return a
})("I'm an argument") // I'm an argument
```

### 객체 리터럴 vs 블록 구문

```js
r: 2+2 // valid

foo()

const foo = () => {}
```

위의 내용은 글로벌 스코프에있는 일련의 구문으로, 유효한 구문으로 분석되어 실행된다 .`r`은 레이블이라고 부르며 루프를 깨는 데 주로 유용합니다. 예:

```js
loop: {
  for (const i = 0; i < 2; i++) {
    for (const n = 0; n <2; n++) {
      break loop //breaks outer loop and stops entire loop
    }
  }
}
```

표현식이나 표현식 구문에 레이블을 미리 설정할 수 있다. 이렇게함으로써 가변 실험실을 만들지 않는다는 점에 유의하라.

```js
lab: function a () {}
console.log(lab) //ReferenceError: lab is not defined
```

중괄호 `{ }`를 사용하면, 아래와 같이 표현식 구문과 명령문을 그룹화할 수 있다.

```js
{var a = "b"; func(); 2+2} // 4
```

위의 내용을 브라우저 콘솔에 붙여 넣으면 4를 반환하고 `console.log (a)`를 수행하면 문자열 `b`를 얻는다. 익숙한 객체 리터럴과는 다른 블록 구문 호출 할 수 있다.

```js
console.log({a: 'b'}) // {a: 'b'}

console.log({var a = "b", func(), 2+2}) // SyntaxError

const obj = {var a = "b", func(), 2+2} // SyntaxError
```

블록 문을 값 또는 표현식으로 사용할 수 없다. console.log는 함수이므로 인수로 구문을 사용할 수 없다. 그러나 객체는 리터럴은 허용한다.

```js
{} + 1 //1

{2} + 2 // 2

{2+2} + 3 // 3

{2+2} -3 // -3
```

구문 오류를 발생시키거나 각각 1, 4, 7을 반환할 것으로 예상할 수 있다. 구문은 값으로 사용될 수 없기 때문에 어떤 것도 반환해서는 안 된다는 것을 명심하라. 따라서 Javascript는 오류를 던지기보다는 `+` 연산자의 피연산자를 숫자 또는 문자열로 변환하려고 시도한다. 따라서 블록 문에 의해 반환되는 것은 피연산자로 사용되는 `0`으로 암묵적으로 강요된다.