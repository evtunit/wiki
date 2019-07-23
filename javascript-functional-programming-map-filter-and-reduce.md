# 자바스크립트 함수형 프로그래밍 — map, filter 그리고 reduce

> 원문: [JavaScript Functional Programming — map, filter and reduce](https://medium.com/jsguru/javascript-functional-programming-map-filter-and-reduce-846ff9ba492d)

여러분이 함수형 프로그래밍이 무엇인지 모르더라도, 아마도 map, filter, 그리고 reduce를 사용해 봤을 것이다. 이것들은 매우 유용하고 더 깨끗한 로직을 작성할 수 있기 때문이다.

> 이 기사의 제목은 '자바스크립트 함수형 프로그래밍'이지만, 그 주제에 너무 깊이 빠져들지 않을 것이다. 우리는 map, filter 그리고 reduce를 살펴 볼 것이다. 이것은 기본적으로 함수형이라 불리는 코딩의 정말 멋진 세계에 대한 인식을 높이기 위한 마케팅 수법이다. 아마 들어본 적도 없을 것이고, 들은 적도 없을 것이지만, 생각해 본 적도 없을 것이다.
> 
> 함수형 프로그래밍에 대한 자세한 내용은 Frisby 교수의 가장 적절한 기능 프로그래밍 가이드를 참조하라. ([Link](https://drboolean.gitbooks.io/mostly-adequate-guide/content/?source=post_page---------------------------))
>  또는
>  Functional-Light JavaScript by the awesome Kyle Simpson ([Link](https://github.com/getify/Functional-Light-JS?source=post_page---------------------------))
>  또는
>  전설인 남자, [에릭 엘리엇](https://medium.com/@_ericelliott)의 글을 보라. 함수형 프로그래밍에 대해 자주 이야기한다.
>   
>  이 개념들 중 일부가 처음 접했을 때 이해가되지 않는다고 해도, 걱정하지 말아라. 그것은 당연한 것이다. 계속하면 곧 그 보상을 얻게 될 것이다. 내 생각에 여기서 가장 중요한 것은 당신이 더 나은 자바스크립트 코드를 쓰는 데 도움이 될 새로운 접근법을 소개하는 것이다.

## map

MDN이 말하는 'map'이 무엇인지 살펴보자 ([Link](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map)):

### 설명

`map()`  메소드는 배열의 모든 요소에 제공된 함수를 적용하여, 결과로 새 배열을 생성한다.

### 예제

```js
const numbers = [2, 4, 8, 10];  
const halves = numbers.map(x => x / 2);// halves is [1, 2, 4, 5]
```

특별할 것이 없다. 단지 *numbers* 배열의 값을 검토하여, 절반으로 나누고 그 결과로 새로운 배열 (*halves*)을 만들었다.

`map`은 배열이 존재하고, 그 배열의 모든 항목에 대해 무언가를 하고 싶을 때 사용된다.

## filter

MDN이 말하는 'filter'가 무엇인지 살펴보자 ([Link](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)):

### 설명

`filter()`  메소드는 배열의 모든 요소에 제공된 함수의 조건을 만족하는지 판단하여, 결과로 새 배열을 생성한다.

### 예제

```js
const words = ["spray", "limit", "elite", "exuberant", "destruction", "present"];  
  
const longWords = words.filter(word => word.length > 6);// longWords is ["exuberant", "destruction", "present"]
```

`map`과 마찬가지로 `filter` 역시 특별할게 없다. *words* 배열에서 어떤 값이 조건에 맞는지 확인하여 (길이가 6 이상), 해당 값들을 새 배열에 담아 반환한다. 

## reduce

reduce는 다재다능하고 유연하다. 수 많은 시나리오에 사용될 수 있다. 그러나 대체로 숫자 배열의 합을 구하거나 특정 속성에 합계를 구하는 것에 사용되는데, 안타까운 일이다.

MDN이 말하는 'reduce'가 무엇인지 살펴보자 ([Link](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)):

### 설명

`reduce()`  메소드는 배열의 각 요소(왼쪽에서 오른쪽으로)에 대해 누산기(accumulator)와 함께 함수를 적용하여 단일 값으로 감소시킨다.

### 예제

```js
const total = [0, 1, 2, 3].reduce((acc, value) => acc + value, 1);// total is 7
```

가장 일반적인 예시는 합을 구하는 것이다. 그러나 개발자들로 부터 알아차린 것은 어떤 숫자들을 합할 일이 별로 없다는 것이다. 그래서 `reduce`가 존재한다는 것을 잊거나 혹은 어떤 숫자들을 합해야 할 때에만 존재한다는 것을 기억하지만 (앞에서 암시했던 것처럼) `reduce`는 단순히 합한 것 이상의 것을 할 수 있다.

`reduce`의 정말 멋진 점은 콜백 함수의 호출의 결과를 다음 콜백 함수의 호출로 전달하여 **속임수** 같이 동작할 수 있게 한다.

### Compose Functions (함수의 합성)
 
> `reduce`를 이용하여 함수를 합성하는 예제는 Eric Elliott이 쓴 reduce 글에서 발췌하였다. Eric Elliott은 항상 양질의 컨텐츠를 생산하고 가능한 모든 플랫폼에서 그를 따랐으면 좋겠다. [Link](https://medium.com/javascript-scene/reduce-composing-software-fe22f0c39a1d)

우리에게 가장 흥미로운 부분은 다음과 같다.

```js
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);
/*
const compose = (...fns) => {
	return (x) => {
		return fns.reduceRight((v, f) => f(v), x)
	}
};
*/
```

마치 시 처럼 보이지 않는가?

*compose*는 커링 패턴 형태로써 부분적으로 적용되며, 그 결과를 다음 함수로 전달한다. **rest parameters** 가 사용된 것을 주목하라. 때문에 원하는 만큼의 함수를 전달 하여 *fns* 배열에 넣을 수 있는 것이다.

그런 compose에 함수들을 전달한 다음 초기 값 (*x*)를 입력하면 된다.

수집된 함수의 배열(*fns*)에 *x*를 초기값으로 전달하여 [reduceRight](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)를 호출하여 반환한다. 

**reduceRight**는 reduce와 기능은 같지만 배열의 끝에서 시작하여 시작점으로 이동한다. 

### 사용 예: 

```js
const add1 = n => n + 1;  
const double = n => n * 2;const add1ThenDouble = compose(  
  double,  
  add1  
);add1ThenDouble(2); // 6  
// ((2 + 1 = 3) * 2 = 6)
```

훌륭하다!

### 프로미스 체이닝

> reduce를 이용한 프로미스 체인 예제는 [Bobby Brennan](https://medium.com/u/974e7427ad58)의 프로미스 패턴과 안티-패턴에 관한 훌륭한 글에서 발견했다.

재미있는 부분 :

```js
let itemIDs = [1, 2, 3, 4, 5];
itemIDs.reduce((promise, itemID) => {  
  return promise.then(_ => api.deleteItem(itemID));  
}, Promise.resolve());
```

위 코드는 아래와 같이 해석된다 : 

```js
Promise.resolve()  
.then(_ => api.deleteItem(1))  
.then(_ => api.deleteItem(2))  
.then(_ => api.deleteItem(3))  
.then(_ => api.deleteItem(4))  
.then(_ => api.deleteItem(5));
```

이 예제는 복잡하지도 않고 간단하다. reduce를 하면 코드가 엄청 간결해진다.  40개 이상의 ID를 가진 배열이 있고 reduce를 사용하지 않는다고 상상해보라. 

## 결론

map, filter, reduce를 정말 멋지고 창의적인 방법으로 사용하는 걸 봤다. 멋지고 창의적인 예들은 reduce를 위한 것이었을 뿐이지만..
만약 map, filter, reduce가 사용된 흥미로운 방법들을 발견하면 공유해 주길 바란다. 이 3가지를 사용할 수 있는 실용적인 방법들이 많이 있다고 확신한다!