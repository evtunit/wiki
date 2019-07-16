> 이 게시물은 JavaScript에서 객체를 관리하는 데 유용한 몇 가지 방법을 탐색하는 데 도움이 되는 일종의 Grab Bab(이게 뭔지 모르겠음)이다. Object.keys, Object.protype.hasOwnProperty 및 새로운 Object.assign에 대해 알아보도록 한다.

## hasOwnProperty
`hasOwnProperty`는 객체의 인스턴스에 속성이 있는지 없는지 확인 할 수 있는 메소드이다. 이 점을 매우 명확하게 설명하는 예제를 아래에서 확인해보자.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾'
}

console.log('clown' in myObj); // true
console.log('valueOf' in myObj); // true

console.log(myObj.hasOwnProperty('clown')); // true
console.log(myObj.hasOwnProperty('valueOf')); // false
```

<br>
<br>

## Object.keys
`Object.keys`는 객체의 Property key값들만 새로운 배열로 return해주는 메서드이다.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾'
}

console.log(Object.keys(myObj));

// ["clown", "police", "santa", "farmer"]
```

<br>

`Object.keys`는 `for...of`문을 통해 아주 유용하게 사용할 수 있다.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾'
}

for (let k of Object.keys(myObj)) {
  console.log(`Hey ${ myObj[k] }!`);
}

// "Hey 🤡!"
// "Hey 👮!"
// "Hey 🎅!"
// "Hey 👩‍🌾!"
```

> Note: `Object.keys`에서 array가 반환되었다. `keys`가 꼭 순서대로 있을 필요는 없다.

<br>
<br>

## Object.assign

ES2015(ES6)는 `Object` 생성자에 대한 새로운 정적 방법을 제공한다. `Object.assign`은 값을 객체에서 다른 객체로 쉽게 복사할 수 있다. 다음 예제에서는 빈 객체를 문자 그대로 사용하고 `myObj`의 속성을 복사하여 `myObj`의 복사본인 새 객체(`myObj3`)를 만든다.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾'
}

const myObj2 = myObj;

const myObj3 = Object.assign({}, myObj);

console.log(Object.is(myObj, myObj2)); // true

console.log(Object.is(myObj, myObj3)); // false

console.log(myObj3);

// Object {
//   clown: "🤡",
//   farmer: "👩‍🌾",
//   police: "👮",
//   santa: "🎅"
// }
```

> Note: `Object.is`는 두 객체가 동일한지 확인하는 방법이다.

> Note: 객체의 열거 가능한 속성만 `Object.assign`으로 복사된다.

<br>

첫 번째 인수는 Default 객체이고 다음 인수는 주가 될 객체이다. 여러 객체를 전달할 수 있으며 마지막으로 전달된 객체의 중복 속성이 다음을 따릅니다.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾'
}

const myObj2 = Object.assign({}, myObj, {
  santa: '🎄',
  teacher: '👩‍🏫'
});

console.log(myObj2);

// Object {
//   clown: "🤡",
//   farmer: "👩‍🌾",
//   police: "👮",
//   santa: "🎄",
//   teacher: "👩‍🏫"
// }
```

<br>

Redux에서 사용하는 상태관리패턴과 비슷하다. Object.assign을 사용하면 기존 객체에서 완전히 새로운 객체를 생성하여, 객체를 복사하고 확장할 수 있다.

<br>
<br>

## Bonus: Object.freeze

`Object.freeze`는 객체의 원본이 바뀌지 않도록 얕게 얼린다. 다음 예에서는 객체의 속성을 변경하거나, 추가하거나, 삭제할 수 없는 것을 볼 수 있다.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾'
}

myObj.clown = 'scary';
myObj.astronaut = '👨‍🚀';

Object.freeze(myObj);

myObj.clown = 'really scary';
myObj.student = '👩‍🎓';
delete myObj.santa;

console.log(myObj);

// Object {
//   clown: "scary",
//   farmer: "👩‍🌾",
//   police: "👮",
//   santa: "🎅",
//   astronaut: "👨‍🚀"
// }
```

<br>

그리고 또 다른 메서드인 `Object.isFrozen`를 사용하는데, Object가 frozen이 되었는지 체크하는 메서드이다.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾'
}

console.log(Object.isFrozen(myObj)); // false

Object.freeze(myObj);

console.log(Object.isFrozen(myObj)); // true
```

<br>

중첩된 객체는 객체에 의해 자동으로 고정되지 않는다. 다음 예제에서는 객체를 얼린 후에도 중첩된 `animals`객체의 속성이 변경되거나 삭제될 수 있다.

<br>

```js
const myObj = {
  clown: '🤡',
  police: '👮',
  santa: '🎅',
  farmer: '👩‍🌾',
  animals: {
    cow: '🐄',
    rabbit: '🐇'
  }
}

Object.freeze(myObj);

delete myObj.animals.rabbit;
myObj.animals.cow = 'moo!';

console.log(myObj);

// Object {
//   clown: "🤡",
//   farmer: "👩‍🌾",
//   police: "👮",
//   santa: "🎅",
//   animals: {
//     cow: 'moo!'
//   }
// }
```