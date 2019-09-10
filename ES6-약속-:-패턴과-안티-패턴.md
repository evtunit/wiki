몇 년 전에 NodeJS를 처음 시작했을 때, 나는 “콜백 지옥” 이라는 것에 감동을 받았습니다.  
다행히도 2017년 NodeJS는 최신의 가장 뛰어난 JavaScript기능을 채택 했으며 v4부터 Promise 들을 지원했습니다.  

그러나 promise 들은 코드를 더 간결하고 읽기 쉽게 만들지 만 콜백에만 익숙한 사람들에게는 다소 어려울 수 있습니다.  
여기서는 promise 들을 다루는 동안 배운 몇 가지 기본 패턴과 몇 가지 문제를 설명합니다.  

참고 : 이 도움말 전체에서 화살표 함수를 사용하였습니다. 익숙하지 않은 경우 매우 간단하지만 이점에 대해 읽어 보는 것이 좋습니다.  

## 패턴 및 모범 사례  

### promise들 사용
promise을 지원하는 타사 라이브러리를 사용하는 경우 사용법이 매우 간단합니다.  
걱정해야 할 함수는 then()과 catch()입니다.  
예를 들어 getItem(), updateItem() 및 deleteItem()의 세 가지 메소드가있는 API 클라이언트가 있으며 각각 Promise를 리턴합니다.  

then()을 호출 할 때마다 Promise 체인에 다른 단계가 생성되고 체인의 어느 지점에서든 오류가 발생하면 다음 catch() 블록이 트리거됩니다.  
then() 및 catch()는 모두 원시 값 또는 새로운 약속을 반환 할 수 있으며 결과는 체인의 다음 then()으로 전달됩니다.  

비교를 위해 다음은 콜백을 사용하는 동등한 로직입니다.  

주목해야 할 첫 번째 차이점은 콜백을 사용하면 모든 catch-all 오류 블록이 아닌 프로세스의 모든 단계에서 오류 처리를 포함해야한다는 것입니다.  
콜백의 두 번째 문제는보다 문체입니다. 각 단계는 가로로 들여 쓰기되어 약속 중심 코드를 볼 때 분명한 연속성을 숨깁니다.  

### 유망한 콜백들
알아야 할 첫 번째 요령 중 하나는 콜백을 promise로 전환하는 방법입니다.  
여전히 콜백을 사용하는 라이브러리 또는 고유한 레거시 코드를 사용하는 라이브러리를 사용하고 있지만  
다행스럽게도 promise로 감싸는 데 몇 줄 밖에 걸리지 않습니다.  
다음은 노드의 콜백 기반 fs.readFile을 promise 기반 함수로 변환하는 예입니다.  

중요한 부분은 promise 생성자입니다. 함수에는 함수와 함수의 두 가지 매개 변수가 있습니다.  
이 함수 안에는 우리가 모든 작업을 수행하는 곳이 있으며, 작업이 완료되면 resolve라고 부르고 오류가 있으면 거부합니다.  

해결 또는 거부 중 하나만 호출해야하며 한 번만 호출해야합니다.  
이 예에서 fs.readFile이 오류를 반환하면 해당 오류를 전달하여 거부합니다. 그렇지 않으면 파일 데이터를 전달하여 해결합니다.  

### 유망한 가치
ES6에는 promise.resolve() 및 promise.reject()라는 두 가지 편리한 핸들러 함수가 있습니다.  
예를 들어 promise를 반환해야하지만 특정 경우를 동기적으로 처리하는 함수가 있을 수 있습니다.

promise.reject()에 무엇이든 전달할 수 있지만 항상 오류를 전달하는 것이 좋습니다.  

### 동시에 실행
참고 : 이전 버전에서는 "동시"가 아닌 "병렬"이라는 단어를 잘못 사용했습니다. 차이점에 대한 자세한 설명은 주석을 참조하십시오.  
promise.all은 동시에 약속의 배열을 실행하는 편리한 방법입니다. 예를 들어 디스크에서 읽을 파일 목록이 있다고 가정합니다.  
위에서 만든 readFilePromise 함수를 사용하면 다음과 같습니다.

