당신은 JavaScript로 프로그래밍하면서 재귀 함수에 대한 참조를 발견했을 수도 있습니다.  
심지어 자신 스스로 몇개의 construct를 구성했을 수도 있습니다. 그러나 당신은 효과적인 재귀 함수의 많은 예를 보지 못했을 겁니다.  
실제로 이 접근 방식의 특성 외에 재귀가 언제 어디서 유용하게 쓰이는지, 혹은 부주의하게 사용하면 얼마나 위험한지 고려하지 않았을겁니다.  

## 재귀는 무엇이 좋을까요?  
재귀는 결과에 도달 할 때까지 함수 호출을 반복하여 작업하는 기술입니다.  
대부분의 루프는 재귀 스타일로 다시 작성할 수 있으며 일부 기능적 언어에서는 이 루핑 방식이 기본값입니다.  

그러나 JavaScript의 기능적 코딩 스타일은 재귀 함수를 지원하지만,  
대부분의 JavaScript 컴파일러는 현재 이를 안전하게 지원하도록 최적화되어 있지 않습니다.  

루프 내에서 다른 매개 변수를 사용하여 동일한 함수를 반복적으로 호출해야 할 때 재귀가 가장 잘 적용됩니다.  
여러 상황에서 사용할 수 있지만, 프랙털 수학, 정렬 또는 복잡한 또는 비선형 데이터 구조의 노드 탐색과 같은 반복 분기와 관련된 문제를 해결하는 데  
가장 효과적입니다.  

함수형 프로그래밍 언어에서 재귀를 선호하는 한 가지 이유는 로컬 변수를 사용하여 상태를 설정 및 유지 관리 할 필요가 없는 코드를  
생성 할 수 있기 때문입니다.  
재귀 함수는 주어진 입력에 대해 구체적이고 일관된 반환 값을 가지고 외부 변수 상태에 대한 부작용이 없는 순수한 방식으로  
작성하기 쉽고 자연스럽게 테스트하기 쉽습니다.  

## Looping
재귀를 적용 할 수 있는 함수의 전형적인 예는 계승입니다.  
이것은 각각의 앞의 정수에 1을 곱한 값을 반복해서 곱한 후 값을 반환하는 함수입니다.  

예를 들어, 3의 계승은 다음과 같습니다.
````
3 × 2 × 1 = 6
````
6의 계승은 다음과 같습니다.
````
6 × 5 × 4 × 3 × 2 × 1 = 720
````  
이 결과가 얼마나 빨리 커지는 지 알 수 있습니다. 또한 동일한 동작이 계속 반복되고 있음을 알 수 있습니다.  
하나의 곱셈 연산의 결과를 가져 와서 두 번째 값보다 작은 값을 다시 곱합니다. 그런 다음 우리는 하나에 도달 할 때까지 계속 반복합니다.  

for문을 사용하면 올바른 결과를 반환 할 때 까지 작업을 반복적으로 수행하는 함수를 만드는 것은 어렵지 않습니다.  
````
var factor = function(number) {
  var result = 1;
  var count;
  for (count = number; count > 1; count--) {
    result *= count;
  }
  return result;
};
console.log(factor(6));
// 720
````
이것은 효과가 있지만 기능적 프로그래밍 관점에서 매우 아름답지는 않습니다.  
for문를 지원하고 결과를 반환하려면 상태를 유지 관리하고 추적하는 두 개의 로컬 변수를 사용해야합니다.  
for문을 버리고 더 기능적인 JavaScript 접근 방식을 취할 수 있다면 더 깨끗하지 않습니까?  

## 재귀
우리는 JavaScript가 함수를 인수로 취하는 함수를 작성할 수 있다는 것을 알고 있습니다.  
따라서 실제 기능을 사용하려면 실행중인 상황에서 작성하고 실행합니다.  

가능한가요? 예를 들어 다음과 같은 간단한 while문을 예로 들어 보겠습니다.  
````
var counter = 10;
while(counter > 0) {
    console.log(counter--);
}
````
이 작업이 끝나면 카운터 값이 변경 되었지만 루프는 상태를 천천히 보유한 각 값을 인쇄하는 작업을 수행 했습니다.  

