# Chrome V8 엔진이 JavaScript를 코드를 읽는 방법

크롬의 V8의 핵심을 깊이 파고들기 전에 먼저 기초부터 좀 낮추도록 하겠습니다. 

저희의 모든 시스템은 마이크로프로세서로 구성되어 있습니다. 이 프로세서는 지금 여러분의 컴퓨터 안에 있고 여러분이 이것을 읽을 수 있게 해줍니다.

마이크로프로세서는 전기 신호로 작동하고 궁극적으로 그 일을 하는 작은 기계입니다. 우리는 마이크로프로세서에 지시를 내립니다. 

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