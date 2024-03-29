## ES modules : A cartoon deep-dive

ES 모듈은 자바스크립트에 공식 표준화 된 모듈 시스템을 도입합니다. 그러나 10년 가까이 표준화 작업을 하면서 여기까지 오는데는 시간이 걸렸습니다.  

하지만 기다림은 거의 끝났습니다. 5월에 파이어 폭스60(현재 베타 버전)이 출시되면 모든 주요 브라우저가 ES 모듈을 지원할 것이고, Node모듈 실무그룹은 현재 Node.js에 ES 모듈 지원을 추가하기 위해 노력하고 있습니다. 또한 WebAssembly를위한 ES 모듈 통합도 진행 중입니다. 

많은 자바스크립트 개발자들은 ES 모듈이 논란이 되었음을 알고 있습니다. 

그러나 ES 모듈이 어떻게 작동하는지 실제로 이해하는 사람은 거의 없습니다.  

### 모듈이 해결하는 문제는 무엇인가?

생각해보면, 자바스크립트의 코딩은 변수 관리에 관한 것입니다. 변수에 값을 할당하거나 변수에 숫자를 추가하거나 두 변수를 결합하여 다른 변수에 넣는 것이 전부입니다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/01_variables-500x178.png" width="400" alt="">  

코드의 상당 부분이 변수를 변경하는 것이기 때문에 이러한 변수를 구성하는 방법은 코드를 얼마나 잘 작성하고 얼마나 잘 유지 관리 할 수 있는지에 큰 영향을 미칩니다.

한번에 몇 가지 변수만 생각하면 일이 쉬워집니다. 자바스크립트는 스코프(Scope)라고 불리는 것이 돕는 방법을 가지고 있습니다. 자바스크립트에서 범위가 작동하는 방식 때문에 함수는 다른 함수에서 정의된 변수에 액세스할 수 없습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/02_module_scope_01-500x292.png" width="400" alt="">  

이것은 좋다. 즉, 한 가지 함수를 수행 할 때 그 함수 하나만 생각하면됩니다. 다른 함수가 변수에 어떤 영향을 미치는지 걱정할 필요가 없습니다.
또한 단점을 가지고있습니다. 그것은 다른 함수들간에 변수를 공유하는 것을 어렵게 만듭니다.  

변수를 범위 외부에서 공유하고 싶다면 어떻게해야합니까?   
이를 처리하는 가장 일반적인 방법은 전역 범위와 같이 위의 범위에 배치하는 것입니다.  

아마도 jQuery에서 이 사실을 기억할 것입니다.   
jQuery 플러그인을 로드하기 전에 jQuery가 전역 범위에 있는지 확인해야 했습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/02_module_scope_02-500x450.png" width="400" alt="">  

이 방법은 효과적이지만 결과로 생기는 성가신 문제입니다.  
먼저, 모든 스크립트 태그가 올바른 순서로 정렬되어야 합니다. 그런 다음 아무도 명령을 망치지 않도록 조심해야 합니다.  
그 명령을 망쳐 버리면 실행 중에 앱에서 오류가 발생합니다. 함수가 전역에서 jQuery가 기대하는 위치에 함수를 찾지 못하면 오류를 발생시키고 실행을 중단합니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/02_module_scope_03-500x450.png" width="400" alt="">  

이로 인해 코드 유지가 까다로워집니다. 오래된 코드 또는 스크립트 태그를 제거하는 룰렛의 게임을합니다. 당신은 무엇이 깨질지 모릅니다. 코드의 다른 부분들 사이의 종속 관계는 암시적입니다. 모든 함수는 전역 변수를 가져올 수 있으므로 어떤 함수가 어떤 스크립트에 의존하는지 알지 못합니다.  

두 번째 문제는 이러한 변수가 전역 범위에 있기 때문에 해당 전역 범위 안에있는 코드의 모든 변수를 변경할 수 있다는 것입니다. 악성 코드는 일부러 해당 변수를 변경하여 코드가 의도하지 않은 작업을 수행하도록 만들거나 비악성 코드가 실수로 변수를 차단할 수 있습니다.  

