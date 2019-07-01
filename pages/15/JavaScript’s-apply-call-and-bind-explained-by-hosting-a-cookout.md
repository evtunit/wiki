# 자바스크립트의 apply, call, bind를 요리해보자.

원문 : https://dev.to/kbk0125/javascripts-apply-call-and-bind-explained-by-hosting-a-cookout-32jo

**만약 가족 행사 또는 파티에 그릴을 사용해본 적이 있다면, 자바스크립트에서 apply, call, bind에 대해 이해할 수 있습니다.**

나중에 혹은 팀원이 다시 읽을 수 있는 명확한 코드를 작성하려면 다음과 같은 공통 규칙 중 하나를 따르세요. : 절대 반복하지 마세요.

반복적으로 메서드나 함수를 만든다면, 코드는 더 유지하기가 어려워집니다.  동일한 코드를 여러 버젼을 업데이트 하는 것을 잊지 않아서 버그를 만들 수 있습니다.

만약 자바스크립트의 개념에 대해 확고한 이해가 있다면, 실행 컨텍스트를 추적하려고 할 때 더욱 어려울 수 있습니다.  함수와 객체 사이의 관계 입니다.

보다 깨끗한 코드를 작성하기 위해서는 apply, call, bind 메서드를 사용하여 목적에 맞게 실행 컨텍스트를 조작할 수 있습니다. 서로 다른 객체는 각각 객체마다 재작성하지 않고 메서드를 공유할 수 있습니다.

Apply, call, and bind은 함수와 함께 호출되기 때문에 **function methods**로 불리기도 합니다. 

