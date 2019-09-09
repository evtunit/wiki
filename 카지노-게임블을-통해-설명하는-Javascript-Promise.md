> 원본: https://blog.codeanalogies.com/2018/08/26/javascript-promises-explained-by-gambling-at-a-casino/

도박을 하거나 도박에 관한 영화를 본 적이 있다면 자바스크립트의 `Promise`를 쉽게 이해할 수 있다.

우리는 자바스크립트의 비동기적 기능을 좋아한다(?). 그리고 우리는 '지옥의 피라미드'처럼 생긴 코드를 얻게 된다.

<br>

![](https://i2.wp.com/blog.codeanalogies.com/wp-content/uploads/2018/08/callbackpic.png?w=626&ssl=1)

<br>

위의 이미지는 `콜백 지옥`이라고 알려져 있는데, 그 누구도 이 코드를 다시 읽고 모든 것이 어떻게 작동하는지, 어떤 순서로 작동하는지 이해가 잘 안되기 때문이다

위의 예에서는 몇 가지 어려운 점이 있다.

- 명확한 오류 처리. 일이 잘못되면 어떻게 될까?
- 각 기능은 이전 기능에 따라 달라진다. 비동기식 스타일을 필요로 하지 않는다. 코드를 읽으면서 다른 사람들에게 주문을 분명히 하고 싶어한다. 당신이 이 많은 기능을 함께 묶으면, 동기식 코드는 더 읽기 쉬울 것이다.
- 함수에 입력하기 위한 변수를 지속적으로 추적한 다음 출력해야 한다. 또한 각 출력에 발생하는 논리를 추적한다. 이것은 지치게 된다.

<br>

Promise을 이용하여 이 모든 과정을 더 이해할 수 있게 할 수 있다. Promise에 대해 한두 번 들어봤을지 모르지만, 사실 나는 어려워보여서 이 구문을 무시하곤했다. 만약 당신이 콜백을 이해한다면, Promise의 기본적인 용도는 사실 꽤 쉽다.

Promise들은 명확한 코드를 쓰고 두통이 없이 모든 단계를 이해할 수 있도록 해주는 단일한 목적의 직설적인 기능들을 장려한다. 잠시 생각해 본 결과, Promise은 꼭 카지노 여행과 같다는 것을 깨달았다. 카지노는 당신의 은행 계좌에 있는 돈의 액수를 "수정"하는 반면, 일련의 약속들은 특정한 순서로 데이터를 수정한다.

<br>

![](https://i2.wp.com/blog.codeanalogies.com/wp-content/uploads/2018/08/PromiseCasinoDiagram.jpg?w=1460&ssl=1)

<br>

자, 콜백에 대한 경험이 없다면 [콜백 원리](https://blog.codeanalogies.com/2016/04/11/javascript-callbacks-explained-using-minions/)에 대한 나의 설명을 확인하십시오. Promise에 대한 보다 기술적인 설명을 원하는 경우 본 가이드, 본 가이드 또는 이 [비디오](https://www.youtube.com/watch?v=obaSQBBWZLk)를 참조하십시오.

<br>

## What is a promise?

주말 휴가를 카지노로 간다고 하자. 당신은 2주간의 봉급을 주머니에 가지고 있고, 마지막 10센트까지, 당신이 내기를 하면서 매 순간을 즐기게 될 것이다. 아니면 당신은 운이 좋아서 결국 돈을 벌게 될 것인가?

호텔 방에 가서 카지노로 가 각 게임 종류는 현금을 받기 때문에 당신은 현금인출기에 가서 1,000달러를 인출하고 시작해야 할 것이다.

한 걸음 물러서서 이 시나리오를 생각해 보자. 비록 현금이 카지노 외부의 어떤 것에든 사용될 수 있지만, 그것은 그 안에 있는 한 가지, 즉 돈이 다 떨어지기 전에 남겨둔 게임의 수라는 것을 의미한다. 그 현금 금액은 주말 동안 점점 더 줄어들 것 같다. 그것은 또한 성장할 수 있지만, 당신은 이미 이번 주말에 1000달러 이상을 잃지 않겠다고 스스로에게 약속했다.

<br>

![](https://i1.wp.com/blog.codeanalogies.com/wp-content/uploads/2018/08/PromiseInitDiagram.jpg?w=1460&ssl=1)

<br>

위의 다이어그램에서 게임 간에 남은 돈이 어떻게 전달되는지 알아?

Promise는 아직 존재하지 않지만 미래에 분명히 존재할 가치의 자리를 차지하고 있다. 이것은 당신이 어떤 기능을 명확하게 따르고 그것의 시작과 끝을 이해할 수 있게 해준다. 위와 같이 Promise는 연속적인 비동기 함수에 명확성을 부여하고 입력과 출력을 명확히 하는 훌륭한 방법이다.

하나의 비동기 함수의 산물을 다음 함수에 직접 전달한다. 이전 함수가 값을 반환하는 즉시 그 함수가 시작될 것이다. 또는 오류가 반환되면 다른 기능을 실행하게 된다. 우리는 나중에 그 만일의 사태에 대비할 수 있다.

<br>

## Creating Your FirstcPromise

실제로 Promise에는 생산자와 소비자 두 종류가 있다.

생산자는 연쇄상에서의 첫 번째 Promise이고, 소비자들은 연쇄상에서의 이전의 Promise의 결과를 기다린다. 이 경우 게임을 하려면 돈이 필요하기 때문에 (확실히) ATM에 가는 것이 생산자다.

또한 Promise은 다음 세 가지 상태 중 하나를 가질 수 있다.

1. `Pending` - 아직 완료되지 않음
2. `Fulfilled` - Promise가 완료되어 있음
3. `Rejected` - Promise가 오류로 완료되었거나 실패함.

그래서 만약 당신이 ATM을 방문해서 당신이 의도했던 수술을 끝내지 못한다면... 음, 당신은 당신의 은행 계좌에 1000달러를 가지고 있지 않을 수도 있고 카지노에서 즉시 나가야만 한다. 만약 당신이 1000달러를 성공적으로 인출한다면, 당신은 값을 반환할 것이다.

![](https://i0.wp.com/blog.codeanalogies.com/wp-content/uploads/2018/08/ProducerDiagram.jpg?w=1105&ssl=1)

<br>

그럼 이걸 코드로 바꾸자. 여기 Promise 구문이 있다.

<br>

```js
let withdraw = new Promise(function(resolve,reject){
 
  let amount = visitATM(1000);
  return resolve(amount)
});

```

그리고 여기 그 코드의 대화형 버전이 있다.

![](https://blog.codeanalogies.com/wp-content/uploads/h5p/content/37/images/image-5b8340dc0a235.jpg)

<br>

다른 비동기 코드와 마찬가지로, 이 접근법은 당신의 코드가 방문 ATM 기능의 상태를 기다릴 수 있게 해준다. 그것이 완성되지 않았다면 계속하는 것은 의미가 없다!!

## Chaining Multiple Promises

당신이 카지노에 있는 동안 슬롯, 포커, 룰렛을 하고 싶다고 가정해보자. 각각 현금으로 구입하도록 요구한다. 물론 포커에 너무 많은 돈을 걸어서 다 떨어지면 다음 게임 중 어떤 게임도 할 수 없을 것이다.

슬롯을 먼저 재생하고 싶다고 합시다.

<br>

```js
let withdraw = new Promise(function(resolve,reject){ 
 
  let amount = visitATM(1000); 
 
  return resolve(amount) 
}); 
 
withdraw.then(function(amount){
  let slotResults = playSlots(amount, 100);
   
  if(slotResults <= 0)
    throw err;
     
  return slotResults;
});
```


Promise는 이전 Promise이 해결되거나 완료된 후에 어떤 일이 일어나야 하는지를 보여주기 위해 `.then` 구문을 사용한다. 이 경우, Promise의 최종 결과는 금액 내에 포함되어 있다.

그래서, 우리가 `.then()`을 사용하여 다음 Promise을 정할 때, 우리는 또한 그 이전 결과에 해당하는 논쟁 금액을 명명한다.

또 다른 중요한 메모-플레이슬롯은 꾸며낸 function이다. 우리는 그것이 두 가지 주장, 즉 당신이 가진 돈의 총액과 도박을 할 의향이 있는 액수를 필요로 한다고 상상하고 있다.

<br>
![](https://i1.wp.com/blog.codeanalogies.com/wp-content/uploads/2018/08/promisev2.jpg?w=1460&ssl=1)