### 모듈은 어떻게 도움이됩니까?

모듈은 이러한 변수와 함수를 구성하는 더 좋은 방법을 제공합니다. 모듈을 사용하면 함께 사용하기에 적합한 변수와 함수를 그룹화 할 수 있습니다. 
이러한 함수와 변수를 모듈 범위에 넣습니다. 모듈 스코프를 사용하여 모듈의 함수간에 변수를 공유 할 수 있습니다. 

그러나 함수 범위와 달리 모듈 범위는 변수를 다른 모듈에서도 사용할 수 있는 방법을 제공합니다. 모듈의 어떤 변수, 클래스 또는 함수가 사용 가능해야하는지 명시 적으로 말할 수 있습니다.  

다른 모듈에서 사용할 수있는 것이 있으면 이를 export라고합니다. export를하면 다른 모듈은 해당 변수, 클래스 또는 함수에 의존한다고 명시 할 수 있습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/02_module_scope_04-500x450.png" width="400" alt=""> 

모듈은 명시적인 관계이기 때문에 다른 모듈을 제거하면 어떤 모듈이 파손될지를 알 수 있습니다.  

일단 모듈들 사이에 변수를 export하고 import 능력을 갖게 되면, 당신의 코드를 서로 독립적으로 작동할 수 있는 작은 덩어리로 분해하는 것을 훨씬 더 쉽게 만듭니다. 그런 다음 이 덩어리를 결합하고 다시 결합하면, 마치 레고 블록과 같은, 동일한 모듈에서 모든 종류의 어플리케이션을 만들 수 있습니다.

모듈이 매우 유용하기 때문에, 자바스크립트에 모듈 기능을 추가하려는 시도가 여러 번 있었습니다. 오늘날에는 두 가지 모듈 시스템이 활발하게 사용되고 있습니다.  
CommonJS(CommonJS, CJS)는 Node.js가 역사적으로 사용해 온 것이다. ESM(EcmaScript 모듈)은 자바스크립트 규격에 추가된 최신 시스템입니다. 브라우저는 이미 ES 모듈을 지원하며, Node는 지원을 추가하고 있습니다.

새로운 모듈 시스템이 어떻게 작동하는지 자세히 살펴봅시다.

### ES 모듈의 작동 방식 

모듈을 사용하여 개발할때 의존성 그래프를 작성합니다. 서로 다른 종속성 사이의 연결은 사용하는 모든 import 문에서옵니다.

이 import 문은 브라우저 또는 Node가 로드해야하는 코드를 정확히 인식하는 방법입니다. 그래프의 진입점으로 사용할 파일을 제공합니다. 여기에서 나머지 코드를 찾으려면 import문을 따르면 됩니다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/04_import_graph-500x291.png" width="500" alt=""> 

하지만 파일 자체는 브라우저가 사용할 수 있는 것이 아닙니다. 이 모든 파일을 모듈 레코드라는 데이터 구조로 변환하려면 구문 분석해야 합니다. 그렇게 하면 파일에서 무슨 일이 벌어지고 있는지 실제로 알 수 있습니다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/05_module_record-500x287.png" width="500" alt=""> 

그런 다음 모듈 레코드를 모듈 인스턴스로 변환해야합니다. 인스턴스는 두 가지를 결합합니다 : Code와 State. 

코드는 기본적으로 일련의 지시사항 입니다. 무언가를 만드는 방법을 배우는 것과 같습니다. 하지만 그 자체로는 어떤 일도 할 수 없습니다. 이러한 지시들을 따르기 위한 원료가 필요합니다.  

State란 무엇입니까? State는 당신에게 원료를 줍니다. 
State는 임의의 시점에서 변수의 실제 값입니다. 물론 이 변수는 값을 보유하는 메모리의 상자에 대한 애칭 일 뿐입니다.  
따라서 모듈 인스턴스는 Code(명령어 목록)와 State(모든 변수의 값)를 결합합니다. 

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/06_module_instance-500x372.png" width="500" alt=""> 

