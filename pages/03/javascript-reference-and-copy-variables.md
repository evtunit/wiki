# Javascript 변수의 참조 및 복사

> 원문: https://hackernoon.com/javascript-reference-and-copy-variables-b0103074fdf0

각 프로그래밍 언어에는 고유한 특성이 있지만 자바스크립트는 많은 특성을 가지고 있다. 해당 포스팅은 참조로 할당된 변수와 복사에 의해 할당된 변수에 대해 이야기할 것이다.

## 원시 타입(Primitive Value) vs 복합 타입(Compound Value) 

우선 자바스크립트의 원시 타입 값들을 살펴보자.

### Primitive

* `null`
* `undefined`
* `String`
* `Number`
* `Boolean`

### Compound

* `Object`
* `Array`
* `Function`

기술적으로 원시 타입은 항상 Pass-by-value(복사)이며 복합 타입은 Pass-by-reference(참조)이다. 예를 들어보자.

```js
let person1 = 'Michael';
let person2 = person1;
person1 = 'John';

console.log(person1); // John
console.log(person2); // Michael

let birthYear1 = 1995;
let birthYear2 = birthYear1;
birthYear1++;

console.log(birthYear1); // 1995
console.log(birthYear2); // 1996
```

위의 예제는 원시 타입으로 값을 복사하고, 복사 대상이되는 값(person1, birthYear1)을 변경했다. 복사 대상이 되는 값을 변경하여도 복사한 값에는 영향을 미치지 않는다. 그러나 복합 타입의 경우, 다음과 같을 것이다.

```js
let inventory = ['banana', 'apple'];
let purchased = inventory;
inventory.push('strawberry');
purchased[1] = 'corn';

console.log(inventory); // ["banana", "corn", "strawberry"]
console.log(purchased); // ["banana", "corn", "strawberry"]

let person1 = { name: 'Vitor', birthYear: 1995 };
let person2 = person1;
person2.name = 'Eduardo';
person1.birthYear = 1992;

console.log(person1); // Object {name: "Eduardo", birthYear: 1992}
console.log(person2); // Object {name: "Eduardo", birthYear: 1992}
```

복합 타입(Object, Array)를 사용하여 복사 대상이 되는 값(inventory, person1)을 변경하여도 복사한 값(purchased, person2)에 영향을 미치고, 그 반대의 경우에도 영향을 미치고 있다.

### 하지만 언제나 예외는 있기 마련...

```js
let inventory = ['banana', 'apple'];
let purchased = inventory;
inventory = ['pie', 'bacon', 'pizza'];
purchased[1] = 'corn';

console.log(inventory); // ["pie", "bacon", "pizza"]
console.log(purchased); // ["banana", "corn"]

let person1 = { name: 'Vitor', birthYear: 1995 };
let person2 = person1;
person1 = { name: 'Eduardo', birthYear: 1992 };

console.log(person1); // Object {name: "Eduardo", birthYear: 1992}
console.log(person2); // Object {name: "Vitor", birthYear: 1995}
```

왜 위의 예제의 복사 대상의 값과 복사된 값은 서로 값이 다를까? 분명 Object와 Array는 참조 복사가 되는 값일텐데..?

자바스크립트는 실제로 참조 값을 복사하고 있고, Object 또는 Array의 속성(property)을 변경해도 참조를 변경하지는 않는다. (위의 예제의 purchased 값을 확인하자)

하지만 변수에 새로운 값을 지정하면 참조 자체를 변경하고 원래 Object/Array에 영향을 미치지 않는다! (위의 예제의 inventory 값을 확인하자)


## 위 현상을 어떻게 수정할까?

원본 값에 대한 변경을 걱정하지 않고 Object의 속성을 업데이트 하고 배열을 수정하고 싶다고 가정해보자.

이 문제를 해결하려면 복사본을 만들면 된다! 다음은 예제이다. (ES6 버전에서만 사용가능).

```
let inventory = ['banana', 'apple'];

// ES5 >> let purchased = inventory.slice(); // OR let purchased = [].concat(inventory);
// ES6 methods
let purchased = [...inventory]; // OR let purchased = Array.from(inventory); 새로운 값으로 복사

inventory.push('strawberry');
purchased[1] = 'corn';

console.log(inventory); // ["banana", "apple", "strawberry"]
console.log(purchased); // ["banana", "corn"]

let person1 = { name: 'Vitor', birthYear: 1995 };
// ES6
let person2 = Object.assign({}, person1); // 새로운 값으로 복사

person2.name = 'Eduardo';
person1.birthYear = 1992;

console.log(person1); // Object {name: "Vitor", birthYear: 1992}
console.log(person2); // Object {name: "Eduardo", birthYear: 1995}
```

ES6의 `...(spread operator)`와 [Object.assign](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 메소드를 이용하여 배열과 객체의 값을 복사하였다.

이제 원본 값이 변경되는 점은 걱정할 필요가 없다! 두 개의 원본 값을 복사했는데, 원본 값이 변경될 경우 이 솔루션을 사용한다.

## 끝!

이 포스팅이 내가 이 사실을 알았을 때 만큼이나 독자들에게 유용했으면 좋겠다! 자바스크립트를 더 배우고 배운 것에 대해 글을 쓰는 것을 계속하고 있다. 그러니 여기 [Medium](https://hackernoon.com/@vcapretz)와 [트위터](https://twitter.com/vcapretz)에서 팔로잉 하는 것을 잊지 말았으면 좋겠다!

좋아요 버튼을 클릭하고 자바스크립트나 피드백에 대해 알고 싶은 내용 아래의 의견을 언급함으로써 ‘Recommend’를 주는 것이 좋다면 말이다!


## 번역 후기

* 자바스크립트의 기초이지만 개발하면서 가끔 까먹는 참조 복사
* 내용이 약간 빈약한 것 같지만 기본 적인 내용은 들어있다.