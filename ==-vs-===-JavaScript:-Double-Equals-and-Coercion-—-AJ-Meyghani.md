# == vs === JavaScript: Double Equals and Coercion

 == (double equals 또는 느슨한 비교) 연산자는 흥미로운 연산자 입니다. 어떻게 동작하는지 모르기 때문에 사용을 꺼려합니다.
그러나 그것은 피할만한 좋은 이유는 아닙니다. == 의 동작원리를 이해하고 피해야하는 이유를 아는 것이 더 좋습니다.
이유를 알게되면 왜 === 를 사용해야하는지 알 수 있습니다. 

이 글을 읽고 난 후 강제 변환과 double equals 의 모든 것을 알게 될 것 입니다.

## Double equals 

double equals 연산자는 타입이 같지 않다면 피연산자를 강제로 변환시킵니다. 타입이 같다면 triple equals 연산자를 이용합니다.
그래서 2 == 3 이랑 2 === 3 은 같습니다. 2 와 3 모두 number 타입이기 때문입니다.
그러나 타입이 같지 않다면 피연사들중 하나 또는 모두를 강제 변환하려합니다. 
예를 들어 2 == "3" 을 비교한다면 숫자 2와 3 인 문자열을 비교합니다. 이 떄 JavaScript 는 문자열 3을 숫자 3으로 강제 변환 한 다음 2를 호출합니다. 2 === 3 을 비교하고 결과는 false 가됩니다.

우리는 double equals 연산자의 기초를 알고 더 깊이 연산자가 어떻게 결정하는지에 대한 방법을 학습합니다.

## The Abstract Equality Comparison

(참고: ECMAScript@2015 언어 스펙에서 추상적인 비교에 대한 정보를 읽을 수 있습니다)
x 와 y 를 비교할 때 x 와 y 는 모든 타입이 될 수 있습니다.

시작: 만약 타입의 유형이 같다면 Javascript 는 === 연산자를 호출하여 완료처리 할 것 입니다.
그러나 다른 타입일 경우 Javscript 는 다른 방법을 따릅니다. Javascript 무엇이 필요한지 알아내기 위한 시도를 합니다.

다음 실행 단계입니다.

a. 먼저 null 그리고 undefined 를 비교합니다. 

b. 문자열과 숫자를 비교합니다. 문자열 값을 number 로 형변환하고 == 를 호출하고 처음 단계로 돌아갑니다.

```js
2 == "3"
      ↓   coerce "3" to number 3.
2 ==  3   calling double equals again, and start over
```

c. boolean 값을 숫자로 형변환하고 == 로 비교합니다.

```js
true == "3"
↓             coerce true to number 1.
1    == "3"   start over again.
```

d. 객체를 숫자, 문자열, 심볼과 비교. 객체를 원시 타입으로 변경 한 후 비교합니다. 

```js
{ a: 'hello'}      ==     "5"
     ↓                          coerce the object to a primitive
"[object Object]"  ==     "5"   and start over again
```

다른 예제



```js
[1,2,3]     ==     "5"
  ↓                       coerce object to a primitive
"1,2,3"     ==     "5"    and start over again
```

위의 단계중 일치하는 것이 없다면 마지막에 false 를 반환합니다.

![image](https://cdn.filestackcontent.com/eTIpVrOQcqBNTN6Ma8i4)

## 추상 연산자

객체가 어떻게 원시 타입으로 형변환 되는지 설명합니다.

먼저, 객체의 valueOf 메소드를 호출합니다. valueOf 통해 원시 타입이 리턴되면 그 값을 사용합니다.

그렇지 않은 경우 Object 의 toString 메서드를 호출합니다. toString 이 원시 타입을 반환하면 그것을 사용합니다.

그렇지 않으면 오류가 발생됩니다.

Note1: 기본적으로 발생하는 단계입니다. 문자열 선언을 암시하면 Javscript 는 valueOf 보다 toString 을 먼저 호출합니다. Javascript 는 기본적으로 숫자를 선호하기 때문에 valueOf 를 먼저호출하고 toString 을 호출합니다.

Note2: Date 객체를 원시 객체로 강제 변환할 때 Javascript 는 문자열을 선호합니다. 그렇기 때문에 toString 을 먼저 호출 한 다음 valueOf 를 호출합니다.

예시1 

```js
var x = {};
x.valueOf();   // -> {} : not a primitive, call toString now.
x.toString();  // -> "[object Object]": string is a primitive, I like it!
```

위 예제에서 x 가 원시 타입으로 변환되면 ㄹ과는 문자열이 됩니다. 

예제2

```js
var x = {name: 'Tom'};
x.valueOf = function () {return 5;};
x.valueOf();   // -> 5 : number primitive, I liked it!
```

위의 예제에서 x 가 원시타입으로 강제 변환되면 숫자 5 가 결과가 됩니다.

예제3

```js
// coercing an array of numbers to a primitive
var x = [1,2,3];
x.valueOf();   // -> [1,2,3] : not a primitive, call toString now.
x.toString();  // -> "1,2,3" : string is a primitive, I like it!
```

예제에서 x 가 원시타입으로 강제 변환되면 결과는 문자 123 이 됩니다.

예제 4

```js
// coercing an array of elements with different types to a primitive
var x = [undefined, null, true, 1, "5", new Date(), {a: 2}, [1,2,3]]
x.valueOf();   // -> Array itself : not a primitive, call toString now.
x.toString();  // -> ",,true,1,5,Sat Mar 05 2016 10:59:47 GMT-0500 (EST),[object Object],1,2,3" : string is a primitive, I like it!
```

예제에서 x 가 원시타입으로 변환 될 때 결과는 다음과 같은 문자열이 됩니다.

 ", true, 1,5, Sat Mar 05 2016 10:59:47 GMT-0500 (EST), [object Object], 1,2,3 "

number 의 강제변환: 
toNumber 가 다른 유형에 대해 수행하는 작업입니다.

- undefined → NaN
- null → 0
- number → number 자체 
- boolean → false 0, true 1 
- string → 숫자 반환이면 반환, parse string, 그렇지 않으면 NaN, 빈문자열은 0 

Eg: "5" → 5, "" → 0, "29xY" → NaN - object:

먼저 ToPrimitive 를 호출 한 다음 결과에 대해 ToNumber 를 호출합니다. 