우리가 필요로하는 것은 각 모듈에 대한 모듈 인스턴스입니다. 모듈 로딩 과정은 진입점 파일에서 모듈 인스턴스의 전체 그래프로 진행됩니다.  

ES 모듈의 경우 세 단계로 작업이 수행됩니다. 

1. Construction - 모든 파일을 찾아 다운로드하고 모듈 레코드로 파싱합니다.  
2. Instantiation - 내 보낸 값을 모두 배치하기 위해 메모리에있는 상자를 찾습니다(그러나 값을 아직 채우지는 않음). 그런 다음 exports와 imports 모두 메모리의 해당 상자를 가리 킵니다. 이를 링크라고합니다.  
3. Evaluation - 코드를 실행하여 변수의 실제 값으로 상자를 채웁니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/07_3_phases-500x184.png" width="600" alt=""> 

사람들은 ES 모듈이 비동기적이라고 말합니다. 작업은 loading, instantiation 및 evaluation 세 단계로 나뉘며 이러한 단계는 개별적으로 수행 할 수 있기 때문에 비동기적이라고 생각 할 수 있습니다. 

이 스펙이 CommonJS에 없는 종류의 비동기를 도입한다는 것을 의미합니다. 나중에 더 자세히 설명 하겠지만, CJS에서는 모듈과 그 아래의 의존성이 한꺼번에 로드되고, 인스턴스화되고, evaluation이 이루어집니다. 

그러나 단계 자체는 반드시 비동기식은 아닙니다. 동기식으로 수행 할 수 있습니다. 그것은 무엇을 로드하고 있는지에 달려 있습니다. 왜냐하면 모든 것이 ES 모듈 스펙에 의해 제어되지 않기 때문입니다. 실제로 두 개의 반쪽이 있는데, 서로 다른 스펙으로 다뤄집니다. 

