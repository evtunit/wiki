# Learn and Understand Recursion in JavaScript

번역 : [https://codeburst.io/learn-and-understand-recursion-in-javascript-b588218e87ea](https://codeburst.io/learn-and-understand-recursion-in-javascript-b588218e87ea)

## What is Recursion?
재귀는 간단히 함수가 자신을 호출할때를 말합니다.

바로 뛰어 들어 아마도 가장 유명한 재귀 예제를 살펴 보겠습니다. 이 예는 제공된 정수의 계승을 반환합니다.

```javascript
function factorial(x) {  
  if (x < 0) return;  
  if (x === 0) return 1;  
  return x * factorial(x - 1);  
}factorial(3);  
**// 6**
```

** 우와 괜찮다면 괜찮습니다. **. 중요한 부분은 4 번 줄에서 일어나고 있습니다 : `return x * factorial (x — 1);.` 보시다시피, 함수는 실제로 자신을 다시 호출하지만 (`factorial (x-1)`), 처음 호출 했을 때보다 1이 작은 매개 변수를 사용합니다. 이것은 재귀 함수입니다.

> 이 코드 예제를 더 자세히 설명하기 전에 계승이 무엇인지 이해하는 것이 중요합니다.

숫자의 계승을 얻으려면 숫자 1에 도달 할 때까지 그 숫자에 1을 뺀 값을 곱하십시오.

예제 1: **4** 의 팩토리얼은  _4 * 3 * 2 * 1_, or  **24**.

예제 2: **2**  의 팩토리얼은  _2 * 1_, or  **2**.

이제 우리 고등학교 수학 수업이 끝났으니, 좋은 것들로 돌아갈 수 있습니다!

## The three key features of recursion

모든 재귀 함수에는 3가지 특징이 있습니다.

### A Termination Condition

`if (뭔가 나쁜 일이 생겼다) {STOP};` 종료 조건은 우리의 재귀 실패 안전입니다. 비상 브레이크처럼 생각하십시오. 입력이 잘못되면 재귀가 실행되지 않도록 합니다. 팩토리얼 예제에서 `if (x <0) return;` 은 종료 조건입니다. 음수를 계승 할 수 없으므로 음수가 입력 된 경우 재귀를 실행하고 싶지 않습니다.

### A Base Case

간단히 말해 : `if (이러한 일) {예! 끝났다.};` 기본 사례는 재귀를 중지한다는 점에서 종료 조건과 유사합니다. 그러나 종료 조건은 나쁜 데이터에 대한 포괄적 인 것임을 기억하십시오. 기본 사례는 재귀 함수의 목표입니다. 기본 사례는 일반적으로 `if` 문 안에 있습니다. 계승 예에서 `if (x === 0) return 1;` 이 기본 사례입니다. x 를 0으로 낮추면 계승을 결정하는 데 성공했습니다!

### The Recursion

간단히 말해서 : 우리의 함수 자체를 호출합니다. 계승 예에서 `return x * factorial (x — 1);` 은 실제로 재귀가 발생하는 곳입니다. 우리는 숫자 `x` 의 값에 `factorial (x-1)` 이 평가하는 모든 값을 곱한 값을 반환합니다.

### All Three Together

이제 우리는 계승 예제가 어떻게 작동하는지 전혀 알지 못하지만 이상적으로는 의미를 갖게 되었습니다.

```javascript
function factorial(x) {  
  **// TERMINATION**  
  if (x < 0) return; **// BASE**  
  if (x === 0) return 1; **// RECURSION**  
  return x * factorial(x - 1);  
}factorial(3);  
// 6
``` 

## Factorial Function Flow

팩토리얼 함수를 호출 할 때 어떤 일이 발생하는지 정확하게 살펴 보겠습니다.

1. 먼저 3의 값을 전달하는 함수를 호출합니다.

```javascript
factorial(3);
```

2. 이로 인해 기능이 실행됩니다. if 문이 실패하고 재귀 라인이 실행됩니다. `factorial (3-1)` 의 값과 3을 곱한 정수를 반환합니다.

```javascript
return **3** * factorial(**2**);
```

3. `factorial (2)` 가 실행되면  if 문들이 실패하고 재귀가 발생하는 경우. `factorial (2-1)` 의 값을 2에 곱한 값을 반환합니다.

```javascript
return **2** * factorial(**1**);
```

4. `factorial (1)` 이 실행되면 if 문들이 실패하고 재귀가 발생하는 경우. `factorial (1-1)` 의 값 1에 곱한 값을 반환합니다.

```javascript
return **1** * factorial(**0**);
```

5. factorial (0)이 실행되면 다른 일이 발생합니다. 0이 기본 사례이므로 if 문이 전달되고 함수가 1을 반환합니다.

```javascript
if (x === 0) return **1**;
```


**이제 함수가 마침내 반환되었으므로 모든 것이 '풀립니다'. 재귀는 단순히 중첩 된 함수 호출 그룹이기 때문입니다. 중첩 함수를 사용하면  _most inner_ 중첩 함수가 먼저 반환됩니다.**

 >이것은 이해해야 할 중요한 부분입니다. 처음에 이해하지 못하면이 내용을 몇 번 읽어보십시오.

`factorial(0)`  returns  `1`

`factorial(1)`  returns  `1 * factorial(0)`, or just  `1*1`

`factorial(2)`  returns  `2 * factorial(1)`, or just  `2*1*1`

`factorial(3)`  returns  `3 * factorial(2)`, or just  `3*2*1*1`

```javascript
return 1 * 1 * 2 * 3  
**// 6**
```

따라 왔나요? 다음은 다르게 구성된 동일한 설명입니다.

```javascript
factorial(3) returns 3 * factorial(2)  
factorial(2) returns 2 * factorial(1)  
factorial(1) returns 1 * factorial(0)  
factorial(0) returns 1// Now that we've hit our base case, our function will return in order from inner to outer:factorial(0) returns 1                 => 1  
factorial(1) returns 1 * factorial(0)  => 1 * 1  
factorial(2) returns 2 * factorial(1)  => 2 * 1 * 1  
factorial(3) returns 3 * factorial(2)  => 3 * 2 * 1 * 1// 3 * 2 * 1 * 1 = **6**
```


아직도 무슨 일이 일어나고 있는지 이해하지 않아도 괜찮습니다. 혼란을 해소하기 위해 다른 예제로 넘어 갑시다.

## Lets look at a second example

이 두 번째 예에서는 완전히 다른 길을 가고 있습니다. 이것은 인터넷에서 인기있는 또 다른 예이며 문자열을 뒤집는 것과 관련이 있습니다.

> 이 방법은 JavaScript에서 문자열을 뒤집는 가장 효율적인 방법은 아닙니다. 훨씬 빠른 방법이 있습니다. 재귀의 일반적인 인터넷 예 일 뿐이므로 이를 다루고 싶었습니다.

다음 코드를 보십시요

```javascript
function revStr(str){  
  if (str === '') return '';  
  return revStr(str.substr(1)) + str[0];  
}

revStr('cat');  
// **tac**
```

Instantly you can (ideally) notice a few things:

1. `str === ""` 가 우리의 기본 사례입니다. 우리의 끈에 문자가 없으면 성공했습니다.
2. `return revStr (str.substr (1)) + str [0];` 은 재귀 마술이 일어나는 곳입니다.
3. 여기엔 종료 사례가 없습니다. 이 경우 기본 사례가 종료 사례이기 때문입니다. 음수 문자가 포함 된 문자열을 얻을 수 없습니다. 함수에만 문자열 만 입력하면 괜찮을 것입니다.

### Let’s break it down line by line

이 예에서는 문자열 `cat` 을 반전시킵니다. 우리는 함수를 호출하고 문자열 cat을 전달하는 것으로 시작합니다.

```javascript
revStr('cat');
```


재귀 사례가 실행됩니다.

JavaScript에서 `substr ()` 메소드는 지정된 위치에서 시작하는 문자열을 리턴합니다. `ʻcat'.substr (1) ===‘at’`

`str [0]` 은 문자열에서 해당 인덱스의 문자를 제공합니다. 따라서 `cat [0] === 'c'`

```javascript
return revStr(str.substr(1)) + str[0];// SAME AS  
return revStr('at') + 'c'
```

재귀 사례가 다시 실행됩니다.

```javascript
return revStr(str.substr(1)) + str[0];// SAME AS  
return revStr('t') + 'a'
```

우리의 재귀 사례는 마지막 한 번 실행됩니다.

```javascript
return revStr(str.substr(1)) + str[0];// SAME AS  
return revStr('') + 't'
```

이번에는 기본 사례가 실행되고 함수는 빈 문자열을 반환합니다.

```javascript
if (str === '') return '';
```

**이제 함수가 반환되었으므로 모든 것이 '풀리고'순서대로 돌아옵니다.**

```javascript
return ‘’ + ‘t’ + ‘a’ + ‘c’  
// **tac**
```

### Break it down even more

```javascript
revStr('cat')  returns 
revStr('at') + 'c'revStr('at')   returns 
revStr('t') +  'a'revStr('t')    returns 
revStr('') +   't'revStr('')     returns               ''
```


```javascript
revStr('')     returns                ''  => ''
revStr('t')    returns revStr('') +   't' => '' + 't'
revStr('at')   returns revStr('t') +  'a' => '' + 't' + 'a'
revStr('cat')  returns revStr('at') + 'c' => '' + 't' + 'a' + 'c'

**// tac**
```

