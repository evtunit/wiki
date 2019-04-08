![](https://cdn-images-1.medium.com/max/1200/1*FYYeO7ZaS9bqe0D29vF5XA.jpeg)
값 vs 참조의 개념을 살펴 보자.  
모든 자바스크립트 개발자는 이 주제가 오늘날의 애플리케이션에서 버그의 원인이 되기 때문에 알아야한다.  
또한 취업 면접 질문 중 하나입니다.  

나는 이 짧은 글로 쉽게 기초를 다룰 수 있다.  

너무 빨리 아래로 스크롤 하지 마라.  
이 두 예제가 반환하는 것을 알고 있습니까?  

````
console.log([10] === [10]);

var oldArray = [];
var object = {};
object.newArray = oldArray;
oldArray.push(10);
console.log(object.newArray === oldArray);
````
첫번째 것은 거짓이고 두번째 것은 사실입니다.
당신의 대답이 맞습니까? 왜 그런지 살펴보자  

자바스크립트에는 값으로 복사 된 유형과 참조로 복사 한 유형이 있다.  
이것들은  
### 원시적인 (값의 복사)
* null
* undefined
* Number
* String
* Boolean

### 객체 (참조로 복사)
* Object
* Array
* Function

## Primitives
````
var a = 5;
var b = a;
a = 10;
console.log(a); // 10
console.log(b); // 5
// this is also true for string, boolean, null, undefined
````
변수에 Primitive를 할당 할 때 값을 복사한다.  

## Objects
이제, 혼란스러운 부분
````
var a = {};
var b = a;
a.a = 1;
console.log(a); // {a: 1}
console.log(b); // {a: 1}
````
배열에 대해서도 마찬가지입니다.
````
var a = [];
var b = a;
a.push(1);
console.log(a); // [1]
console.log(b); // [1]
console.log(a === b); // true
````
변수에 객체를 할당할 때 참조로 복사합니다.  
변수를 선언 함으로써 메모리에 새로운 주소를 생성한다는 것을 상상할 수 있습니다.  
변수 b를 선언하면 이 주소로 연결됩니다.  
따라서 이 주소의 내용을 업데이트 할 때 변수 a와 b는 모두 같은 값을 갖습니다.  
````
var a = [];     # Address #001 -> []
                # Variable a -> #001
var b = a;      # Variable b -> #001
a.push(1);      # Address #001 -> [1]
Variable | Address | Value
a        | #011    | [1]
b        | #011    | [1]
````

### 이제 [10] === [10] 에 대해
객체를 비교할 때 항등 연산자(===) 는 동일한 주소를 가리키는지 확인합니다.  
따라서 [10] 과 [10] 이 두 개의 다른 배열인 경우 결과는 마지막 예제에서와 같이 거짓입니다.  
두 개의 배열 또는 객체의 값을 비교하려는 경우 간단하지만 제한된 해결책이 있습니다.  
````
JSON.stringify(a) === JSON.stringify(b)
````
두 객체/배열 의 속성이 같은 순서가 아니면 false를 반환합니다.  
보다 견고한 해결책을 원할 경우 lodash_.isEqual() 메소드를 사용할 수 있습니다.  
또는 이 스택오버플로우 대답에서 설명한대로 자신의 솔루션을 구현하세요.  