[ES 모듈 스펙](https://tc39.github.io/ecma262/#sec-modules)은 파일을 모듈 레코드로 구문 분석하는 방법과 해당 모듈을 인스턴스화하고 evaluation하는 방법을 설명합니다. 그러나, 처음에 파일을 얻는 방법을 말하지 않습니다.  

파일을 가져오는 것은 loader입니다. loader는 다른 스펙으로 지정됩니다. 브라우저의 경우 해당 스펙은 [HTML 스펙](https://html.spec.whatwg.org/#fetch-a-module-script-tree)입니다.  
그러나 어떤 플랫폼을 사용하고 있는지에 따라 다른 loader를 가질 수 있습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/07_loader_vs_es-500x286.png" width="500" alt=""> 

loader는 모듈이 로드되는 방식을 정확하게 제어합니다.  
ES 모듈 메서드 (ParseModule, Module.Instantiate 및 Module.Evaluate)를 호출합니다.  
JS 엔진의 끈을 조종하는 꼭두각시 인형처럼 느껴집니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/08_loader_as_puppeteer-500x330.png" width="500" alt=""> 

이제 각 단계를 자세히 살펴 보겠습니다. 

#### Construction 

Construction 단계에서 각 모듈에 대해 세 가지 일이 발생합니다.  

1. 모듈이 들어 있는 파일을 어디에서 다운로드할지 알아라 (aka module resolution)
2. (URL에서 다운로드하거나 파일 시스템에서 파일을 로드하여) 파일을 가져옵니다. 
3. 파일을 모듈 레코드로 Parse합니다. 

#### 파일 찾기 및 가져 오기

loader는 파일을 찾아 다운로드하는 것을 처리합니다. 먼저 진입점(entry point) 파일을 찾아야 합니다. HTML에서 스크립트 태그를 사용하여 loader를 찾을 위치를 알려줍니다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/08_script_entry-500x188.png" width="500" alt=""> 

그러나 main.js가 직접적으로 의존하는 다음 모듈을 어떻게 찾을 수 있습니까?
여기서 import 문이 들어옵니다. import 문 중 한 부분을 모듈 지정자라고 합니다. 로더에게 다음 모듈을 어디서 찾을 수 있는지 알려줍니다. 

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/09_module_specifier-500x105.png" width="500" alt=""> 

모듈 지정자에 대해 주목해야할 한 가지 사항 :    
브라우저와 Node간에 다르게 처리해야 할 때가 있습니다. 각 호스트는 모듈 지정자 문자열을 해석하는 고유 한 방법을 가지고 있습니다. 이를 위해 플랫폼마다 다른 모듈 해상도 알고리즘(module resolution algorithm)이라는 것을 사용합니다. 현재 Node에서 작동하는 일부 모듈 지정자는 브라우저에서 작동하지 않지만이를 해결하기위한 지속적인 작업이 진행 중입니다.  

이 문제가 해결 될 때까지 브라우저는 모듈 지정자로 URL 만 수락합니다. 해당 URL에서 모듈 파일을로드합니다. 하지만 그래프 전체가 동시에 그런 일은 일어나지 않습니다. 파일 분석을 하기 전까지는 모듈이 어떤 종속성을 필요로 하는지도 모르고... 파일을 가져올 때까지는 분석할 수 없습니다.  

즉, 트리를 하나씩 분석한 다음 하나의 파일을 파싱한 후 종속성을 파악해서 해당 종속성을 찾아로드해야 합니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/10_construction-500x302.png" width="500" alt=""> 

메인 스레드가 이들 파일 각각을 다운로드 할 때까지 대기해야한다면 많은 다른 작업들이 대기열에 쌓일 것입니다.
왜냐하면 브라우저에서 작업 할 때 다운로드하는 데 시간이 오래 걸리기 때문입니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/11_latency-500x270.png" width="500" alt=""> 

[Based on this chart.](https://twitter.com/srigi/status/917998817051541504)

이와 같이 메인 스레드를 차단하면 사용하기에는 너무 느린 모듈을 사용하는 앱을 만들 수 있습니다. 이것이 ES 모듈 스펙이 알고리즘을 여러 단계로 분할하는 이유 중 하나입니다. 자체 단계로 구성을 분할하면 브라우저가 파일을 가져 와서 모듈 그래프에 대한 이해를 높이기 전에 동기 화 작업을 수행 할 수 있습니다.

알고리즘을 단계로 나누는 이 접근법은 ES 모듈과 CommonJS 모듈의 주요 차이점 중 하나입니다. 

CommonJS는 시스템에서 파일을 로드하는데 인터넷을 통해 다운로드하는 것보다 훨씬 적은 시간이 걸리기 때문에 다르게 수행할 수 있습니다. 즉, 노드가 파일을 로드하는 동안 메인 스레드를 차단할 수 있습니다. 그리고 파일이 이미 로드되었으므로 인스턴스화하고 evaluation하는 것이 합리적이다(CommonJS에서는 별도의 단계가 아니다). 또한 모듈 인스턴스를 반환하기 전에 전체 트리를 걸어 내려가서 임의의 종속성을 로드, 인스턴스화 및 evaluation하는 것을 의미합니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/12_cjs_require-500x298.png" width="500" alt=""> 

CommonJS 접근법은 몇 가지 영향을 미치며, 나중에 그것에 대해 더 자세히 설명하겠습니다. 하지만 한 가지 의미는 CommonJS 모듈이 있는 Node에서 모듈 지정자에서 변수를 사용할 수 있다는 것입니다. 다음 모듈을 찾기 전에 이 모듈의 모든 코드(필요한 명령문까지)를 실행하고 있습니다. 즉, 모듈 resolution를 수행할 때 변수에 값이 있습니다.  
하지만 ES 모듈을 사용하면 평가하기 전에 미리 전체 모듈 그래프를 작성할 수 있습니다. 이는 모듈 지정자에 변수를 포함할 수 없다는 뜻이다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/13_static_import-500x146.png" width="500" alt=""> 

하지만 때로는 모듈 경로에 변수를 사용하는 것이 정말 유용합니다. 예를 들어, 코드가 실행 중인 환경이나 코드에 따라 로드할 모듈을 전환할 수 있습니다.    
ES 모듈에 이를 가능하게 하기 위해 proposal called dynamic import이 있습니다. 이를 통해 ```import(`$ {path} / foo.js`)```과 같은 import문을 사용할 수 있습니다.    
이 방법은 import()를 사용하여 로드된 모든 파일을 별도의 그래프의 진입점으로 처리하는 것입니다.   
동적으로 가져온 모듈은 별도로 처리되는 새로운 그래프를 시작합니다. 

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/14dynamic_import_graph-500x389.png" width="500" alt=""> 

한 가지 주의해야 할 점은 이 두 그래프에 포함된 모든 모듈이 모듈 인스턴스를 공유 할 것입니다.   
이는 로더가 모듈 인스턴스를 캐시하기 때문입니다. 특정 전역 범위에있는 각 모듈에는 하나의 모듈 인스턴스만 존재합니다.  

이것은 엔진에 대한 작업이 줄어든다는 것을 의미합니다. 예를 들어 여러 모듈이 종속되어 있어도 모듈 파일은 한 번만 가져올 수 있습니다. (이것이 모듈을 캐시하는 한 가지 이유입니다. evaluation 섹션에서 다른 것을 보게 될 것입니다.)  

로더는 모듈 맵이라는 것을 사용하여이 캐시를 관리합니다. 각 글로벌은 별도의 모듈 맵에서 모듈을 추적합니다.  
로더가 URL을 가져 오려고하면 URL을 모듈 맵에 넣고 현재 파일을 가져 오는 중임을 나타냅니다. 그런 다음 요청을 전송하고 다음 파일을 가져 오기 시작합니다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/15_module_map-500x170.png" width="500" alt=""> 

다른 모듈이 같은 파일에 의존하면 어떻게됩니까? 로더는 모듈 맵에서 각 URL을 검색합니다.   
거기에 fetching이 있으면 다음 URL로 이동합니다.  

그러나 모듈 맵은 가져 오는 파일을 추적하지 않습니다. 모듈 맵은 모듈에 대한 캐시 역할을합니다.  

#### Parsing

이제 이 파일을 가져왔으니 모듈 레코드로 Parsing해야 합니다. 이렇게 하면 브라우저가 모듈의 각기 다른 부분을 이해할 수 있습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/25_file_to_module_record-500x199.png" width="500" alt=""> 

모듈 레코드가 생성되면 모듈 맵에 저장됩니다. 즉, 여기서부터 요청할 때마다 로더가 맵에서 가져올 수 있습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/25_module_map-500x239.png" width="500" alt=""> 

parsing에는 사소한 것처럼 보일 수 있는 세부 사항이 하나 있지만 실제로는 큰 영향을 미칩니다.   
모든 모듈은 상단에 "use strict"한 것처럼 parsing됩니다.  
다른 사소한 차이점들도 있습니다. 예를 들어, 키워드 await는 모듈의 최상위 코드에 예약되어 있으며 'this'값은 'undefined'값입니다.  

이러한 parsing의 다른 방식을 "parse goal"라고 합니다.  
동일한 파일을 parse하지만 다른 목표를 사용하면 결과가 달라집니다. 따라서 모듈인지 여부에 관계없이 parsing 할 파일의 종류를 parsing하기 전에 알아야합니다.  

브라우저에서는 매우 쉽습니다. script 태그에 type = "module"을 넣으면됩니다.   
그러면 브라우저에 이 파일을 모듈로 parse해야 함을 알립니다. 모듈만 가져올 수 있기 때문에 브라우저는 imports가 모듈이라는 것을 알고 있습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/26_parse_goal-500x311.png" width="500" alt=""> 

그러나 Node에서는 HTML 태그를 사용하지 않으므로 type 속성을 사용할 수 없습니다.  
커뮤니티에서 이를 해결하려고 시도한 한 가지 방법은 .mjs 확장을 사용하는 것입니다. 이 확장자를 사용하면 Node는 "이 파일은 모듈입니다"라고 말합니다.   
사람들이 이 이야기를 parse goal의 수단으로 삼는 것을 보게 될 것이다.   
토론은 현재 진행 중이므로 Node 커뮤니티가 결국 어떤 수단을 사용하기로 결정했는지는 분명하지 않습니다.   

어느 쪽이든, 로더는 파일을 모듈로 parse할지 여부를 결정합니다. 모듈이고 가져 오기가있는 경우 모든 파일을 가져 와서 parse 할 때까지 프로세스를 다시 시작합니다.  

그리고 우린 끝이야! 로딩 프로세스가 끝나면 진입점 파일만 가지고 모듈 레코드 묶음까지 가지게되었습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/27_construction-500x406.png" width="500" alt=""> 

다음 단계는 모듈을 인스턴스화하고 모든 인스턴스를 함께 연결하는 것입니다.  

#### Instantiation

앞서 언급 한 것처럼 인스턴스는 code와 state를 결합합니다. state는 메모리에 있으므로 인스턴스화 단계는 모든 것을 메모리에 연결하는 것입니다.  

먼저, JS 엔진은 모듈 환경 레코드를 생성합니다. 모듈 레코드의 변수를 관리합니다. 그런 다음 모든 exports에 대해 메모리에있는 상자를 찾습니다. 모듈 환경 레코드는 각 export와 연관된 메모리의 상자를 추적합니다.  

메모리에있는이 상자들은 아직 값을 얻지 못합니다. evaluation 후에는 실제 값이 채워질 것입니다.  
이 규칙에 대한 한 가지주의 사항이 있습니다. export 된 함수 선언은 이 단계에서 초기화됩니다. 따라서 evaluation이 더 쉬워집니다.  

모듈 그래프를 인스턴스화하기 위해 엔진은 depth first post-order traversal를 수행합니다. 이는 그래프의 맨 아래, 즉 다른 것에 의존하지 않는 하단의 의존성에 이르게하여 해당 내보내기를 설정한다는 것을 의미합니다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/30_live_bindings_01-500x206.png" width="800" alt=""> 

엔진은 모듈 아래에있는 모든 exports - 모듈이 의존하는 모든 exports를 마무리합니다. 그런 다음 해당 모듈에서 가져온 항목을 연결하기 위해 레벨을 다시 설정합니다.  
export와 import는 모두 메모리의 동일한 위치를 가리 킵니다. 먼저 export을 연결하면 모든 import에 일치하는 export에 연결될 수 있습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/30_live_bindings_02-500x206.png" width="800" alt="">   

이것은 CommonJS 모듈과 다릅니다. CommonJS에서는 전체 export object가 내보낼 때 복사됩니다. 즉, 내보내는 값(숫자와 같은)은 복사본입니다.  

즉, 나중에 export 모듈이 해당 값을 변경하면 importi 모듈은 해당 변경 사항을 보지 못합니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/31_cjs_variable-500x113.png" width="500" alt="">   

반대로 ES 모듈에서는 라이브 바인딩이라고하는 것을 사용합니다. 두 모듈 모두 메모리의 동일한 위치를 가리 킵니다.  
즉, export 모듈에서 값을 변경하면 해당 변경 내용이 import 모듈에 표시됩니다.  

export 모듈은 언제든지 값을 변경할 수 있지만 import모듈은 해당 import값을 변경할 수 없습니다.  
즉, 모듈이 객체를 import한 경우 해당 객체에있는 속성 값을 변경할 수 있습니다.  
(Modules that export values can change those values at any time, but importing modules cannot change the values of their imports. That being said, if a module imports an object, it can change property values that are on that object.)

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/30_live_bindings_04-500x206.png" width="800" alt="">  

이와 같이 라이브 바인딩을 사용하는 이유는 코드를 실행하지 않고 모든 모듈을 연결할 수 있기 때문입니다. 이는 주기적 의존성을 가질 때 evaluation에 도움이됩니다. 아래에서 설명하겠습니다. 
이 단계가 끝나면, export/import 변수에 대한 모든 인스턴스와 메모리 위치가 연결됩니다.  
이제 코드를 평가하고 해당 메모리 위치를 값으로 채울 수 있습니다.   

#### Evaluation

마지막 단계는 이러한 상자를 메모리에 채우는 것입니다. JS 엔진은 함수 외부에있는 코드인 최상위 코드를 실행하여 이를 수행합니다.
이러한 상자를 메모리에 채우는 것 외에도 코드를 evaluation하면 부작용이 발생할 수 있습니다. 예를 들어 모듈이 서버를 호출 할 수 있습니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/40_top_level_code-500x146.png" width="500" alt="">  

부작용 가능성이 있기 때문에 모듈을 한 번만 평가할 수 있습니다. 정확히 동일한 결과로 여러 번 수행할 수 있는 인스턴스화에서 발생하는 링크와는 달리 evaluation는 몇 번을 수행하느냐에 따라 다른 결과를 가질 수 있습니다.  

이것이 모듈 맵을 갖는 한 가지 이유입니다. 모듈 맵은 표준 URL로 모듈을 캐시하여 각 모듈에 대해 단 하나의 모듈 레코드만 갖습니다. 따라서 각 모듈은 한 번만 실행됩니다. 인스턴스화와 마찬가지로, 이것은 depth first post-order traversal로 수행됩니다.  

우리가 이전에 이야기했던 사이클은 어떨까요?  

주기적 의존성에서는 그래프에 루프가 있습니다. 일반적으로 긴 루프입니다. 하지만 문제를 설명하기 위해 나는 짧은 루프를 가진 인위적인 예를 사용할 것입니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/41_cjs_cycle-500x224.png" width="700" alt=""> 

이것이 CommonJS 모듈에서 어떻게 작동하는지 살펴 보겠습니다. 먼저 주 모듈은 require 문까지 실행합니다. 그런 다음 counter 모듈을 로드합니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/41_cyclic_graph-500x281.png" width="700" alt=""> 

그런 다음 counter 모듈은 export object 메시지에 액세스하려고 시도합니다. 그러나 아직 main 모듈에서 evaluation되지 않았으므로 아직, 이것은 undefined로 반환 할 것입니다. JS 엔진은 로컬 변수에 메모리 공간을 할당하고 값을 undefined로 설정합니다.

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/42_cjs_variable_2-500x113.png" width="500" alt=""> 

evaluation는 counter 모듈의 최상위 코드의 끝까지 계속됩니다. 우리는 메시지에 대한 올바른 값을 얻을 수 있는지(main.js를 평가한 후)를 알고 싶어서 타임아웃을 설정한다. 그리고 나서 evaluation는 main.js에서 재개된다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/43_cjs_cycle-500x224.png" width="600" alt=""> 

메시지 변수가 초기화되어 메모리에 추가됩니다. 그러나 두 가지 사이에는 아무런 연관성이 없기 때문에 필요한 모듈에서 undefined로 유지됩니다.  

<img src="https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/44_cjs_variable_2-500x216.png" width="500" alt="">   

라이브 바인딩을 사용하여 export가 처리 된 경우 counter 모듈은 결국 올바른 값을 볼 수 있습니다. 타임 아웃이 실행될 때까지 main.js의 evaluation가 완료되어 값을 채웁니다.

이러한 사이클을 지원하는 것은 ES 모듈 설계의 큰 근거입니다. 이러한 3단계 설계가 가능해졌습니다.  


### What’s the status of ES modules? (ES 모듈의 상태는 어떻습니까?)

5월 초에 Firefox 60이 출시되면 모든 주요 브라우저가 기본적으로 ES 모듈을 지원합니다. Node는 또한 CommonJS와 ES 모듈 간의 호환성 문제를 파악하기위한 working group과 함께 지원을 추가하고 있습니다.  
즉, type = module 인 스크립트 태그를 사용하고 imports 및 exports를 사용할 수 있습니다. 그러나 더 많은 모듈 기능이 아직 제공되지 않습니다.  
dynamic import proposal은 사양 프로세스의 3 단계에 있으며 import.meta는 Node.js 사용 사례를 지원하는 데 도움이되며 모듈 해결 제안은 브라우저와 Node.js 간의 차이를 원활하게 해줍니다. 따라서 모듈을 사용한 작업이 앞으로 더 좋아 지리라 기대할 수 있습니다.  