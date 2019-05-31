# Chrome V8 엔진이 JavaScript를 코드를 읽는 방법

크롬의 V8의 핵심을 깊이 파고들기 전에 먼저 기초부터 좀 낮추도록 하겠습니다. 

저희의 모든 시스템은 마이크로프로세서로 구성되어 있습니다. 이 프로세서는 지금 여러분의 컴퓨터 안에 있고 여러분이 이것을 읽을 수 있게 해줍니다.

마이크로프로세서는 전기 신호로 작동하고 지시를 내립니다. 

지침은 마이크로프로세서가 해석할 수 있는 언어로 작성되어 있습니다. 가장 일반적인 것은 IA-32, x86–64, MIPS 및 ARM입니다. 

이러한 언어는 하드웨어와 직접 상호 작용하므로 여기에 기록된 코드를 기계 코드라고 합니다. 컴퓨터에 쓰는 코드는 기계 코드로 변환되거나 컴파일됩니다.

<br>

시스템의 특정 메모리 조각에서 낮은 수준에서 수행되는 지침으로 구성됩니다. 프로그램을 실행하기 위해 이 모든 것을 쓸 필요가 없는 것이 행운이라고 느끼실 거예요!

높은 수준의 언어는 언어에서 추상화됩니다. 아래의 추상화 수준에서는 JavaScript를 기계 수준에서 추상화하는 정도를 확인할 수 있습니다. C/C++는 상대적으로 하드웨어에 훨씬 가까우며 다른 고급 언어보다 훨씬 빠릅니다.

![](https://cdn-images-1.medium.com/max/800/1*Hmr87--VeQ_GyZesKYtEeg.png)

<br>

> 이제 V8 엔진으로 돌아가 보겠습니다. 

V8은 Google이 제공하는 강력한 오픈 소스 Javascript 엔진입니다. 자바스크립트 엔진이란 무엇일까요? 

자바스크립트 코드를 마이크로프로세서가 이해할 수 있는 낮은 레벨 또는 기계 코드로 변환하는 프로그램입니다.

[Rhino](https://en.wikipedia.org/wiki/Rhino_(JavaScript_engine)), [JavaScriptCore](https://en.wikipedia.org/wiki/WebKit#JavaScriptCore) 및 [SpiderMonkey](https://en.wikipedia.org/wiki/SpiderMonkey)를 비롯한 다양한 JavaScript 엔진이 있습니다. 

이러한 엔진은 ECMAScript 표준을 따릅니다. ECMAScript는 스크립팅 언어의 표준을 정의합니다. 

JavaScript는 ECMAScript 표준을 기반으로 합니다. 이러한 표준은 언어가 어떻게 작동하고 어떤 기능을 가져야 하는지를 정의합니다. 여기에서 ECMAScript에 대해 자세히 알아볼 수 있습니다.

<br>

Chrome V8 엔진은 다음과 같습니다.

- V8 엔진은 C++로 작성되며, Chrome 및 Nodej에 사용됩니다.
- ECMA-262에 지정된 대로 ECMAScript를 구현합니다.
- V8 엔진은 독립 실행형으로 실행할 수 있으며, C++ 프로그램을 통해 내장할 수 있습니다.

> 마지막 부분을 조금 더 잘 이해하도록 해주세요. 

V8은 독립 실행형으로 실행될 수 있으며, 동시에 자체 기능 구현을 C+++에 추가하여 JavaScript에 새로운 기능을 추가할 수 있습니다.

![](https://cdn-images-1.medium.com/max/800/1*PA3QZ_7EWgoDGNyJID_7MA.png)

<br>

예를 들어, `print('Hello World')`는 Node.js의 올바른 문장이 아닙니다. 그 부분을 편집하면 오류가 생길 것입니다.

그러나, Gitub의 오픈 소스인 V8 위에 C++의 인쇄 기능을 자체적으로 구현하여 인쇄 기능을 기본적으로 사용할 수 있습니다. 이를 통해 JavaScript는 ECMAScript 표준에서 JavaScript가 이해해야 할 사항 이상을 이해할 수 있습니다.

C++는 하드 드라이브의 파일 및 폴더를 처리하는 것과 같이 하드웨어에 훨씬 가깝기 때문에 JavaScript에 비해 프로그래밍 언어로서의 기능이 더 많기 때문에 강력한 기능입니다. 코드를 C++로 작성하고 JavaScript에서 사용할 수 있도록 함으로써 JavaScript에 더 많은 기능을 추가할 수 있습니다. Node.js 자체는 서버측 프로그래밍 및 네트워킹 애플리케이션을 지원하는 V8 엔진의 C++ 구현입니다.

<br>

여기서는 기본적으로 Node.js에서 사용할 수 없는 `Print`, `Read` 같은 다양한 기능의 구현을 볼 수 있습니다. 아래에서는 `print` 기능의 구현을 볼 수 있습니다. Node.js에서 `print()` 기능이 호출될 때마다 콜백이 생성되고 기능이 실행됩니다.

마찬가지로, 다양한 새로운 기능을 구현한 C++를 V8에 추가하여 Node.js가 이해할 수 있도록 할 수 있습니다.

![](https://cdn-images-1.medium.com/max/800/1*GtV0dm8WOU4l43oK58SHqg.png)

<br>


## 결론

V8엔진에 대해서는 간단한 설명으로는 무리이며 V8 엔진이 후드 아래에서 수행하는 작업의 양입니다. Node.js의 작동 방식과 Chrome V8 엔진의 실제 작동 방식을 명확히 이해해야 합니다.