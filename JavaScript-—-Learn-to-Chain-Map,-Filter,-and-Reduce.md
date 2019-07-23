## Chaining Map, Filter, & Reduce

다음 데이터를 고려합니다.

```js
data = [
  {
    name: 'Butters',
    age: 3,
    type: 'dog'
  },
  {
    name: 'Lizzy',
    age: 6,
    type: 'dog'
  },
  {
    name: 'Red',
    age: 1,
    type: 'cat'
  },
  {
    name: 'Joey',
    age: 3,
    type: 'dog'
  },
];
```


보시다시피 여러 개의 객체가 있습니다. 각각의 물체는 애완동물을 나타냅니다. 그 애완동물들은 이름, 나이, 그리고 유형을 가지고 있습니다.

**이 기사의 목표는 개띠의 모든 나이를 합한 JavaScript를 작성하는 것입니다.**

당사의 프로세스는 다음과 같이 보일 수 있습니다.
- 개만 선택합니다.
- 그들의 나이를 개띠로 바꿉니다. (7살까지 곱하기)
- 결과를 합계합니다.

다음은 `for`를 사용하여 이 작업을 수행하는 방법입니다.

```js
function getAges(data) {
  let sum = 0;
  for (var i = 0; i < data.length; i++){
    if (data[i].type === 'dog'){
      let tempAge = data[i].age;
      sum += (tempAge * 7);
    }
  }
  return sum;
}
// getAges(data) = 84
```

<br>

`sum`이라는 변수를 만들어 0으로 설정합니다. 그런 다음 우리는 한 번에 한 개씩 우리의 array를 순환시킵니다. 만약 우리의 애완동물이 개라면, 우리는 그 개의 나이를 7살로 곱해서 그 결과의 가치를 우리의 `sum`에 더합니다. 어레이의 각 개마다 이 작업을 반복합니다. 루프가 끝나면 `sum`을 반환합니다.

그리고 효과가 있어요! `84`개입니다. 그리고 위의 코드가 틀린 것은 아니지만, 완벽한 해결책도 아닙니다.

즉, 한 번에 여러 가지 일을 하고 있습니다. 

이 세 가지 과제를 한 가지 기능에서 모두 달성하고 있기 때문에 읽기가 다소 어렵습니다. 그 코드는 또한 그다지 재사용할 수 없습니다.

이 문제를 해결하기 위해 map()와 reduce(), filter()를 활용해 같은 목표를 달성할 예정입니다.

우리가 첫번째로 해야 할 일은 고양이들을 걸러내는 것입니다. filter()를 사용하여 이 작업을 수행할 수 있습니다.

우리의 filter method는 애완동물을 입력으로 삼고 만약 그 종류가 개와 같을 경우 true를 반환합니다.

```js
let ages = data.filter((animal) => {
  return animal.type === 'dog';
})
```

<br>

이제 개만 낳았으니 개들의 나이를 찾아 7을 곱해야 합니다. `map()`방법으로 할 수 있습니다.

우리의 지도 기능은 단순히 동물들의 나이를 곱한 7을 반환합니다.

```js
.map((animal) => {
  return animal.age *= 7
})
```

<br>

마지막으로, 우리는 모든 개들의 나이를 요약할 필요가 있습니다. 

우리는 이것을 `reduce()`로 할 수 있습니다. 우리의 reduce는 동물 연령의 합계와 현재의 합계를 반환합니다.

```js
.reduce((sum, animal) => {
  return sum + animal.age;
});
```

<br>

이제 우리는 세 단계를 모두 마쳤기 때문에 단순히 우리의 행동을 함께 묶을 뿐입니다. 코드는 다음과 같습니다.

```js
let ages = data
  .filter((animal) => {
    return animal.type === 'dog';
}).map((animal) => {
    return animal.age * 7
}).reduce((sum, animal) => {
    return sum + animal.age;
});
// ages = 84
```

<br>

대단해요! 우리는 우리의 코드를 실행하고 다시 84를 얻습니다.

...하지만 우리 코드는 아직 좀 복잡합니다.

이를 고치기 위해 순기능 3개를 만들어 체인과 함께 사용할 예정입니다.

익숙하지 않은 경우 JavaScript의 순수 함수는 동일한 입력을 제공하므로 항상 부작용 없이 동일한 출력을 반환합니다. 

간단히 말해서, 순수한 기능은 입력 인수에만 의존합니다.

먼저 요소가 개인지 확인하는 기능을 만들겠습니다. 이것은 우리의 요소를 입력으로 간주하고 참 또는 거짓으로 반환합니다.

```js
let isDog = (animal) => {
  return animal.type === 'dog';
}
```

<br>

다음으로, 요소의 나이를 7배로 늘리고 개띠의 나이만 반환하는 기능을 만들 것입니다.

```js
let dogYears = (animal) => {
  return animal.age * 7;
}
```

<br>

마지막으로 두 숫자를 합쳐서 결과를 반환하는 기능이 필요합니다.

```js
let sum = (sum, animal) => {
  return sum + animal;
}
```

<br>

이제 세 가지 기능이 있으므로 map(), filter(), reduce()과 함께 사용할 수 있습니다.

```js
let ages = data
  .filter(isDog)
  .map(dogYears)
  .reduce(sum);
// ages = 84
```


<br>

효과가 있어요! 다시 84점을 얻었습니다.

우리의 최종 버전의 코드는 읽고, 이해하고, 테스트하는 것이 매우 쉽습니다. 

또한 당사의 코드는 순수한 기능으로 구분되어 필요에 따라 프로그램 전반에 걸쳐 쉽게 재사용할 수 있습니다.