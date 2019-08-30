# 자바스크립트의 Set 소개

> 원문: [Introduction to Sets in JavaScript — Alligator.io](https://alligator.io/js/sets-introduction/ )

`Set`은 고유 한 값의 모음을 만들 수 있는 ES6(ES2015)의 새로운 객체 유형이다. `Set`의 값은 문자열 또는 정수와 같은 원시값 일 수 있지만 객체 리터럴 또는 배열과 같은 더 복잡한 객체 유형도 Set의 일부가 될 수 있다.

다음은 기본 set과 `add` , `size` , `has` , `forEach` , `delete` 및 `clear`와 같이 사용 가능한 몇 가지 메소드를 보여주는 간단한 예이다 .

```js
let animals = new Set();

animals.add('🐷');
animals.add('🐼');
animals.add('🐢');
animals.add('🐿');
console.log(animals.size); // 4

animals.add('🐼');
console.log(animals.size); // 4 

console.log(animals.has('🐷')); // true
animals.delete('🐷');

console.log(animals.has('🐷')); // false

animals.forEach(animal => {
	console.log(`Hey ${animal}!`);
});
// Hey 🐼!
// Hey 🐢!
// Hey 🐿!
animals.clear();
console.log(animals.size); // 0
```

다음은 배열을 전달하여 Set을 초기화하는 또 다른 예입니다. 초기화 배열이 어떻게 변하는지 확인해보자. 중복된 값은 사라지고 나중에 추가 된 배열은 그대로 유지된다.

```js
let myAnimals = new Set(['🐷', '🐢', '🐷', '🐷']);

myAnimals.add(['🐨', '🐑']);
myAnimals.add({ name: 'Rud', type: '🐢' });
console.log(myAnimals.size); // 4

myAnimals.forEach(animal => {
	console.log(animal); 
});
// 🐷
// 🐢
// ["🐨", "🐑"] 
// Object { name: "Rud", type: "🐢" }
```

문자열은 반복 가능하므로(iterable)이므로 Set를 초기화 하기 위해 전달 가능하다.

```js
console.log('Only unique characters will be in this set.'.length); // 43

let sentence = new Set('Only unique characters will be in this set.'); console.log(sentence.size); // 18
```

Set에 `forEach` 를 사용한 것 처럼, [for…of](https://alligator.io/js/for-of-for-in-loops/) 루프도 Set을 반복하는 데 사용할 수 있다.

```js
let moreAnimals = new Set(['🐺', '🐴', '🐕', '🐇']);

for (let animal of moreAnimals) {
	console.log(`Howdy ${ animal }`);
}

// Howdy 🐺  
// Howdy 🐴  
// Howdy 🐕  
// Howdy 🐇
```

## Keys와 Values

Set에는 `keys`및 `values` 메소드가 있으며 **keys** 는 **values** 의 별명이므로 두 메소드는 정확히 동일한 작업을 수행한다. 이러한 메소드 중 하나를 사용하면 Set에 추가 된 순서와 동일한 순서로 Set에 값이 있는 새 반복자(iterator) 객체가 반환된다. 예를 들면 다음과 같다.

```js
let partyItems = new Set(['🍕', '🍾', '🎊']);
let items = partyItems.values();

console.log(items.next());
console.log(items.next());
console.log(items.next());
console.log(items.next().done);

// Object {
//   done: false,
//   value: "🍕"
// }

// Object {
//   done: false,
//   value: "🍾"
// }

// Object {
//   done: false,
//   value: "🎊"
// }

// true
```

## 참고 
-   [Introduction to Maps](https://alligator.io/js/maps-introduction/)