> 원본: http://javascript.info/map-set

아래와 같은 복잡한 데이터 구조에 대해 알아보도록 하자.

- Key 집합을 저장하는 Object
- Array 집합

위의 메서드로는 코딩하기 어렵다. 그래서 오늘 배워볼 주제는 `Map`과 `Set`이다.

<br>

## Map
`Map`은 `Object`처럼 키로 연결된 데이터 항목의 집합이다. 그러나 주요 차이점은 `Map`이 모든 종류의 키를 허용한다는 것이다.

메소드와 프로퍼티는 다음과 같다.

- `new Map()` – 맵을 생성한다.
- `map.set(key, value)` – key와 value값을 저장한다.
- `map.get(key)` – key가 map에 없는 경우 undefined로 값을 리턴한다.
- `map.has(key)` – key가 있으면 true를 반환하고 그렇지 않으면 false를 리턴한다.
- `map.delete(key)` – key값을 삭제한다.
- `map.clear()` – map을 돌며 모두 삭제한다.
- `map.size` – 최근 사용된 엘리먼트의 갯수를 리턴한다.

<br>

예를들어: 

```js
let map = new Map();

map.set('1', 'str1');   // a string key
map.set(1, 'num1');     // a numeric key
map.set(true, 'bool1'); // a boolean key

// remember the regular Object? it would convert keys to string
// Map keeps the type, so these two are different:
alert( map.get(1)   ); // 'num1'
alert( map.get('1') ); // 'str1'

alert( map.size ); // 3
```

<br>

보시다시피, Object와 달리 key는 문자열로 변환되지 않는다. 모든 유형의 key가 가능합니다.

**map은 Object를 key로 사용할 수도 있다.**

예를 들어:

<br>

```js
let john = { name: "John" };

// for every user, let's store their visits count
let visitsCountMap = new Map();

// john is the key for the map
visitsCountMap.set(john, 123);

alert( visitsCountMap.get(john) ); // 123
```

<br>

Object를 key로 사용하는 것은 가장 눈에 띄고 중요한 map 기능 중 하나이다. 문자열 키의 경우 개체는 정상일 수 있지만 개체 키는 정상일 수 없다.

<br>

```js
let john = { name: "John" };

let visitsCountObj = {}; // try to use an object

visitsCountObj[john] = 123; // try to use john object as the key

// That's what got written!
alert( visitsCountObj["[object Object]"] ); // 123
```

<br>

`visitsCountObj`는 객체이기 때문에 `john`과 같은 모든 `key`를 문자열로 변환하므로 문자열 키 "`[object Object]`"라고 노출하는데 이 alert값을 우리가 원한 결과가 아니다.

<br>

### 맵이 키를 비교하는 방법

