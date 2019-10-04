> 원문 출처: https://medium.com/cesars-tech-insights/big-o-notation-javascript-25c79f50b19b

"Big O Notation"이란 무엇인가?  
이것은 개발자들에게 매우 흔한 면접 질문입니다.  
간단히 말해서, 입력 시간에 따라 알고리즘이 실행되는 데 걸리는 시간을 수학적으로 표현한 것인데, 보통 최악의 경우 시나리오에 대해 이야기합니다.

실제로, 우리는 **Big O Notation**을 사용하여 입력 크기의 변화에 대응하는 방식으로 알고리즘을 분류합니다.  
그래서 증가율이 동일한 알고리즘은 동일한 **Big O Notation**으로 표현됩니다. 함수의 성장률을 함수의 순서라고도 하기 때문에 O자를 사용합니다.

> 이것을 아는 것이 왜 중요할까요? Big O를 알면 개발자가 알고리즘의 효율성을 인지하고 성능이 좋은 애플리케이션을 만들 수 있도록 도와줍니다.

일반적으로 알고리즘의 첫 번째 버전이 가장 효율적인 솔루션이 아니라 가장 빠른 코드화 버전이라고 가정할 수 있습니다.  
향후 버전에서는 더 효율적인 솔루션으로 교체될 것으로 가정합니다. 이는 민첩한 개발, 특히 [테스트 기반 개발](https://en.wikipedia.org/wiki/Test-driven_development)에서 매우 일반적인 방식입니다.

때로는 더 적은 시간(문자)을 사용하는 대신(또는 더 적은 메모리에) 더 적은 메모리를 사용하는 데 초점을 맞추기를 원하기도 합니다. 일반적으로 시간 절약과 공간 절약 사이에 절충이 이루어집니다. 읽기 시간 절약과 디스크 공간 절약 간에 또는 관계형(정규화된) 데이터베이스와 비관계형(비정규화된) 데이터베이스를 비교할 때도 이러한 균형을 확인할 수 있습니다.

이제 가장 일반적인 유형의 Big O Notations에 대해 살펴보겠습니다. JavaScript(ECMA6)를 참조 언어로 사용하지만 다른 언어에도 동일한 원리가 적용됩니다.

<br>

## 변함없는 시간의 대한 알고리즘
_**O(1)** — “Order 1”_

**하지만 아직 이 순서에서는 복잡성(항목 수)에 관계없이 시간(문자)이 일정합니다.**

유형 또는 길이에 관계없이 이미 알려진 배열 위치에 있는 요소를 반환하는 알고리즘에서 이 정보를 볼 수 있습니다.

예제 코드:

```js
const getLast = items =>
  items[items.length-1];
```

예시 케이스:

```js
getLast(['a', 'b', 'c', 'd']); // d (1 iteration)
getLast(['a', 'b', 'c', 'd', 'e', 'f', 'g']);// g(1 iteration)
```

<br>


## 선형 시간 알고리즘

_**O(N)** — “Order N”_

이 순서에서 최악의 경우 시간(문자)은 항목 수와 함께 증가합니다. 
즉, _N_ 요소의 경우 _N_ 반복이 필요합니다.

예제코드:

```js
const findIndex = (items, match) => {
  for (let i = 0, total = items.length; i < total; i++)
    if (items[i] == match)
      return i;
   return -1;
};
```

예시케이스:

```js
const array= ['a', 'b', 'c', 'd'];
findIndex(array, 'a'); // 0  (1 iteration - best case)
findIndex(array, 'd'); // 3  (4 iterations - worst case)
findIndex(array, 'e'); // -1 (4 iterations - worst case)
```

<br>

## Quadratic - time 알고리즘

_**O(N 2 )** — “Order N squared”_

이러한 종류의 순서의 경우 최악의 경우 시간(문자)은 입력 수의 제곱입니다. 시간은 입력 수와 관련하여 기하급수로 증가합니다.

예제코드:

```js
const buildSquareMatrix = items => {
  let matrix = [];
  for (let i = 0, total = items.length; i < total; i++){ 
    matrix[i] = [];
    for (let j = 0, total = items.length; j < total; j++)
      matrix[i].push(items[j]);
  }
  return matrix;
};
```

예제케이스:

```js
buildSquareMatrix(['a', 'b', 'c']); 
/* 9 iterations for 3 elements, returns:
[
  ['a', 'b', 'c'],
  ['a', 'b', 'c'],
  ['a', 'b', 'c']
]
/*
```

<br>

## 로그 시간 알고리즘

_**O(log n)** — “Order log N”_

search/sort 알고리즘의 기본이며, **대규모 수집을 처리할 때 가장 효율적인 접근방식입니다.**  
구성 요소를 하나씩 살펴보는 대신 데이터를 덩어리로 분할하고 매 반복, 보통 절반 또는 로그 베이스 2에 많은 양을 낭비합니다.

로그 베이스 2를 사용한다고 가정할 경우, 20개 미만의 반복을 사용하는 100만 개의 요소 집합에서 특정 요소를 찾을 수 있습니다. 수집 크기를 10억 개로 확장하면 30개 미만의 반복만 필요할 수 있습니다.

빅데이터는 매일 더 흔하게 사용되므로 수집 규모가 클수록 상대적으로 효율성이 높아지기 때문에 이러한 알고리즘의 이점을 쉽게 확인할 수 있습니다.

이 알고리즘 중 가장 널리 사용되는 알고리즘은 특정 요소를 찾거나 목록을 매우 효율적으로 정렬하는 데 사용할 수 있는 Quick sort 알고리즘입니다. 이 순서의 또 다른 인기 있는 예는 병합 정렬 알고리즘입니다. 향후 기사에 대한 이러한 알고리즘을 살펴보겠습니다.


예제코드:

```js
const quickSort = list => {
  if (list.length < 2) 
    return list;
  let pivot = list[0];
  let left  = []; 
  let right = [];
  for (let i = 1, total = list.length; i < total; i++){
    if (list[i] < pivot)
      left.push(list[i]);
    else
      right.push(list[i]);
  }
  return [
    ...quickSort(left), 
    pivot, 
    ...quickSort(right)
  ];
};
```

예제케이스:

```js
quickSort( ['q','a','z','w','s','x','e','d','c','r']);
// ["a", "c", "d", "e", "q", "r", "s", "w", "x", "z"]
```

<br>

이것이 가장 일반적인 유형의 알고리즘을 다루는 방법입니다. 검색 및 정렬 알고리즘에 대해 자세히 읽어보시기 바랍니다.