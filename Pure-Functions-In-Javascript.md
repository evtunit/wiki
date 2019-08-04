# Pure Functions In Javascript

번역 : [https://appdividend.com/2017/04/10/pure-functions-in-javascript/](https://appdividend.com/2017/04/10/pure-functions-in-javascript/)


Javascript의 순수 함수는 호출 시간에 전달 된 매개 변수로 반환 값이 결정되는 일종의 함수입니다.

Javascript는 순전히 기능적인 언어가 아닙니다. 함수를 객체 (first-class function)로 취급합니다.

함수에 입력 매개 변수 값을 제공하고 항상 동일한 결과를 반환하면 자바 스크립트의 **순수 함수** 가됩니다.

## _Pure Functions_

**순수 함수 프로그래밍** 개념은이 두 엄격한 함수 프로그래밍 언어에서 파생됩니다.

1.  [Clojure](https://clojure.org/about/clojurescript)
2.  [Scala](https://www.scala-lang.org/)

**순수 함수** 특정 **Ajax 호출** 또는 모든 **데이터베이스 호출** 에만 의존하지 않습니다. 단순히 매개 변수를 취하고 함수 본문에서 일부 계산을 수행하고 이를 반환합니다.

_인수에 따라 전적으로 함수 호출에 전달합니다 ._

![Pure Functions In Javascript](https://appdividend.com/wp-content/uploads/2017/04/Pure-Functions-In-Javascript-1.png)


**입력 값을 출력 값에** 매핑합니다.

이 프로그래밍 스타일을 **프로 시저 프로그래밍** 이라고 부를 수 있습니다. 이는 해당 함수에 대한 입력에 대해 수행되는 일련의 연산이기 때문입니다.

_함수는 해당 함수로 전달되는 변수에만 매개 변수로 액세스 할 수 있습니다 ._

전역 변수 또는 클래스의 인스턴스는 **순수 함수** 내에서 합법적 인 변수로 간주되지 않습니다.

변수의 전역 범위는 **순수 함수의 생성에 사용되지 않습니다.**

**수학 함수로 작동하는 명령 집합입니다.**

이 함수는 역할 기반 함수로 인수를 취하고 해당 변수에 대한 연산을 수행 한 다음 반환합니다.

## **Advantages:**

1. 주어진 함수의 결과를 항상 예측할 수 있습니다.
2. 테스트는 항상 어휘 범위 밖에있는 변수의 상태를 변경하지 않기 때문에 쉽습니다.
3. 기능적 프로그래밍은 코드 라인을 줄이고 거의 루프를 제거합니다.

## Examples:

```javascript
//main.js  
let pure = (y) => { 
	let x = 10; 
	x = x + y; 
	return x; 
}; 

let y = pure(3); 
console.log(y); // 13  
let z = pure(3); 
console.log(z); // 13
```

** 브라우저에서 직접 위의 코드를 실행하면 다음 문제가 발생할 수 있습니다. **

## Possible Errors

1.  **You can get any syntax error.**
2.  **If you perform code directly in your browser, then chances are very high to fail the webpack compilation process.**

## Possible Solutions

1.  **[Beginner’s Guide To Setup ES6 Development Environment](http://appdividend.com/2017/03/28/beginners-guide-to-setup-es6-development-environment/)이 문서를 엄격히 준수하고 **main.js** 파일에 코드를 추가하십시오.

위의 예에서 함수의 호출 시간에 매번 매개 변수를 3으로 전달하면 항상 모든 상황에서 **13**을 반환합니다. 그래서 **순수한 자바 스크립트 기능입니다.**

_자바 스크립트에서 숫자, 문자열 및 부울과 같은 간단한 변수는 변경할 수 없습니다. 그러나 객체 및 배열과 같은 데이터 구조는 데이터 모음이므로 변경할 수 있습니다 ._

```javascript
//main.js

const numbers = [1, 2, 3];
numbers.forEach(x => console.log(x)); //1 2 3
```

**Arrays**  and  **Objects**  는 non-primitive 데이터 유형이므로 변형 될 수 있습니다.

위의 예제는 하나의 배열에있는 원소의 값을 하나씩 출력합니다. 그래서 **순수한 자바 스크립트 함수입니다.**

## Example #1

```javascript
//main.js

let arr = [1, 2, 3, 4, 5, 6];

even = arr.filter(function (element) {
    return element%2 === 0;
});
console.log(even); //[2,4,6]
```

배열의 이전 상태를 변경하지 않으면 **순수 함수라고도합니다.**

위의 예에서 **Filter** 함수는 조건을 검사하고 만족 스러운지 여부를 확인합니다. 새 배열 집합을 반환합니다.

## Example #2

```javascript
//main.js

let dances = [
    {name: 'Krunal', type: 'Robot'},
    {name: 'Ankit', type: 'Salsa'},
    {name: 'Tom', type: 'Garba'},
    {name: 'Jerry', type: 'Belly'}
];
let collection = dances.filter((dance)=>{
   return  dance.type === 'Robot'
});
console.log(collection); // [object]
```