전통적인 콜백을 사용하여 동등한 코드를 작성하려고 시도조차 하지 않습니다. 말하기에 충분하면 지저분하고 버그가 발생할 수 있습니다.  

### 시리즈로 실행
때때로 많은 약속을 동시에 실행하면 문제가 발생할 수 있습니다.  
예를 들어 promise.all을 사용하여 API에서 많은 리소스를 검색하려고하면 속도 제한에 도달하면 429 오류로 응답하기 시작할 수 있습니다.  
한 가지 해결책은 promise를 연속적으로 실행하거나 다른 방법으로 실행하는 것입니다.  
불행히도 ES6에는 promise.all에 대한 간단한 아날로그가 없지만 Array.reduce가 우리를 도울 수 있습니다.

이 경우 다음 호출을 하기 전에 api.deleteItem()에 대한 각 호출이 완료 될 때까지 기다립니다.  
이것은 각 itemID에 대해 .then()을 작성하는 더 간결하고 일반적인 방법입니다.  

### Racing
ES6에서 제공하는 또 다른 편리한 핸들러 함수는 promise.race입니다.  
promise.all과 마찬가지로, 이것은 약속의 배열을 가져 와서 동시에 실행하지만 promise가 해결되거나 거부되면 다른 모든 결과를 버리고 반환합니다.  
예를 들어 특정 시간 후에 시간이 초과되는 promise를 만들 수 있습니다.

다른 promise는 계속 실행되므로 결과가 표시되지 않습니다.  

### Catching errors
오류를 잡는 가장 일반적인 방법은 .catch() 블록을 추가하는 것입니다. 이 블록은 앞의 .then() 블록에서 오류를 잡습니다.

여기서 getItem 또는 updateItem이 실패하면 catch() 블록이 트리거됩니다.  
그러나 getItem 오류를 개별적으로 처리하려면 어떻게해야합니까? 다른 catch()를 더 높이 삽입하면 다른 promise를 반환 할 수도 있습니다.

이제 getItem()이 실패하면 개입하여 새 항목을 만듭니다.  

### Throwing Errors
then() 문 안에있는 모든 코드는 try 블록 안에있는 것으로 간주해야합니다.  
둘 다 promise.reject()를 반환하고 새 Error()를 던지면 다음 catch() 블록이 실행됩니다.

즉, 런타임 오류는 catch() 블록도 트리거하므로 오류의 원인에 대해 가정하지 마십시오.  
예를 들어, 다음 코드에서 catch() 블록은 getItem에 의해 발생한 오류만 가져올 것으로 예상 할 수 있지만  
예제에서 볼 수 있듯이 then() 문에서 런타임 오류도 포착합니다.  

### Dynamic chains
때때로 우리는 promise 체인을 동적으로 구성하려고합니다. 특정 조건이 충족되면 추가 단계를 삽입합니다.  
아래 예제에서 주어진 파일을 읽기 전에 선택적으로 잠금 파일을 만듭니다.

promise = promise.then (/*...*/); 를 작성하여 promise의 가치를 업데이트 하십시오.  
아래에서 안티 패턴 호출 then()을 여러 번 참조하십시오.  

## 안티패턴
promise는 깔끔한 추상화이지만 특정 함정에 빠지기 쉽습니다. 다음은 내가 겪는 가장 빈번한 몇 가지 문제입니다.  

### Recreating callback hell (콜백지옥 재생성)
콜백에서 promise로 처음 옮길 때 오래된 습관을 버리기가 어려워 콜백처럼 promise를 중첩시키는 것을 발견했습니다.

이런 종류의 중첩은 거의 필요하지 않습니다.  
때로는 하나 또는 두 수준의 중첩이 관련 작업을 그룹화하는 데 도움이 될 수 있지만  
promise 중첩을 거의 항상 .then()의 수직 체인으로 다시 작성할 수 있습니다.  

### Failure to return
내가 자주 발생하는 오류는 promise 체인 내부에서 return 문을 잊어 버린 것입니다. 아래 버그를 발견 할 수 있습니까?

4행에서 api.updateItem() 앞에 return을 않았으므로 해당 then() 블록이 즉시 해결되며  
api.updateItem()이 완료되기 전에 api.deleteItem()이 호출됩니다.

