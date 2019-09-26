# 자바스크립트의 큐

> 원문: [The Little Guide of Queue in JavaScript](https://hackernoon.com/the-little-guide-of-queue-in-javascript-4f67e79260d9)

큐는 간단한 **데이터 구조**이다. 요소를 뒤쪽(tail)에서 추가 앞쪽(head)에서 삭제할 수 있다.

이 동작을 선입 선출 (**F**irst **I**n **F**irst **O**ut )이라고 한다.

[[/resource/yongkwan/28/01.png]]

큐는 선형(linear) 데이터 구조이다. 매우 중요한 개념은 **가장 오래된** 요소를 먼저 삭제한다는 것이다.

[[/resource/yongkwan/28/02.png]]

## 응용

-   큐는 첫 번째 항목부터 순서대로 객체를 관리해야 할 때마다 사용된다.
-   예를들어 프린터의 문서 출력, 대기자들게 차례대로 응답하는 콜 센터 시스템등이 있다.

## 생성

-   **배열** 또는 **링크드 리스트** 사용하여 큐를 구현할 수 있다.

자바스크립트에서는 *shift* 및 *pop*과 같은 배열 메서드를 사용할 수 있으므로 *큐**를 만드는 것은 매우 간단하다.

### 기억하기

- `unshift`: 요소를 배열의 맨 앞에 추가한다.
- `pop`: 배열의 마지막 요소를 제거한다.

### 구현하기

먼저 가장 먼저 할 일은 빈 배열을 가진 큐 생성자 함수를 만드는 것이다.

```js
function Queue() {  
	this.data = [];  
}
```
`this`는 우리가 만든 객체를 가리켜야 하기 때문에  사용한다는 것을 기억하라.

### 메서드 

주요 메서드는 *add*와 *remove*가 있다.

```js
Queue.prototype.add = function(record) {  
  this.data.unshift(record);  
}

Queue.prototype.remove = function() {  
  this.data.pop();  
}
```

추가로 3개의 보조 메서드를 추가한다.

```js
Queue.prototype.first = function() {  
  return this.data[0];  
}

Queue.prototype.last = function() {  
  return this.data[this.data.length - 1];  
}

Queue.prototype.size = function() {  
  return this.data.length;  
}
```

그러면 아래와 같은 결과를 얻을 수 있다.

```js
const q = new Queue():  
q.add(1);  
q.add(2);  
q.add(3);  
console.log(q);
```

[[/resource/yongkwan/28/03.png]]

가장 오래된 요소는 1을 제일 먼저 추가했기 때문에 **1**이다. 

못 믿겠으면 *last* 메서드를 사용하여 확인 할 수 있다.

```js
console.log(q.first());  
// -> 3
console.log(q.last());  
// -> 1
```

또한 *remove* 메서드를 사용하면 가장 오래된 요소인 **1**이 제거된다.

```js
q.remove();  
console.log(q);
```

[[/resource/yongkwan/28/04.png]]

마지막으로 *size* 메서드를 호출한 결과는 아래와 같다.

```js
console.log(q.size())  
// -> 2
```

마지막 요소를 제거하고 호출했기 때문에 **2**가 반환된다.

완전한 예제: [https://github.com/germancutraro/Queue-Data-Structure](https://github.com/germancutraro/Queue-Data-Structure)