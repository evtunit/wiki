# How to Use Map, Filter, & Reduce in JavaScript

번역 : [https://code.tutsplus.com/tutorials/how-to-use-map-filter-reduce-in-javascript--cms-26209](https://code.tutsplus.com/tutorials/how-to-use-map-filter-reduce-in-javascript--cms-26209)

요즘 개발 도상국에서는 함수형 프로그래밍이 상당히 인기를 얻고 있습니다. 그리고 정당한 이유가 있습니다 : 함수형 기술은 한눈에 이해하기 쉬운 선언적 코드를 작성하고 리팩터링하고 테스트하는 데 도움이됩니다.

함수형 프로그래밍의 초석 중 하나는 목록 및 목록 작업을 특수하게 사용하는 것입니다. 그리고 그러한 것들은 마치 그들이 가지고있는 것과 같은 소리입니다 : 배열 된 것들, 그리고 당신이 그들에게하는 것들. 그러나 기능적 사고 방식은 예상보다 조금 다르게 처리합니다.

이 기사에서는 `map`, `filter`, `reduce`과 같은 목록 조작을 자세히 살펴 보겠습니다. 이 세 가지 기능에 대해 머리를 감싸는 것은 깨끗한 기능 코드를 작성하는 데있어 중요한 단계이며 기능적 및 반응성 프로그래밍의 강력한 기술에 대한 문화를 열어줍니다.

또한`for` 루프를 다시 작성할 필요가 없다는 것을 의미합니다.

이상한가? 들어가자.

## A Map From List to List


종종 우리는 배열을 가져 와서 정확히 같은 방식으로 배열을 수정해야 할 필요가 있습니다. 일반적인 예는 숫자 배열에서 모든 요소를 ​​제곱하거나, 사용자 목록에서 이름을 검색하거나, 문자열 배열에 대해 정규식을 실행하는 것입니다.

`map`은 정확히 그렇게하도록 만들어진 메소드입니다. 그것은`Array.prototype`에 정의되어 있으므로 어떤 배열에서도 호출 할 수 있으며 첫 번째 인수로 콜백을 허용합니다.

배열에서`map`을 호출하면, 콜백이 반환 한 모든 값을 가진 _new_ 배열을 반환하면서 배열 내의 모든 요소에 대해 콜백을 실행합니다.

후드에서`map`은 콜백에 세 가지 인수를 전달합니다.

1. 배열의 _current item_
2. 현재 항목의 _array_ 인덱스
3. map on이라는 _entire 배열_

몇 가지 코드를 살펴 보겠습니다.

### `map`  in Practice

당일의 작업 배열을 관리하는 앱이 있다고 가정 해 보겠습니다. 각`task`는`name`과`duration` 속성을 가진 객체입니다 :

```javascript
// Durations are in minutes
 
var tasks = [
  {
    'name'     : 'Write for Envato Tuts+',
    'duration' : 120
  },
  {
    'name'     : 'Work out',
    'duration' : 60
  },
  {
    'name'     : 'Procrastinate on Duolingo',
    'duration' : 240
  }
];
```

각 작업의 이름만으로 새로운 배열을 만들고 싶다면 우리가 지금까지 해 온 모든 것을 살펴볼 수 있습니다. `for` 루프를 사용하면 다음과 같이 작성할 수 있습니다 :

```javascript
var task_names = []; 
for (var i = 0, max = tasks.length; i < max; i += 1) { 
    task_names.push(tasks[i].name);
}
```

JavaScript는 또한 forEach 루프를 제공합니다. `for` 루프와 같은 기능을 합니다만, 우리를 위해 배열 길이에 대한 루프 인덱스를 검사하는 모든 난제를 관리합니다 :

```javascript
var task_names = [];
tasks.forEach(function (task) {
    task_names.push(task.name);     
});
```

`map`을 사용하여 다음과 같이 작성할 수 있습니다 :

```javascript
var task_names = tasks.map(function (task, index, array) {
    return task.name; 
});
```


`index`와`array` 매개 변수를 포함시켜 필요하다면 거기에 있음을 상기시킵니다. 내가 여기에 그것들을 사용하지 않았기 때문에, 당신은 그것들을 버릴 수 있었고, 코드는 잘 동작 할 것입니다.

두 가지 접근 방식 간에는 몇 가지 중요한 차이점이 있습니다.

1.`map`을 사용하면`for` 루프의 상태를 직접 관리 할 필요가 없습니다.
2. 배열에 색인을 지정하지 않고 요소를 직접 조작 할 수 있습니다.
3. 새 배열을 만들고 그 안에 '밀어 넣기'할 필요가 없습니다. `map`은 완성 된 제품을 모두 한 번에 반환하기 때문에 반환 값을 새 변수에 간단하게 할당 할 수 있습니다.
4. 당신은 콜백에`return` 문을 포함해야한다는 것을 기억해야합니다. 그렇지 않으면`undefined`로 채워진 새로운 배열을 얻게 될 것입니다.

우리가 오늘 볼 수있는 기능들 모두가 이러한 특징들을 공유하고 있습니다.

루프의 상태를 수동으로 관리 할 필요가 없으므로 코드가 더 간단하고 유지 관리가 쉽습니다. 배열로 색인을 생성하는 대신 요소에서 직접 작동 할 수 있다는 사실은 내용을 더 읽기 쉽게 만듭니다.

`forEach` 루프를 사용하면 이러한 문제를 모두 해결할 수 있습니다. 그러나`지도`는 여전히 적어도 두 가지 뚜렷한 장점을 가지고있다.

1.`forEach`는`undefined`를 리턴하므로 다른 배열 메소드와 연결되지 않습니다. `map`은 배열을 반환하기 때문에 다른 배열 메소드와 연쇄 할 수 있습니다.
2.`map`은 루프 안에 배열을 변경시키지 않고 완성 된 제품을 가진 배열을 반환합니다.

상태를 수정하는 장소의 수를 최소한으로 유지하는 것은 기능 프로그래밍의 중요한 교리입니다. 더 안전하고 이해하기 쉬운 코드를 만듭니다.

이제는 노드에있는 경우 Firefox 브라우저 콘솔에서 이러한 예제를 테스트하거나 [Babel](https://babeljs.io/) 또는 [Traceur](https://github.com/google/traceur-compiler)를 사용하여 테스트 해보십시오. ), ES6 화살표 함수를 사용하여보다 간결하게 작성할 수 있습니다.

```javascript
var task_names = tasks.map((task) => task.name );
```

Arrow 함수는 한 줄짜리로`return` 키워드를 생략 할 수있게합니다.

그것보다 훨씬 더 읽기 쉽지 않습니다.

### Gotchas

`map`에 전달한 콜백은 명시 적으로`return` 문을 가져야합니다. 그렇지 않으면`map`은`undefined`로 가득 찬 배열을 출력합니다. `return '값을 포함하는 것을 기억하는 것은 어렵지 않지만 잊지 않는 것이 좋습니다.

잊어 버리면 `map` 은 불평하지 않습니다. 대신, 그것은 아무 것도없는 배열을 조용히 돌려 줄 것입니다. 이와 같은 사일런트 오류는 놀라 울 정도로 디버깅하기가 어렵습니다.

다행히도, 이것은 `map` 을 가진 _only_ gotcha입니다. 그러나 그것은 내가 강조해야 할 공통적 인 함정입니다 : 항상 콜백에`return` 문이 있는지 확인하십시오!

### Implementation

구현을 읽는 것은 이해의 중요한 부분입니다. 따라서, 후드에서 무슨 일이 벌어지고 있는지보다 잘 이해할 수 있도록 가벼운`map`을 작성해 봅시다. 프로덕션 수준의 구현을 보려면 [Mozilla의 MDN에서의 polyfill] (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)을 확인하십시오.

```javascript
var map = function (array, callback) {
    var new_array = [];
 
    array.forEach(function (element, index, array) {
       new_array.push(callback(element)); 
    });
 
    return new_array;
};
 
var task_names = map(tasks, function (task) {
    return task.name;
});
```

이 코드는 배열과 콜백 함수를 인수로 받아들입니다. 그런 다음 새 배열을 만듭니다. 건네받은 배열의 각 요소에 대해 콜백을 실행합니다. 결과를 새 배열로 푸시합니다. 새 배열을 반환합니다. 콘솔에서 이것을 실행하면 이전과 같은 결과를 얻게됩니다. 테스트하기 전에`tasks` 를 초기화해야합니다!

후드에서 for 루프를 사용하는 동안 함수에 래핑하면 세부 정보가 숨겨지고 대신 추상화 작업을 수행 할 수 있습니다.

따라서 코드를 더 선언적으로 만듭니다. 즉, 어떻게 할 것인가? 얼마나 더 읽기 쉽고, 유지 보수가 잘되는지, erm, _debuggable_ 이것이 코드를 만들 수 있는지를 알게 될 것입니다.

## Filter Out the Noise


다음 배열 연산은`filter`입니다. 그것은 정확히 같은 소리를냅니다 : 그것은 배열을 필요로하고 불필요한 요소를 걸러냅니다.

`map` 과 마찬가지로`filter` 는 `Array.prototype`에 정의되어 있습니다. 어떤 배열에서도 사용할 수 있으며 첫 번째 인수로 콜백을 전달합니다. `filter`는 배열의 각 요소에 대해 콜백을 실행하고 콜백이`true`를 반환 한 요소 만 _only_ 포함하는 _new_ 배열을 출력합니다.

또한`map`과 같이`filter`는 콜백 인자 세 개를 넘깁니다 :

1. _current item_
2. _current 색인_
3. 당신이`filter '라고 불렀던 _array_

### `filter`  in Practice


우리의 작업 예제를 다시 살펴 보겠습니다. 각 작업의 이름을 꺼내는 대신, 작업을 완료하는 데 2 ​​시간 이상 걸리는 작업 목록을 얻고 싶다고합시다.

`forEach`를 사용하면 다음과 같이 쓸 수 있습니다.

```javascript
var difficult_tasks = [];
 
tasks.forEach(function (task) {
    if (task.duration >= 120) {
        difficult_tasks.push(task);
    }
});
```

With  `filter`:

```javascript
var difficult_tasks = tasks.filter(function (task) {
    return task.duration >= 120;
});
 
// Using ES6
var difficult_tasks = tasks.filter((task) => task.duration >= 120 );
```

여기서는 콜백에 대한`index`와`array` 인수를 사용하지 않았기 때문에 생략했습니다.

`map`과 같이,`filter`는 우리에게 다음을 허락합니다 :

- forEach 또는 for 루프 내에서 배열을 변경하는 것을 피하십시오
- 다른 곳에서 정의한 배열로 푸시하기보다는 결과를 새 변수에 직접 할당합니다.

### Gotchas


`map`에 전달한 콜백은 제대로 작동하려면 return 문을 포함해야합니다. `filter`를 사용하면 return 문도 포함시켜야하며 반드시 부울 값을 반환해야합니다.

return 문을 잊어 버리면 콜백은`undefined`를 반환 할 것이고,`filter`는 도움이되지 않을 정도로`false`를 강요합니다. 오류를 던지는 대신 빈 배열을 조용히 반환합니다!

다른 경로로 가서 명시 적으로 'true'또는 'false'가 아닌 것을 반환하면 'filter'는 [JavaScript's coercion rules](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20&%20grammar/ch4.md)을 적용하여 의미를 파악하려고 시도합니다.  종종 이것은 버그입니다. 그리고 귀하의 신고서를 잊어 버리는 것과 마찬가지로, 그것은 조용한 것입니다.

항상 콜백에 명시적인 return 문이 포함되어 있는지 확인하십시오. 그리고 항상 _ 필터 _의 콜백이`true` 또는`false` 를 리턴하는지 확인하십시오. 당신의 온건함이 당신에게 감사 할 것입니다.

### Implementation

다시 한 번, 코드를 이해하는 가장 좋은 방법은 ... 글쎄, 그것을 쓰는 것입니다. 우리 자신의 경량`필터 '를 굴려 보자. Mozilla의 좋은 사람들은 [산업 강도의 polyfill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)도 읽을 수 있습니다.

```javascript
var filter = function (array, callback) {
 
    var filtered_array = [];
 
    array.forEach(function (element, index, array) {
        if (callback(element, index, array)) {
            filtered_array.push(element);    
        }
    });
 
    return filtered_array;
 
};
```

## Reducing Arrays


`map`은 배열의 모든 원소를 개별적으로 변환하여 새로운 배열을 만듭니다. `filter`는 속하지 않는 원소를 제거하여 새로운 배열을 만듭니다. 한편,`reduce`는 배열의 모든 요소를 ​​취하고 그것들을 단일 값으로 만듭니다.

`map`과`filter`와 마찬가지로,`reduce`는`Array.prototype`에 정의되어 있으므로 어떤 배열에서도 사용할 수 있습니다. 콜백을 첫 번째 인수로 전달합니다. 그러나 선택적인 두 번째 인수, 즉 모든 배열 요소를 결합하기위한 값도 필요합니다.

`reduce`는 네 콜백을 전달합니다 :

1. _current 값 _
2. 이전 값 _
3. _ 현재 색인 _
4. 당신이`reduce`라고 부른 _array_

콜백은 각 반복마다 _ 이전 값 _을 얻습니다. 첫 번째 반복에는 이전 값이 없습니다. 이것이 초기 값을`reduce '할 수있는 이유입니다. 첫 번째 반복에 대한 "이전 값"으로 사용됩니다. 그렇지 않으면 첫 번째 반복에는 "이전 값"으로 사용됩니다.

마지막으로,`reduce`는 단일 값을 반환하고, 단일 항목을 포함하는 배열은 아니라는 것을 명심하십시오. 이것은 보일 수도있는 것보다 더 중요합니다. 예제에서 다시 살펴 보겠습니다.

### `reduce`  in Practice


`reduce '는 사람들이 처음에는 가장 외계인이라고 느끼는 기능이기 때문에 간단한 단계를 거쳐 단계별로 걷기 시작할 것입니다.

우리가 숫자 목록의 합을 찾고 싶다고합시다. 루프를 사용하면 다음과 같이 보입니다.

```javascript
var numbers = [1, 2, 3, 4, 5],
    total = 0;
     
numbers.forEach(function (number) {
    total += number;
});
```

이것이 forEach의 나쁜 사용 사례는 아니지만,`reduce`는 여전히 우리가 돌연변이를 피할 수있게 해주는 장점이 있습니다. `reduce`를 사용하면 다음과 같이 쓸 수 있습니다 :

```javascript
var total = [1, 2, 3, 4, 5].reduce(function (previous, current) {
    return previous + current;
}, 0);
```

우선, 우리는`numbers` 의 목록에서 `reduce` 를 호출합니다. 이전 값과 현재 값을 인수로 받아들이는 콜백을 전달하고이를 더한 결과를 리턴합니다. `reduce`에 두 번째 인자로`0`을 전달 했으므로 첫 번째 반복에서`previous`의 값으로 사용합니다.

단계별로 살펴보면 다음과 같이 보입니다.

| Iteration | Previous | Current | Total |
|-----------|----------|---------|-------|
| 1         | 0        | 1       | 1     |
| 2         | 1        | 2       | 3     |
| 3         | 3        | 3       | 6     |
| 4         | 6        | 4       | 10    |
| 5         | 10       | 5       | 15    |

테이블 팬이라면 콘솔에서 다음 코드를 실행하십시오.

```javascript
var total = [1, 2, 3, 4, 5].reduce(function (previous, current, index) {
    var val = previous + current;
    console.log("The previous value is " + previous + 
                "; the current value is " + current +
                ", and the current iteration is " + (index + 1));
    return val;
}, 0);
 
console.log("The loop is done, and the final value is " + total + ".");
```


다시 요약하자면 :`reduce`는 배열의 모든 요소를 ​​반복하고 콜백에 지정하지만 배열을 결합합니다. 모든 반복에서 콜백은 _total-so-far_ 또는 _accumulated value_ 인 _previous_ _value_에 액세스 할 수 있습니다. _ 현재 값 _; _current 인덱스; _ 및 _array_ 전체 (필요한 경우).

우리의 작업 예제로 돌아가 보겠습니다. 우리는`map`의 작업 이름 목록과 필터를 사용하여 오랜 시간이 걸린 작업 목록을 필터링했습니다.

우리가 오늘 일하는 데 소비 한 총 시간을 알고 싶다면 어떻게해야할까요?

`forEach` 루프를 사용하면 다음과 같이 쓸 수 있습니다 :

```javascript
var total_time = 0;
     
tasks.forEach(function (task) {
    // The plus sign just coerces 
    // task.duration from a String to a Number
    total_time += (+task.duration);
});
```
`reduce`를 사용하면 다음과 같이됩니다.

```javascript
var total_time = tasks.reduce(function (previous, current) {
    return previous + current;
}, 0);
 
// Using arrow functions
var total_time = tasks.reduce((previous, current) previous + current );
```


쉬운.

거의 전부입니다. 거의 JavaScript는`reduceRight`라고하는 알려지지 않은 메소드를 제공합니다. 위의 예제에서 `reduce`는 배열의 _first_ 항목에서 시작하여 왼쪽에서 오른쪽으로 반복됩니다.

```javascript
var array_of_arrays = [[1, 2], [3, 4], [5, 6]];
var concatenated = array_of_arrays.reduce( function (previous, current) {
        return previous.concat(current);
});
 
console.log(concatenated); // [1, 2, 3, 4, 5, 6];
```

`reduceRight`는 같은 일을하지만 반대 방향으로 :

```javascript
var array_of_arrays = [[1, 2], [3, 4], [5, 6]];
var concatenated = array_of_arrays.reduceRight( function (previous, current) {
        return previous.concat(current);
});
 
console.log(concatenated); // [5, 6, 3, 4, 1, 2];
```

나는 매일`reduce '를 사용하지만, 결코`reduceRight`를 필요로하지는 않습니다. 나는 당신도 아마 그렇지 않다고 생각합니다. 그러나 당신이 한 번 해보신다면, 이제 당신은 그것이 있음을 알 수 있습니다.

### Gotchas


`reduce`가있는 세 가지 큰 잡화는 다음과 같습니다.

1. '돌아 가기'를 잊어 버림
2. 초기 값 잊어 버림
3.`reduce`가 단일 값을 반환 할 때 배열을 기대합니다.

다행히도 처음 두 개는 피하기 쉽습니다. 귀하의 초기 가치가 무엇인지 결정하는 것은 귀하가하는 일에 달려 있지만, 신속하게 결정할 것입니다.

마지막 하나는 조금 이상하게 보일 수 있습니다. `reduce`만이 하나의 값을 반환한다면 왜 배열을 기대할 수 있습니까?

거기에는 몇 가지 좋은 이유가 있습니다. 첫째로,`reduce`는 항상 하나의 _value_ 를 리턴합니다. 항상 하나의 _number_ 를 리턴하는 것은 아닙니다. 예를 들어, 배열 배열을 줄이면 단일 배열을 반환합니다. 버릇이 있거나 배열을 줄이는 경우 단일 항목을 포함하는 배열은 특별한 경우가 아닐 것으로 예상하는 것이 적절할 것입니다.

둘째로,`reduce` _did_ 가 단일 값을 갖는 배열을 반환한다면,`map`과`filter`와 함께 자연스럽게 재생 될 것입니다.

### Implementation

후드 아래 우리 마지막 모습을위한 시간. 평소와 같이, Mozilla는 그것을 확인하고 싶다면 [bulletproof polyfill for reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)을 가지고 있습니다.

```javascript
var reduce = function (array, callback, initial) {
    var accumulator = initial || 0;
     
    array.forEach(function (element) {
       accumulator = callback(accumulator, array[i]);
    });
     
    return accumulator;
};
```


주목할 두 가지 사항은 다음과 같습니다.

1. 이번에는`previous` 대신`accumulator`라는 이름을 사용했습니다. 이것은 야생에서 일반적으로 볼 수있는 것입니다.
2. 사용자가 값을 제공하면 초기 값을 'accumulator'로 지정하고, 그렇지 않으면 기본값을 '0'으로 지정합니다. 이것이 실제`reduce`가 동작하는 방식입니다.

## Putting It Together: Map, Filter, Reduce, and Chainability


이 시점에서 당신은 감명을받지 못할 수도 있습니다.

`map`,`filter`,`reduce`는 독자적으로 너무 흥미 롭지 않습니다.

결국, 그들의 진정한 힘은 연쇄성에 있습니다.

다음과 같이하고 싶다고합시다.

1. 2 일분의 업무를 모으십시오.
2. 작업 지속 시간을 분이 아닌 시간으로 변환하십시오.
3. 2 시간 이상 걸린 모든 것을 걸러 낸다.
4. 다 합친 것.
5. 결과에 결제 시간당 요금을 곱하십시오.
6. 형식화 된 금액을 출력하십시오.

먼저 월요일과 화요일에 작업을 정의 해 보겠습니다.

```javascript
var monday = [
        {
            'name'     : 'Write a tutorial',
            'duration' : 180
        },
        {
            'name'     : 'Some web development',
            'duration' : 120
        }
    ];
 
var tuesday = [
        {
            'name'     : 'Keep writing that tutorial',
            'duration' : 240
        },
        {
            'name'     : 'Some more web development',
            'duration' : 180
        },
        {
            'name'     : 'A whole lot of nothing',
            'duration'  : 240
        }
    ];
     
var tasks = [monday, tuesday];
```

그리고 이제, 우리의 아름다운 모습의 변화 :

```javascript
var result = tasks.reduce(function (accumulator, current) {
                    return accumulator.concat(current);
                }).map(function (task) {
                    return (task.duration / 60);
                }).filter(function (duration) {
                    return duration >= 2;
                }).map(function (duration) {
                    return duration * 25;
                }).reduce(function (accumulator, current) {
                    return [(+accumulator) + (+current)];
                }).map(function (dollar_amount) {
                    return '$' + dollar_amount.toFixed(2);
                }).reduce(function (formatted_dollar_amount) {
                    return formatted_dollar_amount;
                });
```

또는 더 간결하게 :

```javascript
                  // Concatenate our 2D array into a single list
var result = tasks.reduce((acc, current) => acc.concat(current))
                  // Extract the task duration, and convert minutes to hours
                  .map((task) => task.duration / 60)
                  // Filter out any task that took less than two hours
                  .filter((duration) => duration >= 2)
                  // Multiply each tasks' duration by our hourly rate
                  .map((duration) => duration * 25)
                  // Combine the sums into a single dollar amount
                  .reduce((acc, current) => [(+acc) + (+current)])
                  // Convert to a "pretty-printed" dollar amount
                  .map((amount) => '$' + amount.toFixed(2))
                  // Pull out the only element of the array we got from map
                  .reduce((formatted_amount) =>formatted_amount);
```

당신이 그것을 멀리 만들었다면, 이것은 꽤 간단해야합니다. 그래도 설명 할 수있는 이상한 점이 두 가지 있습니다.

첫째, 10 행에 다음과 같이 써야합니다.

```javascript
// Remainder omitted
reduce(function (accumulator, current) {
    return [(+accumulator) + (+current_];
})
```


여기에서 설명 할 두 가지 사항은 다음과 같습니다.

1.`accumulator`와`current` 앞에있는 더하기 기호는 값을 숫자에 강요합니다. 이렇게하지 않으면 반환 값은 쓸모없는 문자열 인 `"12510075100 "`이됩니다.
2. 그 합을 괄호로 묶지 않으면 `reduce` 는 배열이 아니라 단일 값을 뱉어냅니다. 배열에`map` 만 사용할 수 있기 때문에 결국 `TypeError` 를 던질 것입니다!
    

약간의 불편 함을 줄 수있는 두 번째 비트는 마지막 `reduce` 입니다.

```javascript
// Remainder omitted
map(function (dollar_amount) {
    return '$' + dollar_amount.toFixed(2);
}).reduce(function (formatted_dollar_amount) {
    return formatted_dollar_amount;
});
```


`map`에 대한 호출은 단일 값을 포함하는 배열을 반환합니다. 여기서 우리는`reduce`를 호출하여 그 값을 제거합니다.

이를 수행하는 다른 방법은`reduce '호출을 제거하고`map`이 꺼내는 배열에 인덱스하는 것입니다 :

```javascript
var result = tasks.reduce(function (accumulator, current) {
                    return accumulator.concat(current);
                }).map(function (task) {
                    return (task.duration / 60);
                }).filter(function (duration) {
                    return duration >= 2;
                }).map(function (duration) {
                    return duration * 25;
                }).reduce(function (accumulator, current) {
                    return [(+accumulator) + (+current)];
                }).map(function (dollar_amount) {
                    return '$' + dollar_amount.toFixed(2);
                })[0];
```


그 말이 맞습니다. 배열 색인을 사용하는 것이 더 편한 경우 바로 이동하십시오.

그러나 나는 당신이하지 않기를 권합니다. 이러한 함수를 사용하는 가장 강력한 방법 중 하나는 배열 프로그래밍을 자유롭게 사용할 수없는 반응 형 프로그래밍의 영역에 있습니다. 그 습관을 버리면 이제는 반응 기술을 훨씬 쉽게 배우게됩니다.

마지막으로, 우리의 친구 인 `forEach` 루프가 어떻게 완성되는지 봅시다.

```javascript
var concatenated = monday.concat(tuesday),
    fees = [],
    formatted_sum,
    hourly_rate = 25,
    total_fee = 0;
 
concatenated.forEach(function (task) {
    var duration = task.duration / 60;
     
    if (duration >= 2) {
        fees.push(duration * hourly_rate);
    }
});
 
fees.forEach(function (fee) {
    total_fee += fee
});
 
 
var formatted_sum = '$' + total_fee.toFixed(2);
```

참을 수는 있지만 시끄 럽습니다.

## Conclusion & Next Steps


이 튜토리얼에서는`map`,`filter` 및`reduce` 작업을 배웠습니다. 그들을 사용하는 방법; 그리고 대략 어떻게 구현되고 있는지를 보여줍니다. 여러분은`for`와`forEach` 루프를 사용하여 상태를 변경하는 것을 피할 수 있다는 것을 보았습니다. 이제는 모두 함께 연결하는 방법에 대한 좋은 아이디어를 얻었어야합니다.

지금까지, 나는 당신이 연습과 추가 독서에 열망하고 있다고 확신합니다. 여기에 다음으로 향할 위치에 대한 세 가지 주요 제안 사항이 있습니다.

1. JavaScript의 [Functional Programming in JavaScript](http://reactivex.io/learnrx/)에 대한 [Jafar Husain 's](https://twitter.com/jhusain) 의 훌륭한 연습 세트. [Rx. js](https://github.com/Reactive-Extensions/RxJS)에 대한 확실한 소개가 있습니다.
2. Envato Tuts + 강사 [Jason Rhodes'](https://tutsplus.com/authors/jason-rhodes)의 [JavaScript의 기능 프로그래밍 강좌](https://code.tutsplus.com/courses/functional-programming- in-javascript)
3. 우리가 왜 돌연변이를 피하는 이유에 대해 깊이있는 [기능 프로그래밍에 대한 가장 적절한 가이드](https://drboolean.gitbooks.io/mostly-adequate-content/content/ch1.html)과 일반적으로 기능적 사고


JavaScript는 웹에서 작동하는 사실상의 언어 중 하나가되었습니다. 그것은 학습 곡선 없이는 아니에요,뿐만 아니라 당신을 바쁘게 유지하는 프레임 워크와 라이브러리가 많이 있습니다. 공부하거나 직장에서 사용할 추가 리소스를 찾고 있다면 [Envato 마켓 플레이스](https://codecanyon.net/category/javascript)에서 제공되는 것을 확인하십시오.

이런 종류의 일을 더 원한다면 [수시로 내 프로필 확인](https://tutsplus.com/authors/peleke-sengstacke)을 클릭하십시오. 트위터에 나를 붙잡아 라 ([@PelekeS](http://twitter.com/PelekeS)); 또는 [http://peleke.me.](http://peleke.me/)에서 내 블로그를 조회하십시오.

질문, 의견 또는 혼란? 아래에 남겨두면 최선을 다해 개별적으로 돌아갈 것입니다.

**Learn JavaScript: The Complete Guide**

시작하기 만하면됩니다 [자바 스크립트 배우기](https://code.tutsplus.com/series/learn-javascript-the-complete-guide--cms-1112)하는 데 도움이되는 완전한 안내서를 작성했습니다. 웹 개발자로서 또는 더 고급 주제를 탐험하고 싶습니다.