만약 더 자세한 내용을 찾고 있다면 [JavaScriptIsSexy](http://javascriptissexy.com/javascript-apply-call-and-bind-methods-are-essential-for-javascript-professionals/)  사이트를 추천합니다.

### 어떻게 요리하는 걸까요?

이 세가지 방법은 일종의 요리 기술을 적용하여 요리를 위한 음식을 준비하는 것과 같습니다.

1. 언제든지 요리하고 모두를 행복하게 만들 수 있는 일반 식사 (파스타와 소스)
2. 파티일 수도 있는 요리법(버거, 핫도그 등)
3. 너와 너의 파트너를 위한 멋진 만찬(생선과 와인)
4. 행사를 위한 디저트 만들기 (케이크)

각각 서로 다른 조리 기술이 필요합니다. 일부는 개별적인 상황에 고유한 반면 다른 것들은 보다 일반화되어 있습니다.  더 많은 것을 1분안에 설명할 것이다. 

이 경우 각 요리 컨텍스트는 객체와 비슷합니다. 예를 들어, 그릴을 요리 할 것이라고 말하면 그릴을 작동시키는 것과 같은 몇 가지 기술이 있음을 의미합니다!

따라서 각 조리 기술에 대해 개별적인 메서드를 사용할 경우 각 객체에 고유한 메서드가 있고 메서드가 여러 객체에 적용될 수 있는 경우가 있습니다.

```javascript
function boilWater(){};
let generalMeal = "pasta"; 

let cookout = {
  grill: function() {}
} 

let fancyDinner = {
  useOven: function() {}
} 

let dessert = {
  bakeCake: function() {}
}
```



[![DiagramCookingContext](https://res.cloudinary.com/practicaldev/image/fetch/s--f1_q3Nvy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/diagramcookingcontext.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f1_q3Nvy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/diagramcookingcontext.png)

위의 코드에서 boiling water는 모든 상황에서 적용될 수 있는 일반적인 기술입니다.

예제를 사용해봅시다. grill() 메서드는 cookout객체, 즉 조리법을 들고 있다면 그릴 기술을 불러야합니다.



하지만 기다리세요. 요리가 끝났을 때 그릴을 사용하는 방법을 잊지마세요. fancyDinner와 같은 멋진 저녁 식사를 위해 스테이크를 요리하고 싶은 파트너라고 가정해봅시다. cookout객체에서 grill() 메서드를 빌릴 수 있기를 원합니다. 그것이  apply, call, bind이 들어오는 곳입니다.

조리 기술 (메서드)와 조리 컨텍스트 간의 이러한 관계 (객체)가  apply, call, and bind()를 사용하는 방법을 보여주는 주요 방법이 될 것입니다.

이 자습서를 이해하려면 Javascript에서 this를 이해해야합니다. 검토가 필요한 경우  [tutorial on JavaScript’s this](https://blog.codeanalogies.com/2018/03/12/javascripts-this-explained-by-starting-a-high-school-band/) 을 확인하세요.

### Bind Method 소개

아들 또는 딸의 10번째 생일 파티를 위해 요리를 하고 있다고 상상해 보세요. 닭고기, 햄버거, 스테이크 등 모두 만족시키기 위해 그릴에 3가지 종류의 고기를 조리하고 싶습니다. 그들은 모두 파티에서 모두 육식 동물이다.



그러나 당신은 각 개인이 원하는 것이 무엇인지 전혀 모릅니다. 그래서 각 참석자에게 파티에 도착할 때마다 물어볼 필요가 있습니다. 각 유형의 고기는 일반적으로 동일한 단계가 필요합니다.

1. 조미료 추가
2. 그릴에 올려놓기
3. 일정 시간이 지난 후에 그릴에서 제거하기

따라서 각 유형의 고기에 별도의 메소드를 쓰는 것은 중요하지 않습니다. 유일하게 변하는 것은 조리 시간입니다. 햄버거는 15분, 닭은 20분, 스테이크는 10분이 소요 됩니다.

이러한 모든 유형의 고기에 대해 동일하게 일반적인 과정을 사용하기를 원합니다. 세부 사항은 다양합니다.

“함수를 위한 좋은 시간입니다.” 라고 생각할 수도 있습니다. 그러나 그것보다 조금 더 복잡합니다. 위에서 언급했듯이 요리 실력을 보여주기 위해 실행컨텍스트 개념을 사용하려고 합니다. 파티 전체를 위해 처음으로 햄버거, 닭고기 및 스테이크를 요리하고 싶지는 않을 것입니다. 따라서 우리는 수년간의 요리를 통해 얻은 기술과 이 하나의 특정 시나리오에 어떻게 적용할 것 인지를 보여 주어야 합니다.

```
let cookout={
  grill: function(duration, meat, name){
    console.log("Thanks " + name + "! Your " + meat + "will be ready in " + duration + "minutes.");
  }
}
```

[![grillMethod1.png](https://res.cloudinary.com/practicaldev/image/fetch/s--gzsUnrq3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/grillmethod1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gzsUnrq3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/grillmethod1.png)

이러한 경우 우리의 grill 메서드는 개인의 음식이 어떻게 준비되는지에 대한 문장을 기록합니다. bind를 사용하여 실행컨텍스트를 저장하려고 합니다. 명확하게 말하면 실행 컨텍스트에는 두 가지 중요한 세부 사항이 있습니다.

1. 올바르게 객체를 사용하기 위해 *cookout* 객체에 대한 참조
2. 조리 시간(분)

```
let cookBurger = cookout.grill.bind(cookout, 15, "burger"); 
let cookChicken = cookout.grill.bind(cookout, 20, "chicken"); 
let cookSteak = cookout.grill.bind(cookout, 10, "steak");
```

이것은 다양한 종류의 고기를 요리하는 방법에 대한 우리의 기존 지식을 나타냅니다. 각각의 경우에 객체와 분의 값을 저장하므로 모든 참석자에 대한 요청을 신속하게 처리할 수 있습니다.



각 변수(cookBurger, cookChicken, cookSteak)는 인수가 하나 더 있으면 언제든지 실행할 수 있는 새 기능인 사람의 이름입니다. 그래서 여기에 세 사람과 음식 요청이 있습니다.

1. Jack은 햄버거를 원합니다.
2. Jill은 스테이크를 원합니다.
3. David는 치킨을 원합니다.

새로운 함수를 사용하여 grill 메서드를 재작성 하지 ㅇ낳고도 신속하게 이러한 요청을 처리할 수 있습니다. 아래의 각 예제는 함수가 cookout 객체의 컨텍스트에서 실행하는 데 필요한 마지막 인수를 취합니다.

```
cookBurger("Jack")
// Thanks Jack! Your burger will be ready in 15 minutes. 

cookSteak("Jill")
// Thanks Jill! Your steak will be ready in 10 minutes. 

cookChicken("David")
// Thanks David! Your chicken will be ready in 20 minutes.
```

[![bindExamplecook1.png](https://res.cloudinary.com/practicaldev/image/fetch/s--x8wYMsnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/bindexamplecook1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x8wYMsnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/bindexamplecook1.png)

여기에서 bind 메서드를 사용할 수 없다면 상상해보세요! 파티가 시작되었을 때 처음으로 햄버거, 닭고기, 스테이크를 요리하는 것처럼 보일 것입니다. 이전 계획이 없는 일반 grill() 메소드에 세 가지 인수를 제공합니다.

대신에 우리는 부분 함수 애플리케이션을 사용하여 각 유형의 고기를 요리하는 방법을 보여줍니다. 각 개별 손님이 먹고 싶어 하는 것을 들으면 됩니다. 이러한 구분은 실제 요리 경험을 나타냅니다.

### Call 메서드 소개

다른 시나리오가 있습니다. 당신과 당신의 배우자가 멋진 저녁을 요리할 때 보통 어떤 종류의 생선과 와인을 만들고 싶다고 가정합시다. 첫번째 코드 스니펫에서 볼 수 있듯이 보통 오븐에서 생선을 요리하는 것이 좋습니다. 

그러나 어느 날 밤에 스테이크를 만들고 싶다고 결정했습니다. 분명히 그 스테이크를 만들기 위해 그릴을 사용해야할 것입니다.

[![steakWine](https://res.cloudinary.com/practicaldev/image/fetch/s--sQhMnqLn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/steakwine.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sQhMnqLn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/steakwine.png)

문제는 다음과 같습니다. grill() 메소드는 cookout 객체 컨텍스트에 있습니다. 하지만 이제  fancyDinner 객체 내에서 요리 기술을 사용하려고 합니다.  grill 메서드는 다시 사용하고 싶지 않습니다. 코드를 유지하기가 더 어려워질 것 입니다.

대신, Javascript의 call 메서드를 사용하여 fancyDinner 객체의 컨텍스트 내에서 grill 메서드를 호출 할 수 있습니다. 이 새로운 컨텍스트를 사용하면 다시 작성할 필요가 없습니다. 세부 사항에 들어가기 전에 전체 코드가 있습니다.

```
let cookout = {
  drink:"soda",
  grill: function(meal) {
   console.log("I am going to fire up the grill to cook " + meal + " with " +this.drink +" to drink!");
  }
} 

let fancyDinner = {
  drink: "wine",
  useOven: function() {}
}
```

[![firstVcallmethod](https://res.cloudinary.com/practicaldev/image/fetch/s--7Nul2rpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/firstvcallmethod.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Nul2rpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/firstvcallmethod.png)

그래서 cookout의 기본 음료는 소다이며, 멋진 저녁 식사를 위한 기본 음료는 와인입니다. 이제 우리는 call() 메서드(스테이크)에서 예외의 사항들을 인수로 추가하기만 하면 됩니다. 다음은 메소드를 정상적으로 사용하는 것과 call()을 사용하는 것의 차이점입니다.

```
cookout.grill("steak");
// "I am going to fire up the grill to cook steak with soda to drink!"

cookout.grill.call(fancyDinner, "steak");
// "I am going to fire up the grill to cook steak with wine to drink!"
```

[![callmethodsummary2.png](https://res.cloudinary.com/practicaldev/image/fetch/s--nYqIayIK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/callmethodsummary2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nYqIayIK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/callmethodsummary2.png)

첫번째 예제는 매우 간단해야 합니다.: 그것은 모두 cookout객체의 컨텍스트에 있습니다. 그러나 두번째 예에서 첫 번째 인수는 this의 컨텍스트를 fancyDinner객체로 변경했습니다.

grill() 메서드 내에서 console.log문으로 이동하면 this.drink 뿐만 아니라 meal이라는 단일 인수를 참조하는 것을 볼 수 있습니다.

fancyDinner를 호출 메서드의 첫 번째 인수로 사용하면 컨텍스트가 fancyDinner 객체로 설정이 됩니다. 이제는 다른 상황에서 그 굽기 기술을 사용할 수 있습니다.

### Apply 메서드 소개

단 하나의 중요한 차이점을 제외하고 apply()메서드는 call()과 매우 비슷합니다. 개별 매개 변수를 선언한느 대신 인수 배열을 사용할 수 있습니다. 즉 가변적인 함수를 만들 수 있습니다. 즉, 여러 개의 인수가 있는 함수를 만들 수 있습니다. 따라서 컨텍스트와 인수 배열의 두 매개 변수만 허용할 수 있습니다.

원래 생일 파티의 예제로 돌아가봅시다. 아들 또는 딸의 10번째 생일 파티를 위해 요리를 하고 있습니다. 12명의 아이들이 대답하고 그 들이 가고 있다고 말했지만, 실제로 얼마나 많은 사람들이 나타나는지 모릅니다. 그래서 당신은 알 수 없는 사람들을 위해 그릴을 준비해야 합니다.

그러나 bind()와는 달리 apply()로 호출된 함수는 즉시 호출 됩니다.

그래서 알 수 없는 식사 주문 배열을 처리할 수 있는 함수를 만들고, 그릴에 넣어야할 음식의 전체 목록을 반환해야 합니다. 배열의 조직 구조를 유지할 수 있으므로 요청이 들어오는 순서를 지정할 수 있습니다.

```
let cookout = {
  mealOrders: ["chicken", "burger", "burger", "steak", "chicken"],
  grill: function() {
    let args = Array.prototype.slice.call (arguments);
    
    console.log("I am going to cook :" + args.join(","));
  } 
}
```

여기서 주목해야 할 몇 가지 중요한 점이 있습니다. 먼저 grill() 메서드에는 매개 변수가 없습니다. 이것은 이전과 다릅니다.

이 문제를 해결하기 위해 4행의 인수 객체를 사용합니다. [자바스크립트의 인수 객체](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) 는 함수의 인수로 가득 찬 배열과 같은 객체이다.

실제로 배열로 변환하려면 배열 프로토 타입의 slice()메서드를 사용해야 합니다. 이것은 slice() 메서드가 객체에 고유하지 않기 때문에 call()메서드의 또 다른 편리한 애플리케이션 입니다.

마지막으로, 우리는 mealOrders 속성의 배열에 액세스하기 위해 apply()를 사용하여 함수를 호출해야 합니다. 방법은 다음과 같습니다. 

```
cookout.grill.apply(cookout, this.mealOrders);
// "I am going to cook: chicken, burger, burger, steak, chicken
```

[![applymethod1.png](https://res.cloudinary.com/practicaldev/image/fetch/s--NFtWGLhu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/applymethod1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NFtWGLhu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/03/applymethod1.png)

call()처럼 실행 문맥을 선언해야 하기 때문에 cookoout을 첫번째 인수로 사용해야 합니다. 그런 다음 mealOrders속성에서 배열을 제공 받을 수 있습니다. 

이렇게 하면 배열에서 두번째 인수로 전달할 수 있으므로 grill() 메서드 내에서 불명확한 수의 요소를 사용할 수 있습니다.