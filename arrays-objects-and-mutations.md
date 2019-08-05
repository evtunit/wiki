
# 배열과 객체 그리고 변이

> 원문: [Arrays, Objects and Mutations](https://medium.com/@fknussel/arrays-objects-and-mutations-6b23348b54aa)

Javascript에서 배열 및 객체로 작업할 때 불변한(*immutable*) 데이터 구조인 것 처럼 처리하여, 변이를 막는 몇가지 방법에 대해 소개한다.

## 변이란 무엇인가?

불변의 값(*imutable value*)은 한번 만들어 진 다음엔 변경이 되지 않는 값을 말한다. 자바스크립트에선 숫자, 문자열, 불리언과 같은 원시 값이들이 항상 불변 인 값이다. 그러나 배열과 객체 같은 데이터구조는 그렇지 않다.

변이란 원본 요소를 변경하거나 영향을 주는 것을 의미한다. 우리의 목표는 **원래 요소를 항상 변경되지 않게 하는 것**이다.

> 변이는 부작용이다: 프로그램에서 변하는 것이 적을수록 추적해야 할 것이 적어지기 때문에, 프로그램이 더 간단해진다.

원본 요소에 대한 변이를 발생시키는 메소드를 ❌로, 불변한 메소드는 ✅로 표시했다.


## 배열에 새로운 요소 추가하기

-   `Array.prototype.push`  ❌
-   `Array.prototype.unshift`  ❌
-   `Array.prototype.concat` ✅
-   Spread Operator (ES6)  ✅

`[Array.prototype.push](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/push)`는 배열의 마지막에 새로운 요소를 추가하게 한다. 이 메소드는 새로운 사본을 반환하지 않고, 새 요소를 추가하여 원래 배열을 변경하고 새로운 `length` 속성을 가진 배열을 반환한다.

```js
const numbers = [1, 2];
numbers.push(3); // results in numbers being [1, 2, 3]
```

`[Array.prototype.unshift](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)`는 배열의 앞부분에 새로운 요소를 추가할 때 사용한다. 즉 `push`, `unshift`는 수정 된 배열의 새 복사본을 반환하지 않고, 새로운 `length`를 가진 배열을 반환한다.

```js
const numbers = [2, 3];
numbers.unshift(1); // results in numbers being [1, 2, 3]
```

이 두 가지 예제들은 원래의 배열을 변경하고 있다. **원본 배열을 변경하지 않고 유지하는 것이 목표**라는 점을 기억해라. 원본을 변경하지 않고 배열에 새 요소를 추가하는 몇 가지 대안을 살펴보자.

아마도 가장 쉬운 방법은 아래와 같이 원본 배열의 복사본을 생성하고 직접 추가하는 것일 것이다.

```js
const numbers = [1, 2];  
const moreNumbers = numbers.slice();

moreNumbers.push(3);
```

우리는 이것을 "통제된 변이"라고 부를 수 있을 것이다. 원본이 아닌 복사본을 가지고 실제로 배열의 변이를 하고 있기 때문이다.
그러나 `[Array.prototype.slice](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)`는 **원본 배열의 깊은 복사본을 생성하지 않기 때문에** 까다로울 수 있다.

`[Array.prototype.concat](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)`은 실제로 새로운 배열을 반환하는데, 이것이 우리가 원하는 것이다. 이 메서드를 사용하여 원본에 새로운 요소가 추가된 새 배열을 반환할 수 있다. 아래를 보자:

```js
const numbers = [1, 2];  
const moreNumbers = numbers.concat([3]);
```

`slice`와 그랬듯이, `concat`도 원본 배열의 **얕은 복사본**을 반환한다. 이것은 객체와 배열 모두에 대한 **참조만 복사**한다는 것을 의미한다. 온전한 새로운 배열의 복사본을 반환받길 원한다면, Lodash의 `[cloneDeep](https://lodash.com/docs/#cloneDeep)`를 이용하는 것을 고려해보자. 매개변수로 전달된 **배열과 객체를 재귀적으로 복제**한다.

```js
const people = [{ name: 'Bob' }, { name: 'Alice' }];  
const morePeople = cloneDeep(people).concat([{ name: 'John' }]);

console.log(people[0] === morePeople[0]); // returns false
```

**얕은 복사 vs 깊은 복사**는 이 글 전체에 나타나는 반복적인 주제이다. *원본 배열의 요소가 배열이거나 객체이고, 이를 원본 그대로 유지해야하는 경우 항상 `cloneDeep`을 사용해라.* 그러나 **깊은 복사는 성능 면에서 비용이 많이 드는 작업이라는 것을 명심하라.** 아마 항상 깊은 복사본을 필요로 하는 것은 아닐 것이다. 사실, 가능한한 깊은 복사본을 만드는 것을 피해야한다. 정말 필요하고 사용해야한다면, 이율배반(tradeoffs)을 알고 있어야한다.

배열에 새로운 요소를 배열의 변경없이 추가하는 방법으로 다시 돌아가서,  ES6 이후 사용 가능한 [spread operator](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Spread_operator)를 `concat` 대신에 사용할 수 있다.

```js
const numbers = [1, 2];  
const moreNumbers = [...numbers, 3];
```

지금까진 배열의 맨 처음이나 맨 끝에 요소를 추가했지만, `[Array.prototype.splice](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)`를 사용하면 배열의 어느 위치에나 요소를 추가할 수 있다. `splice`는 원본 배열을 변경하니 명심하라.

```js
const positions = ['First', 'Second', 'Fifth', 'Sixth', 'Seventh'];
positions.splice(2, 0, 'Third', 'Fourth');
```

위의 예제에서, 두번째 매개변수(`0`)의 의미는 "아무것도 제거하지 말 것"이다.

또한, 우리는 `concat`을 사용하여 원래 배열을 변경하지 않고도 동일한 결과를 얻을 수 있다.

```js
const positions = ['First', 'Second', 'Fifth', 'Sixth', 'Seventh'];  
const morePositions = positions
.slice(0, 2)  
.concat(['Third', 'Fourth'])  
.concat(positions.slice(2));
```

더블어 spread operators로도 이 작업을 할 수 있다.

```js
const positions = ['First', 'Second', 'Fifth', 'Sixth', 'Seventh'];  
const morePositions = [  
...positions.slice(0, 2),  
'Third',  
'Fourth',  
...positions.slice(2)  
];
```

## 객체에 새로운 속성 추가하기

-   직접 추가하기 ❌
-   `Object.assign`  (ES6) ✅
-   Object spread operator (아직 표준이 안된 실험적인 기능) ✅

우리는 속성을 직접 설정함으로써 쉽게 새로운 속성을 추가할 수 있다. 물론 이것은 원래 객체의 대한 변이를 일으킨다.

```js
const person = { name: 'John Doe', email: 'john@doe.com' };// Using dot notation  

person.age = 27;// Using array notation  

person['nationality'] = 'Irish';
```

아마 원본 객체를 변경하지 않고 새로운 속성을 추가하는 가장 광범위한 해결방법은 `[Object.assign](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)` 또는 Lodash의 `[assign](https://lodash.com/docs/#assign)`을 사용하는 것일 수 있다.

```js
const person = { name: 'John Doe', email: 'john@doe.com' };  
const samePerson = Object.assign({}, person, {  
  age: 27,  
  nationality: 'Irish'  
});
```

`Object.assign`에 대한 참고: 첫 번째 인수가 빈 객체인 것을 확인해보라. `Object.assign`은 전달한 첫번째 매개변수를 변형시킨다. 이 연산자에 의해 `person`이 수정되는 것을 원치 않기 때문에, 첫 번째 인수로 `person`이 아닌 `{}`을 전달하는 것이다. **원본 객체를 변형시키지 않고 유지하려면 항상 빈 객체를 첫 번째 매개 변수로 전달하라.**

배열과 것과 마찬가지로, 아직 표준이 아닌 `object spread operator` 를 사용할 수 있다.  object spread operator는 개념적으로 ES6의  array spread operator와 유사하다.

```js
const person = { name: 'John Doe', email: 'john@doe.com' };  
const samePerson = { ...person, age: 27, nationality: 'Irish' };
```

## 배열에서 요소 제거하기

-   `Array.prototype.splice`  ❌
-   `Array.prototype.pop`  ❌
-   `Array.prototype.shift`  ❌
-   `Array.prototype.slice`  &  `Array.prototype.concat`  **✅**
-   `Array.prototype.slice`  & the ES6 Spread Operator  **✅**
-   `Array.prototype.filter`  **✅**

`[Array.prototype.splice(index, number)](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)`는 `index`에서 시작하여 `number` 갯수만큼 요소를 제거한다. 그리고 제거된 요소의 배열을 반환한다.

```js
const cities = ['Oslo', 'Rome', 'Cork', 'Paris', 'London', 'Bern'];

cities.splice(2, 1); // removes 1 element from the 2nd index (Cork)
```

`[Array.prototype.pop](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)`는 배열의 **마지막 요소**를 제거하는데 사용한다. 또한 제거된 요소를 반환한다. `Array.prototype.push`의 역 함수이다.

```js
const cities = ['Oslo', 'Rome', 'Cork', 'Paris', 'London', 'Bern'];  
const bern = cities.pop(); // removes Bern from the list of cities
```

배열의 **첫 번째** 요소를 제거하고 반환하는 `[Array.prototype.shift](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)`도 있다. 이는 `Array.prototype.unshift`의 역기능이다.

```js
const cities = ['Oslo', 'Rome', 'Cork', 'Paris', 'London', 'Bern'];  
const oslo = cities.shift(); // removes Oslo from the list of cities
```

`splice`, `pop` 그리고 `shift` 모두 원본 배열을 변형시킨다. 이제 원본 배열을 변이 시키지 않는 몇가지 방법을 살펴보자.

앞의 예의 도시 배열에서 *Cork*를 제거한다고 가정하자. 이를 달성하기 위해 우리는 `slice`와 `concat`의 조합을 사용하여 원본 배열의 슬라이스 사본을 만든 다음(처음부터 두 번째 요소까지 포함하지 않음) 원본 배열의 다른 세 번째 위치에서부터 세 번째 위치까지 슬라이스와 연결하면 된다. 이렇게 해서 두 번째 인수에 위치한 원소(*Cork*)를 제거했다.

```js
const cities = ['Oslo', 'Rome', 'Cork', 'Paris', 'London', 'Bern'];  
const capitals = cities  
.slice(0, 2)  
.concat(cities.slice(3));
```

똑같은 결과를 spread operator를 사용하여 얻을 수 있다.

```js
const cities = ['Oslo', 'Rome', 'Cork', 'Paris', 'London', 'Bern'];  
const capitals = [  
...cities.slice(0, 2),  
...cities.slice(3)  
];
```

`[Array.prototype.filter](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)`를 사용하여 제거하려는 요소를 필터링 할 수 있다. 이 메소드는 **새로운 배열을 반환**한다.

```js
const cities = ['Oslo', 'Rome', 'Cork', 'Paris', 'London', 'Bern'];  
const capitals = cities.filter(city => city !== 'Cork');
```

인덱스로 요소를 필터링할 수 있다.

```js
const cities = ['Oslo', 'Rome', 'Cork', 'Paris', 'London', 'Bern'];  
const capitals = cities.filter((city, index) => index !== 2);
```

## 객체에서 속성 제거하기 

-   `delete`  연산자 ❌
-   Object destructuring ✅
-   Lodash’s  `pick`  and  `omit`  ✅

`delete` 연산자를 사용하여 객체에서 속성을 제거할 수 있다. 물론 원본 객체의 변이를 일으킨다.

```js
const person = { name: 'John Doe', email: 'john@doe.com', age: 27 };
// dot notation  
delete person.age;
// array notation  
delete person['age'];
```

변이를 피하기 위해 ES6의 sperad operator를 사용하면 된다. (**object destructuring**)

```js
const person = {  
  name: 'John Doe',  
  email: 'john@doe.com',  
  age: 27,  
  country: 'Australia',  
  language: 'English',  
  profession: 'Front End Developer'  
};

const { profession, country, ...newPerson } = person;
console.log(newPerson);
```

`profession` 과 `country` 가 새 객체에서 제거된다.

또한 Lodash의 `[pick](https://lodash.com/docs/#pick)`와 `[omit](https://lodash.com/docs/#omit)`를 이용할 수 있다. 첫 번째 인수는 작업 할 객체이고, 두 번째 인수는 문자열 또는 제거하려는 속성의 문자열 배열 일 수 있다. **둘 다 새로운 객체를 반환한다.**

```js
const person = { name: 'John Doe', email: 'john@doe.com', age: 27 };  
const fewerDetails = _.omit(person, 'age');// or we could use pick which is the inverse of omit  
const fewerDetails = _.pick(person, ['name', 'email']);
```

## 결론

- 변이가 허용되지 않는 상황(예:리덕스 reducers)이 존재하지만,  많은 다른 경우에는 문제가 없다. 이것이 **관찰가능하거나 관찰불가능한 변이**를 구별하는 것이 필수적인 이유이다. 함수에 객체를 만든 다음 그 객체를 채울 필요가 있다면, 매우 비효율적 일뿐 아니라 변이를 피하기엔 영리하지 않다. [이 스레드](https://www.reddit.com/r/reactjs/comments/5xg6ky/react_trend/deie19b/) 는 주제에 대한 좋은 글이다.
- spread operator, `Array.prototype.concat`, `Array.prototype.slice`, 기타 등등을 기억하라. 이 것들은 모두 배열의 얕은 복사본을 반환한다. 만약 깊은 복사를 원한다면 Lodash의 `cloneDeep`을 사용하라
- 또한 원본 배열을 변경하는 대신 새 배열 / 객체를 반환하면 성능 측면에서 비용이 많이 든다. (특히 깊은 복사) 데이터 구조를 불변으로 유지하는 것은 비용이 많이드는 작업이다.