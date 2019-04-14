# JavaScript type coercion explained Know your engines

[2018년 5월 2일에 수정됨]:이 글은 [available in Russian](https://medium.com/@sergeybulavyk/%D0%BF%D1%80%D0%B5%D0%BE%D0%B1%D1%80%D0%B0%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D1%82%D0%B8%D0%BF%D0%BE%D0%B2-%D0%B2-javascript-35a15ddfc333)의 글이다. Serj Bulavyk의 노력에 박수를 ...


*타입 변환*은 한 타입에서 다른 타입(문자열에서 숫자, object에서 boolean 등)으로 값을 변환 하는 과정이다. 원시적이든 object이든 어떤 타입이든 타입 변환의 대상이 된다. 호출 하기 위해서는 원시 타입들은 number, string, boolean, null, undefined, ES6에 추가된 Symbol이다. 

타입 변환의 실질적인 예를 들자면, 느슨한 동등 연산자 ```a``` 와 ```b```의 타입에 대해 어떻게 적용되는지 보여주는 [JavaScript Comparison Table](https://dorey.github.io/JavaScript-Equality-Table/)를 보라. 이 표는  ```==``` 연산자가 암묵적인 타입의 형변환으로 인해 혼돈을 주고 이러한 조합들은 모두 기억하기는 힘들다. (scary를 혼돈을 준다고 의역함...) 그렇게 할 필요 없고, 그냥 기본적인 타입의 타입 변환의 원리를 배우기만 하면 된다. 

이 글은 자바스크립트의 타입변환이 어떻게 작용하는지 심층적으로 설명하며, 필수적인 지식으로 무장 시켜켜주므로, 다음과 같은 표현들이 어떤 연산을 하는지 설명하는 자신감을 느낄 수 있을 것이다. 기사가 끝날 때까지 나는 답을 보여주고 설명을 하겠다. 

```javascript
true + false
12 / "6"
"number" + 15 + 3
15 + 3 + "number"
[1] > null
"foo" + + "bar"
'true' == true
false == 'false'
null == ''
!!"false" == !!"true"
[‘x’] == ‘x’
[] + null + 1
[1,2,3] == [1,2,3]
{}+[]+{}+[1]
!+[]+[]+![]
new Date(0) - 0
new Date(0) + 0
```

그래 위의 코드들은 개발자로서 할 수 있는 꽤 어리석은 것(실수)들이다. 이 사례들의 90%에서는 암묵적인 형태의 타입 변환의 강제성을 피하는 것이 좋아. 위의 코드들은 타입 변환이 어떻게 작용하는지 당신의 지식을 시험하기 위한 학습으로 해봐. 지루하면 wtfjs.com에서 더 많은 예를 찾을 수 있어.

그런데 때때로 Javascript 개발자 직책에 대한 면접으로 타입 변환에 대해 질문을 받을 수 있으니 계속 읽어 😄

## 암묵적 vs. 명시적 형변환

타입 변환은 암묵적이거나 명시적일 수 있다. 

개발자가 ```Number(value)``` 처럼 적절한 코드를 작성하여 유형 간에 변환할 의도를 나타낼 때 *명시적 형변환* (또는 타입 캐스팅)이라고 부른다. 

자바스크립트는 타입에 느슨한 언어(weakly-typed language를 타입에 느슨한 언어라고 번역함)이기 때문에 값도 자동으로 다른 타입 간에 변환 될 수 있으며, 암묵적 변환이라고 한다. 연산자를 다른 타입의 값에 적용할 때 주로 발생한다. 

```1 == null```, ```2/'5'```, ```null + new Date()``` 또는 ```if (value) {…}``` 처럼 주변 컨텍스트에 의해 ```value```이 boolean으로 형변환이 일어난다. 

암묵적 형변환이 발생하지 않은 연산자는 엄격한 동등연산자로 불리는 ```===```이다. 반면 느슨한 동등연산자는 ```==```을 비교하고 필요하다면 타입형변환이 일어난다. 

암묵적 형변환은 double edge sword이다. 이것은 좌절과 결함의 훌륭한 원천이지만 또한 판별성을 잃지 않고 코드를 적게 쓸 수 있게 해주는데 유용한 메커니즘이다. 

## 3가지 변환 타입

자바스크립트에는 세가지 변환 타입이 있다는 것을 알야한다.

- to string
- to boolean
- to number

둘째로 원시타입과 object에 대한 변환 로직은 다르게 작용하지만, 원시타입과 object 모두 이 세가지 방법으로 변환될 수 있다.

원시 타입 부터 시작하자 !

## 문자열 변환
값을 문자열로 명시적으로 변환하려면 ```String()```을 사용한다. 암묵적 형변환은 다음과 같은 문자열일 때 이진 연산자 ```+```에 의해 일어난다.

```javascript
String(123) // explicit
123 + ''    // implicit
```
모든 원시 값은 예상대로 자연스럽게 문자열로 변환된다.

```javascript
String(123)                   // '123'
String(-12.3)                 // '-12.3'
String(null)                  // 'null'
String(undefined)             // 'undefined'
String(true)                  // 'true'
String(false)                 // 'false'
```
Symbol conversion is a bit tricky, because it can only be converted explicitly, but not implicitly. Read more on ```Symbol``` coercion rules.
Symbol 변환은 명시적으로 변환할 수 있을 뿐, 암묵적으로는 변환할 수 없기 때문에 약간 까다롭다.```Symbol``` 규칙에 관련한 것은 https://leanpub.com/understandinges6/read/#leanpub-auto-symbol-coercion 을 한번 읽어보라. 

```javascript
String(Symbol('my symbol'))   // 'Symbol(my symbol)'
'' + Symbol('my symbol')      // TypeError is thrown
```

## Boolean 변환
값을 Boolean으로 명시적으로 변환하려면 ```Boolean()```을 적용한다. 암시적 형변환은 논리적인 맥락에서 발생하거나, 논리연산자( ```||``` ```&&``` ```!```)에 의해 일어난다. 
```javascript
Boolean(2)          // explicit
if (2) { ... }      // implicit due to logical context
!!2                 // implicit due to logical operator
2 || 'hello'        // implicit due to logical operator
```

참고 : ```||``` 와 ```&&```과 같은 논리 연산자는 내부적으로 boolean 형변환을 하지만 실제로는 boolean이 아니더라도 원래의 피연산자의 값을 반환한다.

```javascript
// returns number 123, instead of returning true
// 'hello' and 123 are still coerced to boolean internally to calculate the expression
let x = 'hello' && 123;   // x === 123
```

boolean 변환의 결과가 ```true``` 또는 ```false```이 두가지 뿐이며, 거짓 같은 값들에 대한 목록을 쉽게 기억할 수 있다. 


```javascript
Boolean('')           // false
Boolean(0)            // false     
Boolean(-0)           // false
Boolean(NaN)          // false
Boolean(null)         // false
Boolean(undefined)    // false
Boolean(false)        // false
```
위의 코드들에 없는 값은 object, function, ```Array```, ```Date```, user-defined type 등 진짜 같은 ```true```로 반환한다. Symbol은 참 같은 값이이다. 빈 object와 array도 참 같은 값이다. 

```javascript
Boolean({})             // true
Boolean([])             // true
Boolean(Symbol())       // true
!!Symbol()              // true
Boolean(function() {})  // true
```

## 숫자형변환
명시적으로 변환하기 위해서는 ```Boolean()``` 와 ```String()```와 같이 ```Number()```을 사용한다. 

더 많은 케이스에서 일어나기 때문에 암시적 형변환은 까다롭다.

- 비교연산자들 (```>```, ```<```, ```<=```,```>=```)
- 비트연산자들 ( ```|``` ```&``` ```^``` ```~```)
- 산술 연산자들(```-``` ```+``` ```*``` ```/``` ```%``` ). 참고, 이진 연산자 +는 문자열을 만나면 숫자형변환이 일어나지 않는다. 
- unary ```+``` operator
- 느슨한 동등연산자 ```==``` (incl. ```!=```). 

참고 : ```==```은 두개의 비교가 문자열일 때 숫자 형변환은 일어나지 않는다.

```javascript
Number('123')   // explicit
+'123'          // implicit
123 != '456'    // implicit
4 > '5'         // implicit
5/null          // implicit
true | 0        // implicit
```
여기에 원시값에서 숫자로 변환하는 방법은 다음과 같다.
```javascript
Number(null)                   // 0
Number(undefined)              // NaN
Number(true)                   // 1
Number(false)                  // 0
Number(" 12 ")                 // 12
Number("-12.34")               // -12.34
Number("\n")                   // 0
Number(" 12s ")                // NaN
Number(123)                    // 123
```
문자열을 숫자로 변환할 때 엔진은 먼저 백스페이스, ```\n```, ```\t``` 문자열을 잘라서 잘라낸 문자열이 유효한 숫자를 나타내지 않으면 ```NaN```을 반환한다. 문자열이 비어 있으면 ```0```을 반환한다. 

```null``` and ```undefined```은 다르게 다뤄지는데, ```null``` 은 ```0```이 되고, ```undefined```은  ```NaN```이 된다.

Symbols은 명시적으로나 암묵적으로 숫자로 변환할 수 없다. 게다가 ```TypeError```는 ```undefined```처럼 ```NaN```으로 변환되어 진다. (Moreover, ```TypeError``` is thrown, instead of silently converting to ```NaN```, like it happens for ```undefined```.) 
Symbol의 변환의 규칙은 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#Symbol_type_conversions)에서 더 많이 나와 있다.

```javascript
Number(Symbol('my symbol'))    // TypeError is thrown
+Symbol('123')                 // TypeError is thrown
```

두가지 *특별한 룰*이 있다는 것을 기억하라. 
1.```null``` 또는 ```undefined```에 ```==```을 적용하면 숫자 변환은 일어나지 않는다. ```null```은 ```null``` 또는 ```undefined```에 해당하며 다른 어떤 것과 같지 않다. 

```javascript
null == 0               // false, null is not converted to 0
null == null            // true
undefined == undefined  // true
null == undefined       // true
```

2. ```NaN```은 그 자체와도 비교가 되지 않는다.
```javascript
if (value !== value) { console.log("we're dealing with NaN here") }
```

## object를 위한 타입 형변환
지금까지 원시값에 대한 타입 형변환을 살펴보았다. 별로 흥미롭지 않아.

object와 엔진이 ```[1] + [2,3]```과 같은 표현이 있을 때는 먼저 object을 원시값을 변환한 다음 최종 타입으로 변환해야한다. 그리고 여전히 숫자, 문자열, boolean의 세가지 변환 유형만 있다.

가장 간단한 경우는 boolean 형변환이다. 비 원시값은 항상 object이든 배열이든 간에 ```true```에 의해 변환이 된다.

object는 숫자 및 문자열 변환을 모두 담당하는 내부 ```[[ToPrimitive]]```을 통해 원시 상태로 변환된다. 

다음은 ```[[ToPrimitive]]```의 메소드에 의한 수도 코드이다. 

```javascript
function ToPrimitive(input, preferredType){
  
  switch (preferredType){
    case Number:
      return toNumber(input);
      break;
    case String:
      return toString(input);
      break
    default:
      return toNumber(input);  
  }
  
  function isPrimitive(value){
    return value !== Object(value);
  }

  function toString(){
    if (isPrimitive(input.toString())) return input.toString();
    if (isPrimitive(input.valueOf())) return input.valueOf();
    throw new TypeError();
  }

  function toNumber(){
    if (isPrimitive(input.valueOf())) return input.valueOf();
    if (isPrimitive(input.toString())) return input.toString();
    throw new TypeError();
  }
}
```
> js-to-primitive-internal.js

```[[ToPrimitive]]```은 input value과 preferred type은 ```Number``` 와 ```String```으로 값이 전달되며 preferredType은 조건에 optional한 부분이다.

숫자 형변환과 문자열 변환 모두 input ojbect의  ```valueOf```와 ```toString```이 두가지 메서드를 사용하는데, 두가지 메서드는 모두 ```Object.prototype```에 명시되어 있어 ```Date```, ```Array``` 등 파생된 타입에서 사용할 수 있다. 

아래는 일반적인 알고리즘이다. 

1. input이 이미 원시적이라면, 아무 것도 하지 않고 반환된다.

2. ```input.toString()```이 호출되면, 그 결과가 원시적이라면 반환한다.

3. ```input.valueOf()```이 호출되면, 그 결과가 원시적이라면 반환한다.

4. ```input.toString()``` 이나 ```input.valueOf()```이 원시적이지 않다면  ```TypeError```를 반환하라.

숫자 형변환은 우선 ```valueOf``` (3)을 호출하고, ```toString``` (2) 호출한다. 문자형변환은 ```toString``` (2)을 호출한 후 ```valueOf``` (3) 호출하여 숫자 변환과 문자형 변환은 반대이다. 

대부분의 빌트인된 타입은 ```valueOf```이 없거나 ```this```는 object를 반환하는 ```valueOf```가 없어서 원시적인 것이 아니므로 무시되고 잇다. 그렇기 때문에 숫자와 문자열 변환 모두 ```toString()```이라고 부르는 것으로 끝날 수 있다. 

다른 연산자는 ```preferredType```파라미터의 도움을 받아 숫자 또는 문자열 변환을 일으킬 수 있다. 그러나 ```==```와 이진 연산자```+```는 ```default``` 변환 상태를 일으키는 두가지 경우이다. 이 경우 문자열 변환을 하는 ```Date```을 제외하고 대부분의 내장된 타입은 숫자 변환을 default로 가정한다. 

```Date```의 변환의 예이다. 

```javascript
let d = new Date();

// get string representation
let str = d.toString();  // 'Wed Jan 17 2018 16:15:42'

// get numeric representation, num of milliseconds since Unix epoch
let num = d.valueOf();   // 1516198542525

// compare with a string representation
// true because d is converted to same string
console.log(d == str);   // true

// compare with numeric representation
// false, because d is not converted to a number via valueOf()
console.log(d == num);   // false

// Result is 'Wed Jan 17 2018 16:15:42Wed Jan 17 2018 16:15:42'
// '+' same to '==' triggers default conversion mode
console.log(d + d);

// Result is 0, since '-' operator explicitly triggers numeric conversion, not a default one
console.log(d - d);
```
> date-object-to-primitive-conversion.js

object에서 원시값으로 형변환 로직에 hook에 의해 ```toString()``` 과 ```valueOf()``` 메서드의 기본값을 override할 수 있다.

```javascript
var obj = {
  prop: 101,
  toString(){
    return 'Prop: ' + this.prop;
  },
  valueOf() {
    return this.prop;
  }
};

console.log(String(obj));  // 'Prop: 101'
console.log(obj + '')      // '101'
console.log(+obj);         //  101
console.log(obj > 100);    //  true
view raw
```
> es5-toprimitive-hooks.js
```obj + ‘’``` 가 어떻게  ```‘101’```을 문자열로 반환하는지 주목하라. ```+``` 연산자는 기본 변환 모드를 일으켜 앞서 말한 것처럼  ```Object```은 숫자 형변환을 디폴트로 가정하여 ```toString()```이 아닌 ```valueOf()``` 메서드를 먼저 사용한다.

## ES6의 Symbol.toPrimitive
ES5에서는 ```toString``` 와 ```valueOf``` 메서드 overriding에 의해 오브젝트에서 원시값으로 hook 할 수 있다.

ES6에서는 object에 ```[Symbol.toPrimtive]```메서드를 사용하면 내부 ```[[ToPrimitive]]```의 루틴을 완전히 대체하여 사용할 수 있습니다. 

```javascript
class Disk {
  constructor(capacity){
    this.capacity = capacity;
  }

  [Symbol.toPrimitive](hint){
    switch (hint) {
      case 'string':
        return 'Capacity: ' + this.capacity + ' bytes';

      case 'number':
        // convert to KiB
        return this.capacity / 1024;

      default:
        // assume numeric conversion as a default
        return this.capacity / 1024;
    }
  }
}

// 1MiB disk
let disk = new Disk(1024 * 1024);

console.log(String(disk))  // Capacity: 1048576 bytes
console.log(disk + '')     // '1024'
console.log(+disk);        // 1024
console.log(disk > 1000);  // true
view raw
```
> es6-symbol-toprimitive.js

## 예
이러한 이론이 적용되어 있는 다음의 예를 들어보자.
```javascript
true + false             // 1
12 / "6"                 // 2
"number" + 15 + 3        // 'number153'
15 + 3 + "number"        // '18number'
[1] > null               // true
"foo" + + "bar"          // 'fooNaN'
'true' == true           // false
false == 'false'         // false
null == ''               // false
!!"false" == !!"true"    // true
['x'] == 'x'             // true 
[] + null + 1            // 'null1'
[1,2,3] == [1,2,3]       // false
{}+[]+{}+[1]             // '0[object Object]1'
!+[]+[]+![]              // 'truefalse'
new Date(0) - 0          // 0
new Date(0) + 0          // 'Thu Jan 01 1970 02:00:00(EET)0'
```
아래에서는 각각의 표현에 대한 설명을 찾을 수 있습니다.

이진연산자 ```+```는 ```true```와 ```false```의 숫자 변환을 일으킨다.

```javascript
true + false
==> 1 + 0
==> 1
```
산술연산자 ```/```는 문자열 ```'6'``` 의 숫자 변환을 일으킨다.
```javascript
12 / '6'
==> 12 / 6
==>> 2
```
연산자 ```+```는  left-to-right으로 연관이 있기 때문에(왼쪽에서 오른쪽으로 연산을 진행한다 느낌...) ```"number" + 15```이 먼저 나온다.  한 피연산자는 문자열이기 때문에 ```+```연산자는 숫자 15에 대한 문자열 변환을 일으킨다. 두번째 단계 expression은 ```"number15" + 3```도 첫번째와 유사하게 비교가 된다.

```javascript
“number” + 15 + 3 
==> "number15" + 3 
==> "number153"
```

```15 + 3```이 먼저 비교가 된다. 두 피연산자는 모두 숫자인 만큼 강요할 필요는 전혀 없다. 두번째 단계 에서는 ```18 + 'number'```이 먼저 비교가 되며, 하나의 피연산자는 문자열이기 때문에 문자열 변환을 일으킨다. 

```javascript
15 + 3 + "number" 
==> 18 + "number" 
==> "18number"
```
비교 연산자 ```>```은 ```[1]``` 과 ```null```로 숫자 변환을 일으킨다.

```javascript
[1] > null
==> '1' > 0
==> 1 > 0
==> true
```

Unary ```+``` 연산자는 이진연산자  ```+```보다 우선순위가 높다. 그래서 ```+'bar'```가 먼저 비교가 된다. Unary ```+```는 문자열 ```'bar'```의 숫자 변환을 일으킨다.문자열이 유효한 숫자를 나타내지 못하기 때문에 결과는 ```NaN```이다. 두번째 단계에서는 ```'foo' + NaN```이 비교가 된다.

```javascript
"foo" + + "bar" 
==> "foo" + (+"bar") 
==> "foo" + NaN 
==> "fooNaN"
```
```==``` 은 숫자형 변환을 일으키고, 문자열 ```'true'```을 전부 NaN으로 변환되어 boolean ```true```은 1로 변환된다.

```javascript
'true' == true
==> NaN == 1
==> false
false == 'false'   
==> 0 == NaN
==> false
```

```==```은 보통 숫자 변환을 일으키지만,```null```의 경우는 그렇지 않다. ```null```은  ```null``` 또는 ```undefined``` 이고, 다른 어떤 것과도 같지 않다.

```javascript
null == ''
==> false
```

```!!``` 연산자는 ```'true'``` 와 ```'false'``` 두가지 문자열을 true로 변환하는데, 이는 비어 있지 않은 문자열이기 때문이다. 그러고나서 ```==```은 ```true```라는 두 개의 boolean은 아무런 형변환없이 같은지만 체크한다. 

```javascript
!!"false" == !!"true"  
==> true == true
==> true
```

```==``` 연산자는 배열의 숫자 변환을 일으킨다. 배열의 ```valueOf()``` 메소드는 배열 자체를 반환하여 원시적인 것이 아니기 때문에 무시되고 있다. 배열의 ```toString()``` 은 ```['x']``` 을 ```'x'``` 문자열로 변환한다.

```javascript
['x'] == 'x'  
==> 'x' == 'x'
==>  true
```

```+``` 연산자는 ```[]```에 대한 숫자 변환을 일으킨다. 배열의 ```valueOf()``` 메서드는 기본이 아닌 배열 자체를 반환하기 때문에 무시되고 있다. 배열의 ```toString```은 빈 문자열을 반환한다.

두번째 단계에서는 ```'' + null + 1```으로 비교가 된다.

```javascript
[] + null + 1  
==>  '' + null + 1  
==>  'null' + 1  
==> 'null1'
```

논리적인 ```||``` 와 ```&&``` 연산자는 boolean으로 형변환이 되지만 원래의 피연산자는 반환하지 않도록 한다.  ```0```은 거짓 같은 값인 반면 ```'0'```은 참 같은 값이다. 왜냐하면 빈 문자열이 아니기 때문이다. ```{}```의 빈 object은 역시 참 같은 값이다.


```javascript
0 || "0" && {}  
==>  (0 || "0") && {}
==> (false || true) && true  // internally
==> "0" && {}
==> true && true             // internally
==> {}
```

두 피연산자의 타입이 동일하기 때문에 형변환은 필요하지 않는다. ```==```은 object의 동일성이 아니라 object의 identity을(my think's ... object의 identity란 무엇일까...) 확인하는 것과 두 배열이 서로 다른 경우이기 때문에 그 결과는  ```false```이다. 

```javascript
[1,2,3] == [1,2,3]
==>  false
```
모든 피연산자는 원시값이 아니므로 ```+```는 가장 왼쪽에 있는 숫자 변환으로 부터 시작한다. ```Object’s``` 와 ```Array’s```의 ```valueOf``` 메서드는 object자체를 반환하기 때문에 무시되고 있다. ```toString()```은 fallback으로 사용된다. 여기서 trick은 첫번째로 ```{}```은 문자 그대로가 아니라 블록 선언문처럼 여겨져 무시된다는 것이다. 그래서 비교는 ```toString()``` 메소드를 통해 빈 문자열로 변환된 다음 ```+[]``` 식에서 시작되어 ```0```으로 변환된다.

```javascript
{}+[]+{}+[1]
==> +[]+{}+[1]
==> 0 + {} + [1]
==> 0 + '[object Object]' + [1]
==> '0[object Object]' + [1]
==> '0[object Object]' + '1'
==> '0[object Object]1'
```

이것은 연산자 우선 순위에 따라 차근차근 설명하는 것이 좋다.

```javascript
!+[]+[]+![]  
==> (!+[]) + [] + (![])
==> !0 + [] + false
==> true + [] + false
==> true + '' + false
==> 'truefalse'
```

```-``` 연산자는 숫자 형변환을 일으킨다. ```Date```. ```Date.valueOf()```은 유닉스 이후 수 밀리초를 반환한다.

```javascript
new Date(0) - 0
==> 0 - 0
==> 0
```

```+``` 연산자는 기본적인 변환을 일으키고, Date는 문자열 형변환을 기본으로 가정하기 때문에 ```valueOf()``` 가 아니라 ```toString()``` 메서드가 사용된다.

```javascript
new Date(0) + 0
==> 'Thu Jan 01 1970 02:00:00 GMT+0200 (EET)' + 0
==> 'Thu Jan 01 1970 02:00:00 GMT+0200 (EET)0'
```

## 참고 자료
 
Nicholas C. Zakas가 쓴 [Understanding ES6](https://leanpub.com/understandinges6) 책이 가장 잘 쓰여졌다고 추천한다. 이 책은 너무 높은 레벨이 아니라 훌륭한 ES6 학습 자료로 내실을 너무 많이 파고 들지 않는다. 

Axel Rauschmayer이 쓴 [SpeakingJS](http://speakingjs.com/)은 ES5를 익히기에 좋다.

(Russian) Современный учебник Javascript — https://learn.javascript.ru/. 이 것은 타입 형변환에 대한 두 페이지가 있다.

JavaScript Comparison Table — https://dorey.github.io/JavaScript-Equality-Table/

wtfjs — 작은 코드들을 제공함 — https://wtfjs.com/