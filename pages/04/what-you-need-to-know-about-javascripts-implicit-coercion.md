# Javascript의 암묵적 형 변환(Implicit coercion)에 관하여

> 원문: [What you need to know about Javascript's Implicit Coercion](https://dev.to/promhize/what-you-need-to-know-about-javascripts-implicit-coercion-e23)

자바스크립트의 암묵적 형 변환은 예기치 못한 값 타입을 기대되는 타입으로 변환하려고 시도하는 것을 말한다. 따라서 문자열 등을 기대하는 곳에 올바른 문자열을, 숫자를 기대하는 곳에 올바른 숫자를 전달해 줄 수 있다. 이것은 자바스크립트의 특징 중 하나이다.


```js
3 * "3" //9
1 + "2" + 1 //121

true + true //2
10 - true //9


const foo = {
  valueOf: () => 2
}
3 + foo // 5
4 * foo // 8

const bar = {
  toString: () => " promise is a boy :)"
}
1 + bar // "1 promise is a boy :)"


4 * [] // 0
4 * [2] // 8
4 + [2] // "42"
4 + [1, 2] // "41,2"
4 * [1, 2] // NaN

"string" ? 4 : 1 // 4
undefined ? 4 : 1 // 1

```

## 숫자 계산에서 숫자가 아닌 값

### String

연산자(-, *, /, %)가 포함된 숫자 계산에서 피연산자로 문자열을 전달하게 되면, 문자열에 대한 숫자 변환 프로세스는 자바스크립트에 내장된 `Number` 함수를 호출하는 것과 유사하다. 이것은 매우 간단하다. 숫자만 포함하는 문자열은 숫자만큼 변환되지만, 숫자가 아닌 문자를 포함하는 문자열은 `NaN`을 반환한다. 아래에 예시를 보자.

```
3 * "3" // 3 * 3
3 * Number("3") // 3 * 3
Number("5") // 5

Number("1.") // 1
Number("1.34") // 1.34
Number("0") // 0
Number("012") // 12

Number("1,") // NaN
Number("1+1") // NaN
Number("1a") // NaN
Number("one") // NaN
Number("text") // NaN
```

#### 더하기 연산자에 대한 예시

`+` 연산자는 다른 수학 연산자와 다르게 두가지의 기능을 수행한다.

1. 수학적 덧셈
2. 문자열을 연결(concatenation)

문자열이 + 연산자의 피연산자일 경우, 자바스크립트는 문자열을 숫자로 변환하는 대신 숫자를 문자열로 변환한다.

```js
// 문자열로 연결
1 + "2" // "12"
1 + "js" // "1js"

// 덧셈
1 + 2 // 3
1 + 2 + 1 // 4

// 덧셈 후 연결
1 + 2 + "1" // "31"
(1 + 2) + "1" // "31"

// 모두 연결
1 + "2" + 1 // "121"
(1 + "2") + 1 // "121"
```

### Object

대부분의 자바스크립트의 Object 변환은 대게 `[object Object]`로 변환된다. 예시를 보자.

```js
"name" + {} // "name[object Object]
```

모든 자바스크립트 Object는 `toString` 메서드를 상속 받기 때문에 Object가 String으로 형 변환이 가능하다. `toString` 메서드로 반환된 값은 문자열 연결, 수학적 계산과 같은 작업에 사용된다.

```js
const foo = {}
foo.toString() // [object Object]

const baz = {
  toString: () => "I'm object baz" // toString을 재정의
}

baz + "!" // "I'm object baz!" (toString이 호출되어 문자열 연결)
```

수학적 표현식일 때 자바스크립는 `toString`의 반환 값을 숫자로 변환하려고 한다.

```js
const foo = {
  toString: () => 4
}

2 * foo // 8
2 / foo // 0.5 
2 + foo // 6
"four" + foo // "four4"

const baz = {
  toString: () => "four"
}

2 * baz // NaN (숫자로 변환)
2 + baz // 2four

const bar = {
  toString: () => "2"
}

2 + bar // "22"
2 * bar // 4 (숫자로 변환)
```

#### Array

배열에 상속된 `toString` 메서드는 Object와 동작이 약간 다르다. 배열의 `join` 메서드를 매개변수 없이 호출하는 것과 유사하게 동작한다.

```js
[1,2,3].toString() // "1,2,3"
[1,2,3].join() // "1,2,3"
[].toString() // ""
[].join() // ""

"me" + [1,2,3] // "me1,2,3"
4 + [1,2,3] // "41,2,3"
4 * [1,2,3] // NaN
```

따라서 문자열로 기대되는 식에서 배열을 전달하게 되면, 자바스크립트는 배열의 `toString` 메서드의 반환 값과 연결한다. 결과로 숫자를 기대할 경우 반환 값을 숫자로 변환하려고 시도한다.

```js
4 * [] // 0 ([].toString => '', Number('') => 0)
4 / [2] // 2

//similar to
4 * Number([].toString())
4 * Number("")
4 * 0

//

4 / Number([2].toString())
4 / Number("2")
4 / 2
```

#### True, False and ""

```
Number(true) // 1
Number(false) // 0
Number("") // 0

4 + true // 5
3 * false // 0
3 * "" // 0
3 + "" // "3"
```

#### `valueOf` 메서드

`toString` 메서드 이외에도 자바스크립트가 Object가 문자열이나 숫자 값으로 반환 되기를 기대할때 사용되는 `valueOf` 메서드도 정의 할 수 있다.

> valueOf() 메서드는 특정 객체의 원시 값을 반환한다.

```
const foo = {
  valueOf: () => 3
}

3 + foo // 6
3 * foo // 9
```

`toString`과 `valueOf` 메서드가 모두 Object에 정의된 경우 자바스크립트는 `valueOf` 메서드를 사용한다.

```
const bar = {
  toString: () => 2,
  valueOf: () => 5
}

"sa" + bar // "sa5"
3 * bar // 15
2 + bar // 7
```

`valueOf` 메서드는 숫자 값을 나타내야 하는 Object를 위해 만들어졌다.

```js
const two = new Number(2)

two.valueOf() // 2
```

## Falsy and Truthy 

모든 자바스크립트 값은 참(true) 또는 거짓(false)으로 형 변환될 수 있다. boolean `true`에 대한 형 변환은 값이 truty라는 것을 의미한다. boolean `false`에 대한 형변화는 falsy이라는 것을 의미한다.

자바스크립트에는 falsy 값을 반환하는 값들이 존재한다.

1. false
2. 0
3. null
4. undefined
5. ""
6. NaN
7. -0

이 외에 나머진 값들은 truthy 값을 반환한다.

```js
if (-1) // truthy
if ("0") // truthy
if ({}) // truthy
```

위의 예시들은 적절해 보인다. 그러나 값의 진실성을 결정하려고 할땐 좀 더 명시적으로 사용하는 것이 좋다. 기본적으로 자바스크립트의 암시적 형변환에 의존하면 안된다. 비록 암시적 형변환을 제대로 알고 있다고 생각해도 말이다. 아래와 같이 사용하자.

```js
const counter = 2

if (counter)
```

아래의 예시들은 진실성을 판단하려고 할때 좀더 명확한 방법들이다.

```js
if (counter === 2)

//or

if (typeof counter === "number")
```

예를들어 숫자를 매개변수로 받는 함수를 작성한다고 가정해 보자.

```js
const add = (number) => {
  if (!number) new Error("Only accepts arguments of type: number")
  //your code
}
```

자, 이제 add 함수를 0과 함께 호출한다면(add(0)), 의도하지 않은 에러가 계속 발생할 것이다.

```js
add(0) // Error: Only accepts arguments of type: number

//better check

const add = (number) => {
  if (typeof number !== "number") new Error("Only accepts arguments of type: number")
  //your code
}

add(0) // no error
```

## NaN

`NaN`은 자기 자신과 같지 않은 특별한 숫자 값이다.

```js
NaN === NaN // false

const notANumber = 3 * "a" // NaN

notANumber == notANumber // false
notANumber === notANumber // false
```

`NaN`은 자바스크립트에서 자기 자신과 같지 않은 유일한 값이다. 따라서 `NaN`을 그 자체와 비교하여 확인할 수 있다.

```js
if (notANumber !== notANumber) // true
```

ES6에서 NaN 값을 확인할 수 있는 `Number.isNaN` 메서드가 등장했다.

```js
Number.isNaN(NaN) // true
Number.isNaN("name") // false
```

전역 함수 `isNaN` 함수 사용을 주의하라. `NaN`인지 확인하기 위해 인수를 형변환 시도한다. 예시를 보자.

```
isNaN("name") // true
isNaN("1") // false
```

전역 함수 `isNaN` 사용은 피해야하며, 작동 방식은 아래 함수와 유사하다.

```js
const coerceThenCheckNaN = (val) => {
  const coercedVal = Number(val)
  return coercedVal !== coercedVal ? true : false
}

coerceThenCheckNaN("1a") // true
coerceThenCheckNaN("1") // false
coerceThenCheckNaN("as") // true
coerceThenCheckNaN(NaN) // true
coerceThenCheckNaN(10) // false

```