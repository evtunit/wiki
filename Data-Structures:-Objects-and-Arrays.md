# Data Structures: Objects and Arrays

번역 : [https://scotch.io/courses/10-need-to-know-javascript-concepts/data-structures-objects-and-arrays#toc-what-is-an-object-](https://scotch.io/courses/10-need-to-know-javascript-concepts/data-structures-objects-and-arrays#toc-what-is-an-object-)

JavaScript에서 대괄호와 중괄호로 된 데이터를 본 적이 있습니까? 이들은 각각 배열과 객체입니다.

```js
// arrays
[]

// objects
{}
```

JavaScript로 객체와 배열을 논의 할 것입니다. 다음과 같이 JavaScript에서 데이터 유형을 알고있을 것입니다.

-   String
-   Number
-   Boolean
-   Null
-   Undefined

객체와 배열은 다르지 않지만 좀 더 복잡한 데이터 구조를 가지고 있습니다. 그것들은 JavaScript에서 다른 데이터 유형의 컨테이너로 시각화 될 수 있으므로 데이터 구조라는 용어입니다.

## What is an Object?

JavaScript 객체는 알고있는 자연 객체와 유사합니다. 예를 들어 컵, 집, 자동차 등

이들 객체가 할수 있는 것들:

- 몇가지를 포함
- 특별한 특징을 지님
- 다른 객체를 포함할 수 있음
- 몇가지 목적을 위해 사용할 수있음
- 그들에게 행해진 몇 가지 행동을 받아 들일 수있다

예를 들어, 컵에 작은 컵이있을 수 있으며 컵에서 물을 마시고 컵을 깨고 자동차를 운전하거나 어린이 또는 장난감 자동차를 자동차에 넣고 목록이 계속 될 수 있습니다.

JavaScript 객체는 다르지 않으며 다음과 같습니다.

```js
const myObject = {};
```

중괄호는 개체를 나타내며 개체의 요소가 개체를 구성합니다. 객체 값은 **키 : 값** 쌍으로 제공되며 이러한 값은 객체의 **속성** 또는 **방법 (함수)** 일 수 있습니다. 속성은 객체의 특징 또는 속성 인 반면, 방법은 객체에서 수행 할 수있는 기능 또는 동작입니다.

참고 : 객체가 객체를 사용하거나 인식하려면 객체에서 객체 메소드를 정의해야합니다.

객체는 다음과 같이 나타납니다.

```js
const myObject = {
    myKey1: 'ObjectValue1',
    myKey2: 'ObjectValue2',
    mykeyN: 'ObjectValueN',
    objectMethod: function() {
      // Do something on this object
    }
};
```

기본적으로 **JavaScript의 거의 모든 것이 Object**입니다. 이러한 이유로 많은 사람들이 JavaScript를 객체 지향 언어로 간주합니다. 브라우저에서 윈도우가 로드되면 `Document` 개체의 인스턴스가 생성되고 브라우저에 표시된 다른 모든 것은 문서 개체의 자식이며 문서 메서드는 부모 개체를 조작하는 데 사용됩니다.

객체를 중첩 할 수도 있습니다.

```js
const myObject = {
  first:  { key:  'thing', key2: 2 },
  second: { key3: 'otherThing' }, 
  third:  { key4: 'my string' }
};
```

## Everything is an Object

모든 것이 객체라고 말하면 JavaScript는 모든 유형을 해당 유형에 대한 메소드를 제공하는 객체로 래핑합니다. 이를 명확히하기 위해 다음 코드를 살펴 보겠습니다.

```js
// create a message with a string value
const myMessage = 'look at me go!';

// convert to uppercase
myMessage.toUpperCase();
```

이 문자열에서 어떻게 `.toUpperCase ()` 메소드를 호출 할 수 있었습니까? 
우리는 일반적으로 객체에 대해서만 메소드를 호출 할 수 있습니다. 문자열을 만들 때 JavaScript는 실제로 해당 문자열을 string 유형의 객체로 래핑합니다. 다음은 동일합니다

```js
// simple way to create a string
const myMessage = 'look at me go!';

// what javascript sees
const myOtherMessage = String('look at me go!');

myMessage == myOtherMessage; // true
```

> 이에 대한 자세한 내용은 JavaScript 객체에 대한 비디오를 확인하십시오. [웹 개발을위한 JavaScript 시작 : 객체] (https://scotch.io/courses/getting-started-with-javascript/objects)

## Creating Objects

객체를 생성하는 몇 가지 방법이 있습니다 :

### Using Object Initializers

이는 키 : 값 쌍과 메소드를 중괄호 안에 넣어서 위에 표시된대로 수행됩니다. 이것을 문자 표기법이라고도합니다.

```js
const obj = {
    firstName: 'William',
    lastName:  'Imoh',
    married:   true,
    greet:     function() {
      alert("Hello everyone, i'm " + this.firstName);
    }
};
```

위의 코드에서 다음을 호출하여 이러한 속성과 메서드에 액세스 할 수 있습니다.

```js
obj.firstName;   // William
obj.lastName;    // Imoh
obj.greet();     // Will alert, Hello everyone, i'm William 
```

왜 우리가 `obj.firstName` 대신에 이것을 `this` 를 사용했는지 궁금 할 것입니다. 자, `this` 는 그것이 호출되는 부모 객체를 나타내는 대명사와 같은 역할을합니다. 다음 강의에서 이에 대해 더 배울 것입니다.

### Using Object Constructor

`new` 키워드를 사용하여 객체 인스턴스를 생성합니다.

```js
const house     = new Object();

house.color     = 'red';
house.houseType = 'bungalow';
house.location  = 'Lagos';
```

우리가 한 것은 객체 인스턴스를 생성하고 변수 하우스에 할당 한 다음 속성과 개별 값을 편집하는 것입니다.

또한 함수 생성자를 사용하여 객체 또는 여러 개의 유사한 객체를보다 쉽게 ​​만들 수 있습니다.

```js
function University(name, location, size) {
  this.name     = name;
  this.location = location;
  this.size     = size;
}

const theUniversity = new University('caritas', 'enugu', 20000);

theUniversity.name;     // caritas
theUniversity.size;     // 20000
```

여기서 함수는 인수로 구성되었습니다. 함수 이름은 대문자로되어 있으며 생성자 함수에 대한 규칙입니다. 이 함수에는 인수가 전달되며이 인수는 `this.property` 에 의해 정의 된 객체 속성의 기본 값입니다.

새 키워드는 함수를 호출하고 함수에 정의 된 매개 변수와 일치하는 매개 변수를 전달하여 새 키워드로 구성됩니다. 이것을 실행하십시오.

```js
console.log(theUniversity)
```

그리고 우리는 이것을 얻습니다 :

```js
University {
  name:     'caritas',
  location: 'enugu',
  size:     '20000'
}
```

## Accessing an Object

다음과 같은 두 가지 방법으로 객체의 속성에 액세스 할 수 있습니다.

### Dot notation

점 표기법에서 객체의 속성 값은 다음과 같이 액세스됩니다.

```js
const objectName = {
  objectProp: 'my super duper value',
};

objectName.objectProp; // my super duper value
```

### Square Bracket Notation

대괄호 표기법은 점 표기법의 대안이며 다음과 같이 작성됩니다.

```js
const objectName = {
  objectProp: 'super cool yo!',
};

objectName['objectProp']; // super cool yo!
```

속성 이름이 대시 나 숫자와 같은 특수 문자로 시작하거나 변수를 키로 사용하려는 경우 대괄호 표기법 만 사용할 수 있습니다.

현대적인 프로그래밍 언어가 시작된 이래로 for-loop를 사용하여 데이터 세트를 순회하는 것은 많은 비용이 들지 않았으며 Objects에는 객체 속성을 순회하거나 반복하는 여러 가지 방법이 있습니다.

-   **for…in loop**: 이것은 가장 일반적인 것으로 객체의 열거 가능한 각 속성과 프로토 타입 체인을 반복하는 데 사용됩니다.
-   **Object.keys(o)**: object.keys (o) 메소드는 객체 o를 순회하는 데 사용되며 열거 가능한 모든 속성 이름이 포함 된 배열을 반환합니다.
-   **Object.getOwnPropertyNames(o)**: 덜 알려진 방법은 객체 o를 반복하고 열거 가능한 객체의 모든 자연 속성 이름 배열을 반환합니다.

## Arrays

배열은 특별한 종류의 JavaScript 객체입니다.

```js
const a = [];

typeof a; // returns object
```

배열은 여러 변수, 즉 300 마리의 닭을 취하는 데이터 구조입니다! 객체 데이터 구조와 달리 키 : 값 쌍은 없지만 단수 데이터가 있습니다. 배열을 어떻게 식별합니까? 다음과 같이 보입니다 :

```js
const myArray = ['hi', 'i', 'am', 'chief'];
```

배열의 데이터는 대괄호로 묶고 원하는만큼의 변수와 다른 배열을 포함 할 수 있습니다. 배열은 객체이며 객체도 포함 할 수 있습니다.

```js
const myArray = [
  { name: 'chris', location: 'racoon city' }
];
```

## Creating Arrays

배열은 두 가지 방식으로 생성됩니다.

### Array Literal

배열 리터럴을 사용하는 것이 배열을 만드는 가장 일반적인 방법입니다. 배열 값은 대괄호로 묶습니다.

```js
const arr = [];
const bar = ['drinks', 'music', 'dance', 'beer', 'more beer'];

// an array of objects
const coocoo = [
  { key:  'thing', key2: 2 },
  { key3: 'otherThing' }, 
  { key4: 'my string' }
];
```

### Array Constructor

이것은 새로운 키워드를 사용하여 배열을 만드는 또 다른 방법입니다. 스크립트에서 예기치 않은 동작을 일으킬 수 있으므로 많은 사람들이 나쁜 습관으로 간주합니다. 배열 값은 생성자에 인수로 전달됩니다.

```js
const bar = new Array('beer', 'music', 'beer', 'more beer');

console.log(bar);  // ['beer', 'music', 'beer', 'more beer']
```

다음과 같은 경우 :

```js
const fig = new Array(10, 20);

console.log(fig);   // [10, 20]
```

그림이 제거되면 어떻게 되나요? 우리는 :

```js
const fig = new Array(10);

console.log(fig);   // returns 10 empty arrays!
```

우리는 10 개의 빈 배열을 얻습니다! 배열 리터럴 []을 사용하면 읽기가 쉽고 쓰기가 적으며 예상대로 작동하므로 항상 권장됩니다.

## Accessing Arrays

객체와 달리 배열 값은 0부터 시작하는 색인 ​​위치를 사용하여 액세스합니다 (배열은 0으로 색인화 됨). **객체는 문자 인덱스를 사용하는 반면 배열은 숫자 인덱스를 사용합니다.** 배열은 다음과 같이 액세스됩니다 :

```js
const myArr = [10, 40, 50, 70, 20, 2, 100];

myArr[0] // 10
myArr[3] // 70
myArr[7] // 100
```

`arr.firstNumber` 와 같은 배열에서 문자 색인을 사용하려고하면 오류 메시지가 발생합니다.

### Array Methods

객체와 마찬가지로 배열에도 조작에 사용할 수있는 메소드가 있습니다. 이러한 메소드는 내장되거나 구성 될 수 있습니다. 여러 배열 방법이 있으며 [여기](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) 에서 찾을 수 있습니다.

```js
const fig = [2, 5, 10, 30, 20];

arr.length;   // returns the length of the array = 5
```

for 루프를 사용하여 배열을 반복하는 것은 매우 원활합니다. 위의 예를 사용하면

```js
const fig = [2, 5, 10, 30, 20];

for (let i = 0; i < fig.length; i++) {
  console.log(fig[i]);
}

// 2
// 5
// 10
// 30
// 20
```

다른 배열 방법은 다음과 같습니다.

-   `pop()`: 배열에서 마지막 요소를 제거하고 해당 요소를 반환
-   `slice()`: 배열 부분의 얕은 복사본을 새로운 배열로 반환
-   `shift()`: 배열에서 첫 번째 요소를 제거하고 해당 요소를 반환
-   `unshift()`: 배열의 시작 부분에 하나 이상의 요소를 추가하고 배열의 새 길이를 반환

배열 작업을 할 때는 항상 내장 메서드를 살펴보십시오. 일반적으로 필요한 것을 찾을 수 있습니다.

배열 메서드는 ** mutator 메서드 **, ** accessor 메서드 ** 및 ** iteration 메서드 **로 그룹화 할 수 있습니다. 뮤 테이터 메소드는 실제로 배열을 변경하거나 수정하는 메소드입니다. 예를 들어 `array.push ()` 메소드는 지정된 요소를 배열에 추가하고 수정 된 배열을 반환합니다.

```js
const fig = [10, 20, 30, 40];

fig.push(80);
console.log(fig);    // [10, 20, 30, 40, 80]
```

접근 자 메소드는 배열을 변경하지 않고 메소드의 효과에 따라 배열의 이미지를 작성하는 메소드입니다 `예 : array.slice ()`

```js
const fig = [10, 20, 30, 40];
const b   = fig.slice(1);

console.log(b);      // [20, 30, 40]
console.log(fig);    // [10, 20, 30, 40]
```

이름에서 알 수 있듯이 반복 메소드는 배열의 길이를 샘플링하고 메소드에 정의 된 콜백 함수로 배열의 각 요소를 평가하는 동안 배열을 반복하는 데 사용됩니다. 여기에는 `.forEach ()`및`.map ()` 메소드가 포함됩니다.

```js
const fig = [10, 20, 30, 40];

const newFig = fig.map(function(val) { //calls a function on each element of the array
  return val + 10;
});

console.log(newFig) // [20,30,40,50]
```

마지막으로 배열에는 길이를 포함하는 속성이 있으며 배열에는 객체가 만들어 질 수 있습니다.

## Conclusion

우리는 객체가 무엇인지, 어떻게 만들 수 있는지, 속성과 값을 처리하여 특수하게 만드는 이유를 명확하게 이해합니다. 객체에는 데이터베이스 아키텍처에서 서버 응답에 이르기까지 다양한 응용 프로그램이 있습니다.

반면에 배열은 컨테이너와 같은 데이터 모음입니다. 그것의 사용 또한 광대하다. 배열과 객체 모두에 사용할 수있는 수많은 방법으로 자유롭게 놀아보십시오. 항상 손에 닿는다는 것을 항상 기억하십시오. 여러 번 객체의 배열을 만들 것입니다. 예를 들어, 사용자 개체 목록이있을 수 있습니다.