동일한 루프의 재귀 버전은 다음과 같습니다.  
````
var countdown = function(value) {
    if (value > 0) {
        console.log(value);
        return countdown(value - 1);
    } else {
        return value;
    }
};
countdown(10);
````
countdown 함수의 정의 내에서 countdown 함수를 어떻게 호출하는지 봅니까?  
JavaScript는 상위와 같은 것을 처리하고 원하는 것을 수행합니다.  
countdown이 실행될 때마다 JavaScript는 호출 된 위치를 추적 한 다음 완료 될 때까지 해당 함수 호출 스택을 통해 뒤로 작동합니다.  
우리 함수는 변수의 상태를 수정하는 것을 피했지만 여전히 재귀를 제어하기 위해 전달 된 값을 활용했습니다.  

계승 사례로 돌아가서 재귀를 사용하기 위해 다음과 같이 이전 함수를 다시 작성할 수 있습니다.  
````
var factorial = function(number) {
  if (number <= 0) { // terminal case
    return 1;
  } else { // block to execute
    return (number * factorial(number - 1));
  }
};
console.log(factorial(6));
// 720
````
이런 식으로 코드를 작성하면 부작용없이 전체 프로세스를 상태 비저장 방식으로 설명 할 수 있습니다.  
계산할 때 먼저 함수에 전달되는 인수의 값을 테스트하는 방법도 주목할 가치가 있습니다.  
우리는 마지막 케이스에 도착했을 때 신속하고 깨끗하게 종료하기 위해 자신을 호출 할 함수를 원합니다.  
이 방법으로 계승 된 계승의 경우, 전달되는 숫자가 0 또는 음수 일 때 마지막 사례가 발생합니다.  
(원하는 경우 음수 값을 테스트하고 다른 메시지를 반환 할 수도 있음).  

## Tail Call Optimization (꼬리 호출 최적화)
현대적인 JavaScript 구현의 한 가지 문제점은 재귀 함수가 무기한으로 쌓이지 않고 엔진 용량을 초과 할 때까지  
메모리에서 쌓이는걸 방지할 표준 방법이 없다는 것입니다.  
JavaScript 재귀 함수는 매번 호출 된 위치를 추적해야 올바른 시점에서 다시 시작할 수 있습니다.  

Haskell 및 Scheme과 같은 많은 기능 언어에서 이것은 꼬리 호출 최적화라는 기술을 사용하여 관리됩니다.  
꼬리 호출 최적화를 사용하면 재귀 함수의 각 연속 사이클이 메모리에 쌓이지 않고 즉시 발생합니다.  

이론적으로 꼬리 호출 최적화는 현재 JavaScript의 다음 버전 인 ECMAScript6 표준의 일부이지만  
아직 대부분의 플랫폼에서 완전히 구현되지는 않았습니다.  

## Trampoline Functions (트램폴린 함수들)
필요할 때 JavaScript가 안전한 방식으로 재귀 함수를 수행하도록하는 방법이 있습니다.  
예를 들어, 한 번에 하나의 작업 만 스택에 유지하면서 재귀 실행을 반복적으로 관리하는 사용자 정의 트램펄린 기능을 구성 할 수 있습니다.  
이 방법으로 사용되는 트램펄린 함수는 특정 컨텍스트에 함수를 바인딩하는 JavaScript의 기능을 활용하여 순환 함수가 자체에 대해 반사되어  
주기가 완료 될 때까지 한 번에 하나씩 결과를 빌드 할 수 있습니다. 이렇게하면 수행 대기중인 작업 스택이 많이 생성되지 않습니다.  

실제로 트램펄린 기능을 사용하면 일반적으로 안전을 위해 성능이 저하됩니다.  
또한 함수를 재귀 적으로 작성하여 얻는 우아함과 가독성은 JavaScript에서 접근 방식을 작동시키는 데 필요한 코드 복잡성에서 사라집니다.  

궁금한 점이 있으면이 개념에 대해 자세히 읽고 아래 토론에서 의견을 나누십시오.  
StackOverflow에서 짧은 스레드로 시작한 다음 Don Taylor와 Mark McDonnell의 JavaScript로 트램펄린의 기복을 자세히 살펴 보는 에세이를 살펴보십시오. 

## 우리는 아직 없습니다.
재귀는 알아야 할 강력한 기술입니다. 많은 경우 재귀는 복잡한 문제를 해결하는 가장 직접적인 방법입니다.  
그러나 ECMAScript6이 어디에서나 구현 될 때까지 꼬리 호출 최적화가 필요하며 재귀를 적용하는 방법과 위치에 대해 매우 신중해야합니다.  
