# JavaScript 에서 Function 표현식과 선언이란 ?

function 구문은 함수를 선언합니다.

선언 된 함수는 "나중에 사용하기 위해 저장" 되고 호출 될 때 실행됩니다.

변수 선언은 "var"로 시작해야하는 것처럼 함수의 선언은 "function"으로 시작해야합니다.

```js
function bar() {
  return 3;
}
```

함수를 사용하려면 이름을 사용하여 호출해야합니다. 예: bar ();

## 함수 표현식이란 무엇인가요 ?

함수는 표현식을 사용하여 정의 할 수도 있습니다.
함수 표현식은 변수에 저장 될 수 있습니다.

```js
var x = function (a, b) {return a * b};
```

함수를 변수에 저장 한 후에 변수를 함수처럼 사용할 수 있습니다. 
변수에 저장된 함수에는 함수 이름이 필요없습니다. 변수 이름을 사용하여 호출합니다.

## 함수 표현식 vs 함수 구문 

#### Example: Function Expression

```js
alert(foo()); // ERROR! foo wasn't loaded yet
var foo = function() { return 5; }
```

#### Example: Function Declaration 

```js
alert(foo()); // Alerts 5. Declarations are loaded before any code can run.
function foo() { return 5; }
```


- 함수 선언은 코드가 실행되기 전에 로드됩니다. 함수 표현식은 인터프리터가 해당 코드 행에 도달 할 때 로드됩니다.
- var 문과 유사하게 함수 선언은 다른 코드의 맨 위로 올라갑니다. 함수 표현식은 호이스트되지 않기 때문에 정의 된 범위의 로컬 변수 복사본을 유지할 수 있습니다.

## 함수 표현식의 이점 

함수 표현식이 함수 선언식보다 더 유용하게 사용되는 몇 가지 다른 방법이 있습니다.

- 클로저
- 다른 함수에 인수로서
- 즉시 호출 된 함수 식 (IIFE)




