이전에는 JavaScript에서 time loop를 수행하는 한 가지 방법이 있었습니다. setInterval().  
꽤 빠른 것을 반복 할 필요가 있다면 (for 루프처럼 빠르지는 않지만 가능한 한 빨리) 반복 할 필요가 있다면, 그것을 사용할 것입니다.  
애니메이션의 목적 상, 매초 60 개의 "프레임"이 매끄럽게 나타나기 때문에 다음과 같은 루프를 실행합니다.

````
setInterval(function() {
  // animiate something
}, 1000/60);
````

지금 이것에 대한 더 나은 대안이 있습니다. Paul Irish는 2 년 전에 requestAnimationFrame을 도입했습니다.  
나는 그것을 더할 필요가 별로 없으며, 실제로 사용 해보지 못했고, 이제는 단어를 전파하고 기본적인 사용법을 쓰는 데 도움이 될 것이라고 생각했습니다.  

## 더 나은 이유?

Paul 설명 :  
* 브라우저가 애니메이션을 최적화 할 수 있으므로 애니메이션이 부드럽게 처리된다.
* 비활성 탭의 애니메이션이 중지되어 CPU가 차가워진다.
* 더욱 배터리 친화적이다.

## 가장 간단한 가능한 예시
````
function repeatOften() {
  // Do whatever
  requestAnimationFrame(repeatOften);
}
requestAnimationFrame(repeatOften);
````
한 번 호출하여 킥오프하면 함수가 재귀적으로 자신을 호출합니다.  

## 시작 및 중지
requestAnimationFrame은 setTimeout 또는 setInterval과 마찬가지로 취소하기 위해 사용할 수있는 ID를 반환합니다.  
jQuery는 여기에서 간단한 애니메이션 및 이벤트 바인딩을 보여주기 위해 사용되었습니다.

````
var globalID;

function repeatOften() {
  $("<div />").appendTo("body");
  globalID = requestAnimationFrame(repeatOften);
}

$("#start").on("click", function() {
  globalID = requestAnimationFrame(repeatOften);
});

$("#stop").on("click", function() {
  cancelAnimationFrame(globalID);
});
````

## 브라우저 지원

사용할 수있는 테이블을 참조하십시오.
유일한 주목할만한 문제는 IE 9, iOS 5 및 Android입니다. 그러나 실제로는 문제가 아닙니다. 왜냐하면 : 

## Polyfill
멋진 웹 기능과 마찬가지로 사용 가능한 경우 웹 기능을 사용하고 사용할 수 없을 때 사용할 수있는 기능으로 대체하는 것이 좋습니다.  
아마도이 요지를 참조하는 것이 가장 좋습니다.  
사실상 requestAnimationFrame 또는 cancelAnimationFrame을 사용하기 전에 그 청크를 문자 그대로 포함 시키십시오.

이것을 사용하면 모든 브라우저에서 requestAnimationFrame을 사용할 수 있습니다.  


## 약간 복잡한 예제
````
var globalID;

function repeatOften() {
  $("<div />").appendTo("body");
  globalID = requestAnimationFrame(repeatOften);
}

  globalID = requestAnimationFrame(repeatOften);

$("#stop").on("click", function() {
  cancelAnimationFrame(globalID);
});
$("#start").on("click", function() {
  globalID = requestAnimationFrame(repeatOften);
});
````

사실 더 복잡한 것은 여러 애니메이션을 동시에 사용하여 실행하는 것입니다 (그래도 여전히 OK입니다).  
예제를 알고 있다면 의견에서 그 장점 중 일부를 자유롭게 링크하십시오.
