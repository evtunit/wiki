# 어떻게 자바스크립트 엔진이 작동하는가?

원문 : http://thibaultlaurens.github.io/javascript/2013/04/29/how-the-v8-engine-works/
작성일 : 2013-4-29

## 용어 사전 정의 (영->한)

---
engine : 엔진

type: 유형

lookup: 룩업

Monomorphic: 단일

polymorphic: 다형성

object: 객체

boxing: [복싱](https://en.wikipedia.org/wiki/Object_type_(object-oriented_programming)#Boxing)

linear: 선형

arrays where the set of keys are very compact : 컴팩트한 배열

sparse array: 희소 배열

allocate:  할당

---

V8은 독일의 Google 개발 센터에서 구축 된 자바 스크립트 엔진입니다. 오픈 소스이며 C ++로 작성되었습니다. 클라이언트 측 (Chrome)과 서버 측 (node.js) JavaScript 애플리케이션 모두에 사용됩니다.

V8은 웹 브라우저 내에서 JavaScript 실행 성능을 높이기 위해 처음 개발되었습니다. V8은 속도를 높이기 위해 인터프리터 대신 JavaScript 코드로 보다 효율적인 기계 코드로 변환합니다. SpiderMonkey 또는 Rhino (Mozilla)와 같은 많은 최신 자바 스크립트 엔진으로 JIT (Just-In-Time) 컴파일러를 구현하여 실행시 JavaScript 코드를 기계 코드로 컴파일합니다. V8과의 주요 차이점은 바이트 코드 또는 중간 코드를 생성하지 않는다는 것입니다.

이 글의 목적은 클라이언트 측 또는 서버 측 애플리케이션 모두에 최적화 된 코드를 생성하기 위해 V8의 작동 방식을 보여주고 이해하는 것입니다. "JavaScript 성능에 관심을 가져야할까요?"라고 묻는다면, Daniel Clifford (V8 팀 기술 책임자 겸 관리자)의 말을 인용하여 대답 할 것입니다. "현재의 애플리케이션을 더 빨리 실행할 수있는 것은 아닙니다. 과거에는 할 수 없었던 일들을 가능하게합니다."

## 숨겨진 클래스

JavaScript는 프로토 타입 기반 언어입니다. 복제 프로세스를 사용하여 클래스와 객체가 생성되지 않습니다. JavaScript는 또한 동적으로 유형이 지정됩니다. 유형 및 유형 정보는 명시 적이지 않으며 속성은 즉석에서 객체에 추가 및 삭제할 수 있습니다. 유형 및 속성에 효과적으로 액세스하는 것이 V8의 첫 번째 큰 도전 과제입니다. 객체 속성을 저장하기 위해 사전과 같은 데이터 구조를 사용하고 (대부분의 자바 스크립트 엔진과 마찬가지로) 속성 위치를 확인하기 위해 동적 룩업을 수행하는 대신 V8은 런타임에 숨겨진 클래스를 생성하여 유형 시스템의 내부 표현을 가짐으로써 속성 액세스 시간을 향상시킬 수 있습니다.

예를 들어 Point 함수와 두 개의 Point 객체를 만들어 보겠습니다.
<img src="./resource/scarlett/11/hiddenclass.png">

레이아웃이 동일하다면, p와 q는 V8에 의해 생성 된 것과 동일한 숨겨진 클래스에 속한다. 이는 숨겨진 클래스를 사용하는 또 다른 이점을 강조합니다. V8에서는 속성이 동일한 객체를 그룹화 할 수 있습니다. 여기서 p와 q는 동일한 최적화 된 코드를 사용합니다.

이제 q 객체에 z 속성을 추가하고 싶다고 가정 해 봅시다. 선언 직후에 (동적으로 형식이 지정된 언어에서 완벽하게 괜찮습니다).

V8은이 시나리오를 어떻게 다루겠습니까? 사실 V8은 생성자 함수가 속성을 선언하고 숨겨진 클래스의 변경 사항을 추적 할 때마다 새로운 숨겨진 클래스를 만듭니다. 왜? 두 개의 객체가 생성되고 (p와 q) 생성 후 두 번째 객체 (q)에 멤버가 추가되면 V8은 마지막으로 숨겨진 클래스 (첫 번째 객체 p)를 생성하고 새 객체를 생성해야하기 때문에 (두 번째 객체 q)를 새 멤버와 비교합니다.

<img src="./resource/scarlett/11/transition.png">

새로운 숨겨진 클래스가 생성 될 때마다 이전 클래스는 숨겨진 클래스가 대신 사용되어야하는 클래스 전환으로 업데이트됩니다.

### 코드 최적화
V8은 각 속성에 대해 새로운 숨겨진 클래스를 작성하기 때문에 숨겨진 클래스 작성을 최소한으로 유지해야합니다. 이렇게하려면 개체를 만든 후에 속성을 추가하지 말고 같은 순서로 개체 멤버를 항상 초기화해야합니다 (다른 숨겨진 클래스 트리를 만들지 않기 위해).

[업데이트] 또 다른 속임수 : 단일 연산은 동일한 숨겨진 클래스를 가진 객체에서만 작동하는 연산입니다. V8은 함수를 호출 할 때 숨겨진 클래스를 생성합니다. 다른 매개 변수 유형으로 다시 호출하면 V8은 다른 숨겨진 클래스를 만들어야합니다. 단일 형태 코드를 다형성 코드로 선호합니다.

## V8 최적화 된 JavaScript 코드의 예

### 태그 값

숫자와 자바 스크립트 객체를 효율적으로 표현하기 위해 V8은 둘 다 32 비트 값을 나타냅니다. 31 비트이기 때문에 여기서 SMall Integer 또는 SMI라고하는 객체 (플래그 = 1) 또는 정수 (플래그 = 0)인지 여부를 알기 위해 비트를 사용합니다. 그런 다음 숫자 값이 31 비트보다 큰 경우 V8은 숫자를 상자에 넣고 이중으로 바꾸고 새 개체를 만들어 내부에 번호를 넣습니다.

코드 최적화 : 자바 스크립트 객체에 값 비싼 복싱(boxing) 작업을 피하기 위해 가능한 한 31 비트 부호가 있는 숫자를 사용하십시오.

### 배열

V8은 배열 처리시 두 가지 방법을 사용합니다.

- 빠른 요소 : 키 집합이 매우 컴팩트한 배열 용으로 설계되었습니다. 매우 효율적으로 액세스 할 수있는 선형 저장소 버퍼가 있습니다.
사전 요소 : 내부에 모든 요소가없는 희소 배열 용으로 설계되었습니다. 실제로는 "빠른 요소"보다 액세스하기가 더 비싼 해시 테이블입니다.
- 코드 최적화 : V8이 배열을 처리하기 위해 "빠른 요소"를 사용하는지 확인하십시오. 즉, 키가 다음 증가하는 숫자가 아닌 경우 희소 배열을 피하십시오. 또한 큰 배열을 미리 할당하지 않도록하십시오. 사용할 때 할당하는 것이 좋다.(It is better to grow as you go.) 마지막으로 배열의 요소를 삭제하지 마십시오. 키 집합이 희박합니다.

V8은 JavaScript 코드를 어떻게 컴파일합니까?

## V8에는 두 개의 컴파일러가 있습니다!

- 자바 스크립트에 좋은 코드를 생성 할 수있는 "전체" 컴파일러 : 훌륭하지만 훌륭한 JIT 코드는 아닙니다. 이 컴파일러의 목표는 코드를 신속하게 생성하는 것입니다. 목표를 달성하기 위해 유형 분석을하지 않으며 유형에 대해 알지 못합니다. 대신 인라인 캐시 (줄여서 IC) 전략을 사용하여 프로그램이 실행되는 동안 유형에 대한 지식을 구체화합니다. IC는 매우 효율적이며 약 20 배의 속도 향상을 가져옵니다.

- 대부분의 JavaScript 언어에 대해 훌륭한 코드를 생성하는 최적화 컴파일러. 나중에 제공되며 더운 기능을 다시 컴파일합니다. 최적화 컴파일러는 인라인 캐시에서 유형을 가져 와서 코드를 더 잘 최적화하는 방법을 결정합니다. 그러나 일부 언어 기능은 try / catch 블록과 같이 아직 지원되지 않습니다. (try / catch 블록의 해결 방법은 함수에 "비 안정적인"코드를 작성하고 try 블록에서 함수를 호출하는 것입니다)

코드 최적화 : V8은 최적화 해제를 지원합니다. 최적화 컴파일러는 인라인 캐시에서 다양한 유형에 대한 긍정적 가정을 합니다. 이러한 가정이 유효하지 않은 경우 최적화가 해제됩니다. 예를 들어 생성 된 숨겨진 클래스가 예상 한 클래스가 아닌 경우 V8은 최적화 된 코드를 버리고 전체 컴파일러로 돌아와 인라인 캐시에서 다시 유형을 가져옵니다. 이 프로세스는 느리고 최적화 된 후에는 함수를 변경하지 않으려 고해서는 안됩니다.

## 레퍼런스

Google I/O 2012 “Breaking the JavaScript Speed Limit with V8” with Daniel Clifford, tech lead and manager of the V8 team: [video](https://www.youtube.com/watch?v=UJPdhx5zTaw) and [slides](http://v8-io12.appspot.com/).

V8: an open source JavaScript engine: [video](https://www.youtube.com/watch?v=hWhMKalEicY) of Lars Bak, V8 core engineer.

Nikkei Electronics Asia blog post: Why Is the New Google V8 Engine So Fast?

## 관련 포스트글
[Relational database and Normalization 28 Feb 2013](http://thibaultlaurens.github.io/database/2013/02/28/relational-database-and-normalization/)

[Ruby: tips, tricks and bytecode analysis 11 Feb 2013](http://thibaultlaurens.github.io/ruby/2013/02/11/ruby-tips-tricks-and-deep-analysis/)

[Generics in C# 12 Jan 2013](http://thibaultlaurens.github.io/microsoft/2013/01/12/generics-with-csharp/)