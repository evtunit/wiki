# 자바스크립트에서 === 또는 == 동등 비교 연산자를 사용해야 하는가?

자바스크립트에서 두가지의 다른 동등 비교 연산자가 있다. 이는 ```===``` 와 ```==``` 이다.  이는 triple equal 그리고 double equal이라 부른다. 둘다 사용했었겠지만 둘 중 하나를 확신하여 선택해서 코드에 사용한 적은 없다. 둘 중 하나를 결정하는 확실한 방법을 알고 싶어한다. 

알고보니 어떤 것을 사용할 지에 대해 결정하는 명확한 방법이 있다. 그리고 이와 같은 로직을 처리하는 비동등 비교 연산자 ```!==``` 와 ```!=```가 있다.

## 동등 비교와 타입을 비교할 땐 ===
```javascript
x === y
```
triple equal 연산자(```===```)는 두 피연산자가 같은 타입이고 같은 값이면 true을 리턴한다. 만약 타입이 다르면, false을 리턴한다.

이러한 동등의 정의는 대부분의 케이스에 사용된다. 문자열 ```"0"```과 숫자 ```0```을 비교할 때 false가 나오는 것을 알 수 있다.

[D. Crockford](https://bytearcher.com/articles/douglas-crockford-javascript-good-parts-book-review/)와 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#Loose_equality_using)에서 나온 자료에서 모두 자바스크립트에서 프로그래밍할 때 triple 동등 연산자만 사용해야하며 double 동등 연산자는 완전히 무시 되어야 한다고 충고한다.

## 복잡한 타입의 변환을 수행할 땐 ==
```javascript
x == y
```
double 동등 연산자 (```==```)는 타입이 다를 경우 타입 변환을 실행하고 그 다음 같은지 비교 한다. 
만약 타입이 다르면, 피연산자 중 하나 또는 둘 다 먼저 같은 타입으로 변환된다. 변환 규칙은 복잡하고 인수의 타입에 따라 달라진다. 전체 비교 표 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#Loose_equality_using) 또는 [ECMAScript](http://ecma-international.org/ecma-262/5.1/#sec-11.9.3) 사양을 참고하면 된다.

예를 들어 문자열 "0" 그리고 숫자 0을 비교 했을 때 첫번째 인수는 숫자로 변환된다. 

```javascript
 "0" == 0 // becomes
 ToNumber("0") === 0
```
While the string and number comparison is understandable, the complex rules for other types lead to illogical results. For example, see the comparisons between ```null```, ```undefined``` and ```false```:
문자열 그리고 숫자열 비교는 이해할 수 있지지만, 다른 유형의 복잡한 규칙은 비논리적인 결과를 초래한다. 예를 들어,
 ```null```, ```undefined```, ```false```의 비교가 있다.  
```javascript
false == undefined // false
false == null      // false
null == undefined  // true
```

## 이제 알았지 ????????? - 오직 === 만 사용해
동등 비교 연산자는 triple 비교 연산자를 사용한다. triple 동등 연산자를 사용하여 타입을 비교 했을 때, 결과는 항상 false이다. 변환 규칙을 외우는 데 어렵지 않게 기대하는 값을 얻을 것이다. (You will get the results you expect that are not subject to hard to memorize conversion rules.) 사과와 오랜지를 비교하는 것은 예상대로 잘못된 것이 나올 것이다. 

이러한 비교 연산자는 primitive 타입을 비교하기 위한 것이다. 객체나 deep 한 배열의 같음을 비교하기 위해서는 피연산자를 구조적으로 비교하는 다른 접근법이 필요하다. 

> 원문 : https://bytearcher.com/articles/equality-comparison-operator-javascript/