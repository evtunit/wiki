# Introduction to Maps in JavaScript

번역 : [https://alligator.io/js/maps-introduction/](https://alligator.io/js/maps-introduction/)

우리는 에서 Javascript 의 [sets](https://alligator.io/js/sets-introduction/) 을 설명했었다.  그것은 ES2015에서 소개했던 maps 관한 토론을 이야기 했던 논리를 따릅니다. Maps 들은 key-value 쌍을 저장하는 새로운 타입의 object 이다.


객체와 달리 map 키는 객체 또는 기능까지 모든 유형이 될 수 있습니다. 또한 map의 사이즈를 객체만큼 간단하진 않지만 쉽게 얻을 수 있습니다.  또한 map을 사용하면 순서에 대한 보장이 없는 객체와 달리 값이 추가 된 순서대로 반복 할 수 있습니다.

다음은 set, get, size, has, delete 및 clear와 같은 몇 가지 사용 가능한 방법 및 속성을 보여주는 간단한 map 예입니다.

```javascript
let things = new Map();

const myFunc = () => '🍕';

things.set('🚗', 'Car');
things.set('🏠', 'House');
things.set('✈️', 'Airplane');
things.set(myFunc, '😄 Key is a function!');

things.size; // 4

things.has('🚗'); // true

things.has(myFunc) // true
things.has(() => '🍕'); // false, not the same reference
things.get(myFunc); // '😄 Key is a function!'

things.delete('✈️');
things.has('✈️'); // false

things.clear();
things.size; // 0

// setting key-value pairs is chainable
things.set('🔧', 'Wrench')
      .set('🎸', 'Guitar')
      .set('🕹', 'Joystick');

const myMap = new Map();

// Even another map can be a key
things.set(myMap, 'Oh gosh!');
things.size; // 4
things.get(myMap); // 'Oh gosh!'
```

## Initializing Maps from Arrays

당신은 두가지 value를 지닌 array를 포함한 array로 부터 map을 초기화 할 수 있습니다. 

```javascript
const funArray = [ 
	['🍾', 'Champagne'], 
	['🍭', 'Lollipop'], 
	['🎊', 'Confetti'], 
]; 
let funMap = new  Map(funArray); 
funMap.get('🍾'); // Champagne
```


## Iterating Over Maps

[for…of](https://alligator.io/js/for-of-for-in-loops/)  and  [array destructuring](https://alligator.io/js/object-array-destructuring-es2015/) 를 사용하면 maps 들을 쉽게 순회 할 수 있습니다.

```javascript
let activities = new  Map(); 

activities.set(1, '🏂'); 
activities.set(2, '🏎'); 
activities.set(3, '🚣'); 
activities.set(4, '🤾'); 

for (let [nb, activity] of activities) { 
	console.log(`Activity ${nb} is ${activity}`);
} 

// Activity 1 is 🏂  
// Activity 2 is 🏎  
// Activity 3 is 🚣  
// Activity 4 is 🤾
```

...그리고 당신은 _forEach_ 를 사용해서 map을 똑같은 방법으로 순회 할 수 있습니다.
그러나 forEach 콜백 함수의 첫 번째 인수는 **값** 이고 두 번째 인수는 **키**입니다.
다음은 **for… of ** 예제와 동일한 결과입니다.


```javascript
activities.forEach((value, key) => { 
	console.log(`Activity ${key} is ${value}`); 
});
```