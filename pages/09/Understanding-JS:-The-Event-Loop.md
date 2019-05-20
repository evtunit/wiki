# 이벤트 루프에 대해 이해 하기

개발을 쉽게 해주는 다양한 종류들의 라이브러리, 도구 같은 것들이 많이 생겨남에 따라 프로그래머가 어떻게 작동하는지에 대해 깊게 이해 하지 않고 애플리케이션을 개발하기 시작합니다. Javascript가 대표적인 사례 입니다. (JavaScript is the poster boy of this exact behavior.?음?이게 몬가여...)
자바스크립트는 가장 복잡한 언어 중 하나이며 널리 퍼져 있는 반면, 많은 개발자들은 높은 수준의 도구를 사용하고 있어 언어의 "나쁜 부분"을 추상화하는 데 매료 됩니다.

놀라운 애플리케이션을 계속 만들 수는 있지만 Javascript에 깊게 들어가면 상당히 도움이됩니다. "괴괴 망측 한 부분"을 이해하는 것은 수석 개발자와 평균 grunt 코더를 분리하는 것입니다. JS 생태계는 끊임없이 변화하는 반면 기본 요소는 다른 모든 도구가 구축되는 기본 요소 입니다. 그것들을 이해하면 더 넓은 인식을 얻을 수 있고 개발 과정을 보는 방법을 바꿀 수 있습니다.

## 이벤트 루프란 무엇입니까?

JavaScript는 단일 스레드 언어라고 들었을 것입니다. 콜스택 및 이벤트 큐라는 용어를 들어 보셨을 수도 있습니다. 대부분의 사람들은 이벤트 루프가 JavaScript가 콜백과 Promise을 사용할 수 있도록 허용하고 있지만 더 많이 있다는 것도 알고 있습니다. 너무 많은 세세한 사항까지 거치지 않고 실제로 JavaScript 코드가 어떻게 동작하는지에 대한 높은 수준의 시각을 갖게 됩니다.

## 콜스택

JavaScript에는 현재 실행중인 함수와 그 이후에 실행될 함수를 추적하는 단일 호출 스택이 있습니다. 하지만 먼저 스택이 무엇일까요? 스택은 배열과 유사한 데이터 구조지만 몇 가지 제한이 있습니다. 항목을 뒤에 추가하고 마지막 항목만 제거할 수 있습니다. 또 다른 예는 플레이트 더미 입니다. 플레이트를 서로 위에 올려 놓으면 언제든지 상단의 플레이트만 제거 할 수 있습니다.

함수를 실행하려고하면 호출 스택에 추가됩니다. 그런 다음 해당 함수가 다른 함수를 호출하면 다른 함수가 호출 스택의 첫 번째 함수 위에 옵니다. 콘솔에서 오류가 발생하면 실행 경로를 보여주는 긴 메시지가 표시 됩니다. 바로 그 순간에 스택이 보입니다. 그러나 우리가 요청하거나 무언가에 타임 아웃을 걸면 어떨까요? 이론적으로 호출 스택이 계속 될 수 있도록 실행될 때까지 전체 브라우저를 고정 시켜야 할까요? 그러나 실제로는 이벤트 테이블 및 이벤트큐로 인해 발생하지 않습니다.

## 이벤트 테이블 및 이벤트 큐

setTimeout 함수를 호출하거나 비동기 연산을 수행할 때마다 이벤트 테이블에 추가 됩니다. 이것은 특정 이벤트가 발생한 후에 특정 함수가 트리거되어야한다는 것을 알고 있는 데이터 구조입니다. 해당 이벤트가 발생하면(타임아웃, 클릭, 마우스 이동) 알림을 보냅니다. 이벤트 테이블은 함수를 실행하지 않으며 자체적으로 호출 스택에 추가하지 않습니다. 이벤트를 추적하여 이벤트 큐로 보내는 것이 유일한 목적입니다.

이벤트큐는 스택과 비슷한 데이터 구조입니다. 다시 항목을 다시 추가하지만 앞에서만 항목을 제거할 수 있습니다. 그것은 일종의 함수가 실행되어야하는 올바른 순서를 지정합니다. 이벤트 테이블에서 함수 호출을 수신지만 호출 스택에 함수를 보내야 할까요?
호출 스택은 이벤트 루프가 들어오는 곳입니다.

## 이벤트 루프

마침내 악명 높은 이벤트 루프에 도달했습니다. 이벤트 루프는 끊임없이 호출 스택이 비었는지에 대한 검사를 실행하는 프로로세스 입니다.
상상해보세요. 시계이 같을 때마다 호출 스택을 확인하고 비어 있는 경우 이벤트 큐를 봅니다. 이벤트큐에 대기중인 항목이 있으면 호출 스택으로 이동합니다. 그렇지 않다면 아무 일도 일어나지 않습니다.

다음은 몇 가지 흥미로운 사례입니다. 다음 코드가 어떤 순서로 실행될 것이라고 생각하십니까?

```javascript
setTimeout(() => console.log('first'), 0)
console.log('second')
```

어떤 사람들은 set timeout이 0으로 호출되기 때문에 즉시 실행 되어야 한다고 생각합니다. 사실 이 예제에서는 "first" 앞에 "second"가 화면에 출력됩니다. Javascript는 setTimeout을 보고 "글쎄, 내 이벤트 테이블에 이것을 추가하고 실행을 계속해야한다"라고 말합니다. 그런 다음 이벤트 테이블, 이벤트 큐를 통해 이동하고 이벤트 루프가 실행될 때까지 기다립니다.

## Exploits

이벤트 루프의 또 다른 흥미로운 예는 재귀입니다. 스택 오버 플로우 오류 메시지를 본 적이 있습니까? 때때로 무한 재귀를 만들 때 가끔 얻을 수 있지만 때로는 실제로 재귀 호출을 많이 만듭니다. 코드 구조를 유지하면서 여전히 불합리한 양의 호출을 할 수있는 간단하면서도 해킹이 가능한 해결 방법이 있습니다. 재귀 호출을 setTimeout에 래핑하세요.

recursion()을 직접 호출하는 대신에 (여러분의 메소드의 이름이라고 상상해보세요), setTimeout (() => recursion (), 0)을 호출 할 수 있습니다. 스택에 직접 쌓이는 대신 이벤트 테이블과 대기열을 통과하기 때문에 스택 오버 플로우가 발생하지 않습니다. 이 방법을 사용하지 말고 JavaScript의 동작을 보여주는 좋은 예입니다.

## 결론

더 많은 것들이 진행되고 있으며 이것은 루프의 기본적인 설명과 이와 관련된 것들 입니다. 되도록이면 이를 간단하게 유지하기 위해 이벤트 루프가 전체 프로세스에 참여하지 않고 수행하는 작업을 설명 할 수있는 방법이 없습니다.다른 점은 이 설명이 V8 JavaScript 엔진의 맥락에 있다는 것입니다. Chrome의 엔진이며 노드에서도 사용됩니다.