> 동등성을 테스트하기 위해 맵은 [SameValueZero](https://tc39.es/ecma262/#sec-samevaluezero) 알고리즘을 사용한다. 엄격한 평등 `===` 과 동일하지만, 차이점은 `NaN`이 `NaN`과 동등하다고 여겨진다는 것이다. 그래서 `NaN`도 key로 활용할 수 있다. 이 알고리즘은 변경하거나 사용자 정의할 수 없다.

<br>

### Chaining

모든 `map.set`은 map 자체를 반환하므로, 다음 호출을 "체인"할 수 있다.

```js
map.set('1', 'str1')
  .set(1, 'num1')
  .set(true, 'bool1');
```

<br>

## Iteration over Map

map에서 루프를 도는 방법은 3가지가 있다.

- `map.keys()` – key를 리턴한다.
- `map.values()` – value를 리턴한다. 
- `map.entries()` – [key, value] 항목에 대해 허용 가능한 값을 리턴한다. 이 값은 기본적으로 `for..of`에서 사용된다.

<br>

예를들어:

```js


let recipeMap = new Map([
  ['cucumber', 500],
  ['tomatoes', 350],
  ['onion',    50]
]);

// iterate over keys (vegetables)
for (let vegetable of recipeMap.keys()) {
  alert(vegetable); // cucumber, tomatoes, onion
}

// iterate over values (amounts)
for (let amount of recipeMap.values()) {
  alert(amount); // 500, 350, 50
}

// iterate over [key, value] entries
for (let entry of recipeMap) { // the same as of recipeMap.entries()
  alert(entry); // cucumber,500 (and so on)
}
```

<br>

> 반복은 값이 삽입된 순서대로 진행된다. map은 일반 Object와 달리 이 순서를 유지한다.

```js
// runs the function for each (key, value) pair
recipeMap.forEach( (value, key, map) => {
  alert(`${key}: ${value}`); // cucumber: 500 etc
});
```

<br>

## Object.entries: Map from Object

map이 생성되면 다음과 같이 초기화를 위해 키/값 쌍으로 배열(또는 다른 허용 가능)을 전달할 수 있다.

<br>

```js
// array of [key, value] pairs
let map = new Map([
  ['1',  'str1'],
  [1,    'num1'],
  [true, 'bool1']
]);

alert( map.get('1') ); // str1
```

<br>

일반 개체가 있고 여기서  map을 작성하려면 개체의 키/값 쌍 배열을 해당 형식으로 정확하게 반환하는 기본 제공 방법 Object.entries(obj)를 사용할 수 있다.

이와 같은 객체로부터 지도를 만들 수 있다.

<br>

```js
let obj = {
  name: "John",
  age: 30
};

let map = new Map(Object.entries(obj));

alert( map.get('name') ); // John
```

<br>

여기서 Object.entries는 ["name","John", ["age", 30] 등의 키/값 쌍의 배열을 반환한다.

<br>

## Object.fromEntries: Object from Map

`Object.entries(obj)`를 사용하여 일반 개체에서 `map`을 만드는 방법을 방금 살펴보았다.

`Object.fromEntries`는 `[key, value]` 쌍의 배열을 지정하면 다음과 같은 방법으로 객체를 생성한다.

<br>

```js
let prices = Object.fromEntries([
  ['banana', 1],
  ['orange', 2],
  ['meat', 4]
]);

// now prices = { banana: 1, orange: 2, meat: 4 }

alert(prices.orange); // 2
```

<br>

`Object.fromEntries`를 사용하여 `map`에서 일반 객체를 가져올 수 있다.

예를 들어, 우리는 데이터를 지도에 저장하지만, 일반 객체를 예상하는 제3자 코드로 데이터를 전달해야 한다.

<br>

예를들어:

```js
let map = new Map();
map.set('banana', 1);
map.set('orange', 2);
map.set('meat', 4);

let obj = Object.fromEntries(map.entries()); // make a plain object (*)

// done!
// obj = { banana: 1, orange: 2, meat: 4 }

alert(obj.orange); // 2
```

<br>

`call to map.entries()`는 `Object.fromEntries`에 대한 올바른 형식으로 키/값 쌍의 배열을 반환한다.

line(*)을 더 짧게 만들 수도 있다.

<br>

```js
let obj = Object.fromEntries(map); // omit .entries()
```

<br>

마찬가지로 `Object.fromEntries`도 인수로 허용 가능한 객체를 예상하기 때문이다. 반드시 배열은 아니다.

또한 `map`의 표준 반복은 `map.entries()`와 동일한 키/값 쌍을 반환한다. 그래서 지도와 같은 키/값을 가진 평범한 물체를 얻을 수 있다.

<br>

## Set

`set`은 각 값이 한 번만 발생할 수 있는 특수 유형 컬렉이다. 참고로 key가 존재하지 않는다.

- `new set(iterable)` – 세트를 작성하고, 허용 가능한 개체(일반적으로 배열)가 제공된 경우, 해당 개체에서 세트로 값을 복사한다.
- `set.add(value)` – 값을 추가하고 set 자체를 반환한다.
- `set.delete(value)` – 값을 삭제하고, 호출 순간에 값이 존재하면 true를 반환하고, 그렇지 않으면 false를 반환한다.
- `set.has(value)` – 값이 set에 있으면 true를 반환하고 그렇지 않으면 false를 반환한다.
- `set.clear()` – 집합에서 모든 것을 제거한다.
- `set.size` – 요소 개수.

기본 기능은 동일한 값을 가진 `set.add(value)`의 반복 호출은 아무 것도 수행하지 않는다는 것이다. 각 값이 set에 한 번만 나타나는 이유이다.

예를 들어, 방문객들이 오고, 우리는 모든 사람들을 기억하기를 원한다. 

하지만 반복적인 방문이 중복으로 이어지면 안 된다. 방문자는 한 번만 "count"해야 한다.

<br>

```js
let set = new Set();

let john = { name: "John" };
let pete = { name: "Pete" };
let mary = { name: "Mary" };

// visits, some users come multiple times
set.add(john);
set.add(pete);
set.add(mary);
set.add(john);
set.add(mary);

// set keeps only unique values
alert( set.size ); // 3

for (let user of set) {
  alert(user.name); // John (then Pete and Mary)
}
```

<br>

집합의 대안은 사용자 배열과 `arr.find`를 사용하여 삽입할 때마다 중복 항목을 확인하는 코드일 수 있다.

하지만 성능은 훨씬 더 나쁠 것이다. 이 방법은 전체 `array`를 통해 모든 요소를 검사하기 때문이다. 설정은 고유성 검사를 위해 내부적으로 훨씬 더 잘 최적화 되어있다.

<br>

## Iteration over Set

루프를 사용하기 위해서는 `for..of` 혹은 `forEach`를 사용해야한다.

<br>

```js
let set = new Set(["oranges", "apples", "bananas"]);

for (let value of set) alert(value);

// the same with forEach:
set.forEach((value, valueAgain, set) => {
  alert(value);
});
```

<br>

`forEach`에 전달된 콜백 함수에는 값, 그 다음 동일한 `valueAgain`, 그리고 대상 개체의 세 가지 인수가 있다. 실제로 동일한 값이 인수에 두 번 나타난다.

이는 callback이 전달된 Map과의 호환성을 위한 것이다. 각에는 세 개의 인수가 있다.

그러나 어떤 경우에는 map을 쉽게 Set로 교체하거나 그 반대로 교체하는 데 도움이 될 수 있다.

map이 반복기에 대해 가지는 것과 동일한 방법도 지원된다.

- `set.keys()` – 값에 대해 허용 가능한 개체를 반환
- `set.values()` – set.keys()와 동일하며, 지도와의 호환성
- `set.entries()` – [value, value] 항목에 대해 허용 가능한 개체를 반환하여 map과 호환