내 생각에 이것은 ES6 promise의 주요 문제이며 종종 예기치 않은 동작을 유발합니다.  
문제는 .then()이 값 또는 새로운 promise를 반환 할 수 있고 undefined가 반환하기에 완벽하게 유효한 값이라는 것입니다.  
개인적으로 promise API를 담당했다면 .then()이 정의되지 않은 상태로 반환되면 런타임 오류가 발생했지만  
지금은 우리가 만든 약속을 조심스럽게 반환해야합니다.

### Calling .then() multiple times
사양에 따르면 동일한 promise에서 then()을 여러 번 호출하면 콜백이 등록 된 순서대로 호출됩니다.  
그러나 이 작업을 수행하는 유효한 이유를 본 적이 없으며 반환 값과 오류 처리를 사용할 때 미묘하게 예기치 않은 동작이 발생할 수 있습니다.

이 예제에서 then()을 호출 할 때마다 p의 값을 업데이트하지 않기 때문에 'b'가 반환되지 않습니다.  
그러나 then()을 호출 할 때마다 q를 업데이트하기 때문에 그 동작이 더 예측 가능합니다.
이것은 오류 처리에도 적용됩니다.

여기서 우리는 promise 사슬을 깨기 위해 오류를 던질 것으로 예상하지만 p의 값을 업데이트하지 않기 때문에 여전히 두 번째 then()에 도달합니다.
단일 promise에서 .then()을 여러 번 호출해야하는 유효한 이유가 있을 수 있습니다.  
promise를 여러 개의 새로운 독립된 약속으로 효과적으로 포크 할 수는 있지만 실제 사용 사례를 찾지 못했습니다.  

### Mixing callbacks and Promises
이것은 promise 기반 라이브러리를 사용하고 있지만 콜백 기반 프로젝트에서 여전히 작업하고 있다면 쉽게 넘어 질 수있는 함정입니다.  
then() 또는 catch() 내부에서 콜백을 항상 호출하지 마십시오. 그렇지 않으면 promise는 promise 체인의 일부로 후속 오류를 삼 킵니다.  
예를 들어 다음은 콜백으로 promise를 포장하는 합리적인 방법 인 것 같습니다.

여기서 문제는 오류가있는 경우 catch() 블록을 추가했지만 "처리되지 않은 promise 거부"에 대한 경고가 표시된다는 것입니다.  
callback()은 then()과 catch() 내부에서 호출되어 promise 체인의 일부이기 때문입니다.
콜백으로 promise를 반드시 래핑해야하는 경우 setTimeout (또는 NodeJS, process.nextTick)을 사용하여 promise를 벗어날 수 있습니다.  

### Not catching errors
JavaScript에서 오류 처리는 이상한 짐승입니다.  
친숙한 try / catch 패러다임을 지원하지만 발신자가 오류를 처리하도록 강제 할 수있는 방법은 없습니다. 자바는 않습니다.  
그러나 콜백에서는 소위 "errbacks"(첫 번째 매개 변수가 오류 인 콜백)를 사용하는 것이 일반적이되었습니다.  
이것은 호출자가 최소한 오류 가능성을 인식하도록합니다. 

예를 들어, fs 라이브러리를 사용하면 :
Promises를 사용하면 특히 파일 시스템 및 데이터베이스 액세스와 같은 민감한 작업의 경우  
오류를 명시 적으로 처리해야한다는 사실을 다시 쉽게 잊을 수 있습니다.  
현재 거부 된 promise를 잡지 못하면 NodeJS에 매우 추악한 경고가 표시됩니다.  

````
(node:29916) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): Error: whoops!
(node:29916) DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.
````
이를 방지하려면 기본 이벤트 루프에서 promise 체인의 끝에 catch()를 추가하십시오.

## 마무리
이것이 일반적인 promise 패턴 및 안티 패턴에 대한 유용한 개요가 되었기를 바랍니다. 
자세한 내용은 다음 몇 가지 유용한 자료를 참조하십시오.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

https://developers.google.com/web/fundamentals/primers/promises

https://medium.com/datafire-io

