최근 어떤 사람이 Javscript 의 기본 과정으로 돌아가고 싶다고 했을 때, Wes Bos 의 "ES6 for Everyon" 과정을 추천했다.
Wes 는 뛰어난 선생님이며 복잡한 주제에 대해서 잘 풀어 설명 할 수 있는 커뮤니케이터이다.

Wes 의 과정을 통하여 기초를 다졌고, Javascript 를 탐험하기 시작했다.

## var, let 과 const 의 차이점 

JS 에서 변수를 선언하는 세가지 방법:

```js
var width = 100;
let height = 200;
const key = 'abc123';
```

### var

var 는 재할당되고 업데이트 될 수 있습니다.

```js
// Define the variable:
var width = 100;

// Call the variable:
width;
// It returns:
100
// Reassign the variable and call it again:
width = 200;

width;
// Returns:
200
```

var 변수는 함수 스코프입니다. 이것은 무엇을 의미하나? 생성된 함수 내에서만 사용 할 수 있다는 것을 의미한다.
함수 내부에서 생성되지 않았다면 전역 범위로 지정된다.

함수 내부에 var 가 정의되어 있고 함수 외부에서 호출하려고하면 동작하지 않는다.

```js
function setWidth(){
    var width = 100;
    console.log(width);
}
width;
// Returns:
Uncaught ReferenceError: width is not defined
```

var 는 함수 내에서 정의 되기 떄문에 함수의 밖에서는 사용이 불가능 한 것 이다.

### 스코프를 이해하다

```js
Example: 
var age = 100;
  if (age > 12){
    var dogYears = age * 7;
    console.log(`You are ${dogYears} dog years old!`);
  }
```

위의 예제에서 console 의 출력결과 

```js
100
You are 700 dog years old!
``

만약 `dogYears` 를 부른다면 

```js
dogYears;
// returns:
700
```

var 변수는 함수 스코프의 범위이므로 `var dogYears` 
위 예제의 함수 안에서 정의 되어 있지 않았기 때문에 - var가 'window'또는 전역 범위에서 대신 정의된다

즉, var는 중괄호에 국한되지 않는다

### let, const 의 이점

let 과 const 를 사용하면 어떤 이점이 있을까 ?
스코프가 함수 단위가 아니라 블록 단위로 바뀌게 됩니다.

블록이란 무엇일까 ? {} 중괄호를 블럭이라고한다.

위의 예제에서 var 를 const 로 변경한 후 dogYears 를 호출하면 Uncaught ReferenceError 에러를 반환합니다.

```js
var age = 100;
  if (age > 12){
    let dogYears = age * 7;
    console.log(`You are ${dogYears} dog years old!`);
  }
```

let 대신 const 를 이용할 수 있습니다.

- var 는 함수 스코프이다
- let, const 는 블록 스코프이다
- 함수 스코프는 함수 안이다
- 블록 스코프는 중괄호이다 











