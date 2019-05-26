# 부드러운 애니메이션을 위한 JavaScript의 requestAnimationFrame() 메소드의 이해

원문 : http://www.javascriptkit.com/javatutors/requestanimationframe.shtml

작성일(수정일) 2017-11-01

## 용어 사전 정의 (영->한)

---

- animation : 애니매이션
- interaction : 인터렉션(상호 작용)
- logic : 로직
- recursively : 재귀적으로
- element : 엘리먼트
- delay : 지연 시간
- function : 함수
- frame : 프레임
- resource : 자원
- reflow : 리플로우
- cardiac arrest : 심정지를 뜻하여 멈춰있음으로 번역
- layout thrashing : 레이아웃 스레싱
- [janky](https://www.dictionary.com/e/slang/janky/) page : 저성능의 페이지
- method : 메소드
- repaint : 리페인트
- vendor prefix : 벤더 프리픽스
- sync : 동기화
- fallback : 폴백
- nemesis counterpart : 의역;콜백 함수 실행을 시작할 때의 시점 참고 : [MDN](https://developer.mozilla.org/ko/docs/Web/API/Window/requestAnimationFrame)
- callback : 콜백
- parameter : 매개변수
- variable : 변수
- return : 반환

---

오늘날의 웹은 모든 페이지에서 다양한 볼거리로 가득 차 있습니다. 메뉴가 슬라이드 되거나 보이지 않게 되고, 내용이 부드럽게 사라지고, 사용자가 페이지를 스크롤 할 때 주위로 애니메이션이 생깁니다. CSS3는 Javascript를 대신하여 이러한 많은 애니메이션을 직관적이고 최적화된 형태로 실행되도록 도움을 주었습니다. 하지만 사용자의 인터렉션이나 비선형 로직이 포함된 보다 복잡한 시나리오에서는 Javascript를 사용합니다. 왜냐하면 Javascript의 requestAnimationFrame() 메소드로 인해 사용자의 화면을 효율적이고, 최적화 된 방식으로 변경하는 애니메이션 관련 코드를 실행할 수 있기 때문입니다. 많은 사람이 혜택을 제대로 이해하지 못하거나 적절하게 사용하는 방법과는 달리, 예전에는 이 방법에 대해서는 들을 수 없었습니다. 이 튜토리얼에서 우리는 requestAnimationFrame ()을 활용하여 스크립트에서 최적의 애니메이션을 수행할 수 있습니다.

## 왜 requestAnimationFrame()을 필요로 할까요?

우선 requestAnimationFrame()에 대한 아이디어와 왜 그 메소드가 필요한지 이야기해 보겠습니다. 전통적인 방법으로 Javascript로 애니메이션을 만들려면 재귀적으로 setTimeout()을 호출하거나, 반복적으로 setInterval()을 실행하도록 하여 엘리먼트를 프레임 단위로 변경(예: 50밀리초마다 한 번씩)하는 코드를 작성해야 합니다.

```javascript
var adiv = document.getElementById('mydiv')
var leftpos = 0
setInterval(function(){
    leftpos += 5
    adiv.style.left = leftpos + 'px' // move div by 5 pixels each time
}, 50) // run code every 50 milliseconds
```

위의 코드가 로직적으로는 맞아 보이지만 실제로 실행해보면 완벽하지 않습니다. 화면에서 무언가를 변경하는 코드를 실행하기 위해 setTmeout/setInterval을 사용할 때 두가지의 문제점이 있습니다.

- 이 함수들이 내부적으로 지연 시간(예, 50 milliseconds)의 값을 주었지만 사용자 시스템 자원들의 변화로 인해 종종 실행되지 않아 애니메이션 프레임 간에 일관성없는 지연 시간의 간격이 생깁니다.

- 심지어 더욱 좋지 않은 것은 setTimeout() or setInterval()을 사용하여 사용자의 화면을 계속 변경하면 종종 사용자의 화면이 물리적으로 표현되기 전에 불필요한 리플로우를 수행해야 하는 브라우저가 멈춰 있을 때 "레이아웃 스레싱"을 유발하여 변경 사항을 표시합니다. 이것은 페이지 리플로우의 과도한 특징으로 특히 저성능의 모바일 페이지 로드 또는 베터리 소모가 발생하여 매우 좋지 않은 영향을 줍니다.

## 해결책 - requestAnimationFrame()

위의 두 가지 단점으로 인해 requestAnimationFrame()이 도입되었습니다. 요약하면 이 메소드를 사용하면 다음번에 사용할 화면을 리페인트 시 코드를 실행할 수 있기 때문에 사용자의 브라우저와 동기화되지 않은 작업에 대한 추측과 화면을 변경하기 위한 하드웨어의 준비 상태가 사라집니다. requestAnimationFrame()을 반복적으로 호출하여 애니메이션을 만들면 사용자의 컴퓨터가 매번 화면을 변경할 준비가 되었을 때  애니메이션 코드가 호출되어 더 부드럽고 효율적인 애니메이션을 만들 수 있습니다. 또한 requestAnimationFrame()을 통해 호출되고 브라우저의 백그라운드 탭에서 실행되는 코드는 일시 중지되거나 사용자 시스템 리소스를 추가로 저장하기 위해 2프레임 이하로 느려지기 때문에 애니메이션을 실행하는 데 아무런 의미가 없습니다.

따라서 애니메이션의 경우 setTimeout/ setInterval 대신 requestAnimationFrame()을 사용해야 합니다. 그런데 정확히 어떻게 해야할까요? 먼저 브라우저 지원 범위을 살펴보세요. requestAnimationFrame()은 오늘날 IE10 +, FF11 +, Chrome 및 Safari 등의 최신 브라우저를 광범위하게 지원하고 있습니다. 이러한 브라우저의 일부 이전 버전에서는 이를 실행하게 하기 위해 메서드 이름 앞에 벤더 프리픽스가 필요합니다. 이 메소드를 지원하지 않는 어떠한 환경에서의 브라우저도 특정 지연 시간 이 후에 코드를 호출하기 위해 setTimeout()에 폴백할 수 있습니다. 다음 코드는 범용적으로 사용할 수 있는 requestAnimationFrame() 함수와 cancelAnimationFrame() 함수입니다 이 함수는 폴백이 내장되어 있습니다.

```javascript
window.requestAnimationFrame = window.requestAnimationFrame
    || window.mozRequestAnimationFrame
    || window.webkitRequestAnimationFrame
    || window.msRequestAnimationFrame
    || function(f){return setTimeout(f, 1000/60)} // simulate calling code 60 
 
window.cancelAnimationFrame = window.cancelAnimationFrame
    || window.mozCancelAnimationFrame
    || function(requestID){clearTimeout(requestID)} //fall back
```

대체 setTimeout() 코드의 경우 지연이 1000/60 또는 16.7 밀리초로 설정됩니다. 이것은 사용자의 화면 새로 고침 속도인 초당 60프레임을 기반으로 브라우저가 호출할 때마다 실제 requestAnimationFrame()이 일반적으로 얼마나 자주 호출되는지 시뮬레이트합니다.

## requestAnimationFrame()의 이해 및 사용

requestAnimationFrame의 구문은 매우 간단합니다.

```javascript
requestAnimationFrame(callback)
```

우리가 실행하고자 하는 코드를 포함하는 콜백 함수를 입력하고 requestAnimationFrame()은 화면이 다음 화면을 리페인트를 받아들일 준비가 되면 콜백 함수를 실행합니다.

주목할 만한 몇 가지 사항이 있습니다.

- 콜백 함수에는 requestAnimationFrame()이 호출된 정확한 시간을 나타내는 타임 스탬프가 자동으로 전달됩니다.

- requestAnimationFrame()은 콜백 함수 실행을 시작할 때의 시점(nemesis counterpart 의역)이 cancelAnimationFrame()에 전달되어 requestAnimationFrame() 호출을 취소할 수 있는 0이 아닌 정수를 반환합니다.
(requestAnimationFrame() returns a non 0 integer that can be passed into its nemesis counterpart cancelAnimationFrame() to cancel a requestAnimationFrame() call)

requestAnimationFrame ()을 한 번 호출하여 DIV를 화면의 원래 위치에서 단지 5픽셀 이동시키는 예입니다.

```javascript
var adiv = document.getElementById('mydiv')
var leftpos = 0
requestAnimationFrame(function(timestamp){
    leftpos += 5
    adiv.style.left = leftpos + 'px'
})
```

위의 코드는 setTimeout()을 사용하여 동일한 코드를 실행하는 것과 매우 유사합니다. 사용자가 정의한 지연 시간 이후에 호출하는 것이 아닌 다음 사용 가능한 화면을 리페인트할 때 코드를 호출합니다. (일반적으로 60fps, 화면 새로 고침 빈도에 따라 약 16.7 밀리 초) 정확한 수치는 변동이 될 수 있으며 중요하지는 않습니다. 여기서 중요하게 봐야할 부분은 브라우저가 아닌 이전 화면을 변경할 준비가 되었을 때만 브라우저가 지능적을 코드를 호출한다는 것입니다.

requestAnimationFrame()을 한 번 호출하면 대부분 무의미해집니다. 이것은 "재귀적이게" 호출될 때마다 프레임별로 원하는 애니메이션 프레임을 생성할 때 비로소 의미가 있어집니다. 각 프레임은 브라우저가 준비될 때만 호출됩니다. 이것은 애니메이션 관련 코드를 효율적으로 처리할 때 requestAnimationFrame ()이 setTimeout 또는 setInterval보다 우수한 방법입니다. requestAnimationFrame()을 사용하여 화면에서 DIV를 한 번에 5픽셀씩 이동하는 초기 예제를 다시 작성합니다.

```javascript
var adiv = document.getElementById('mydiv')
var leftpos = 0
function movediv(timestamp){
    leftpos += 5
    adiv.style.left = leftpos + 'px'
    requestAnimationFrame(movediv) // call requestAnimationFrame again to animate next frame
}
requestAnimationFrame(movediv) // call requestAnimationFrame and pass into it animation function
```

위의 코드는 requestAnimationFrame()을 사용하여 함수 내에서 애니메이션을 정의한 다음  requestAnimationFrame()을 통해 스스로 재귀적으로 호출하여 애니메이션의 각 프레임을 생성하기 위한 기본 blueprint를 보여준다. 애니메이션을 시작하려면, 해당 함수를 매개 변수로 사용하여 함수 밖에서 requestAnimationFrame()을 호출합니다.

## requestAnimationFrame()의 시간 경과에 따른 애니메이션

따라서 requestAnimationFrame()을 사용하여 반복적으로 애니메이션 함수를 호출하는 것만큼 간단하지만 대부분의 애니메이션은 특정 기능을 수행한 후 어느 시점에서 멈춰야 할 때가 까다롭습니다. 위의 DIV를 움직이는 예를 바탕으로 실제로 구현할 법한 시나리오를 'DIV를 2초 정도의 시간 동안 400픽셀 오른쪽으로 이동'으로 정의해보겠습니다. requestAnimationFrame()을 사용하여 작업을 수행하려면 콜백 함수에 전달된 timestamp 매개 변수를 활용할 수 있습니다. 위의 movediv 코드를 다시 수정하여 DIV를 일정 시간 동안 일정한 거리만큼 이동 시키면 이것이 어떻게 작동하는지 볼 수 있습니다.

```javascript
var adiv = document.getElementById('mydiv')
var starttime
 
function moveit(timestamp, el, dist, duration){
    //if browser doesn't support requestAnimationFrame, generate our own timestamp using Date:
    var timestamp = timestamp || new Date().getTime()
    var runtime = timestamp - starttime
    var progress = runtime / duration
    progress = Math.min(progress, 1)
    el.style.left = (dist * progress).toFixed(2) + 'px'
    if (runtime < duration){ // if duration not met yet
        requestAnimationFrame(function(timestamp){ // call requestAnimationFrame again with parameters
            moveit(timestamp, el, dist, duration)
        })
    }
}
 
requestAnimationFrame(function(timestamp){
    starttime = timestamp || new Date().getTime() //if browser doesn't support requestAnimationFrame, generate our own timestamp using Date
    moveit(timestamp, adiv, 400, 2000) // 400px over 1 second
})
```

이것이 어떻게 실행되는지 살펴보겠습니다.

- 애니메이션이 실행되기 직전에 requestAnimationFrame의 timestamp 매개 변수를 사용하여 starttime 변수를 현재 시각으로 설정하거나 requestAnimationFrame이 지원되지 않는 경우 정확성이 떨어지는 new Date().getTime()을 대신 사용합니다. 애니메이션이 실행되기 직전에 requestAnimationFrame의 콜백 함수의 첫 번째 매개변수인 자동으로 전달된 값이 현재 시각을 밀리
초 단위로 정확하게 표시합니다. (정확히 5마이크로 초). 이를 통해 애니메이션이 언제 시작되었는지 알 수 있습니다.

- 애니메이션 함수 moveit() 내에서 변수 timestamp를 사용하여 현재 "프레임"의 현재 시각을 캡처합니다. 우리는 애니메이션 시작 시점과의 차이점을 사용하여 현재 애니메이션에 있는 "지점"을 파악하고 총 거리 (즉 : 400픽셀)에서 DIV의 위치를 변경합니다.

## requestAnimationFrame()의 속도 저하 또는 취소

표준 requestAnimationFrame은 이상적인 조건에서 약 60fps (또는 16.7ms마다 한 번)에서 일반 모니터의 재생 빈도와 동기화되어 실행됩니다. 애니메이션이 초당 다른 프레임 (최대 60fps)을 필요로 하거나 간단히 높은 재생 빈도를 요구하지 않으면 setTimeout() 내 requestAnimationFrame을 호출하여 느려질 수 있습니다.

아래와 같은 방법으로 적용하면 requestAnimationFrame의 이점을 누리는 동안 원하는 프레임 속도를 얻을 수 있습니다

```javascript
var adiv = document.getElementById('mydiv')
var leftpos = 0
var fps = 20
function movediv(timestamp){
    setTimeout(function(){ //throttle requestAnimationFrame to 20fps
        leftpos += 5
        adiv.style.left = leftpos + 'px'
        requestAnimationFrame(movediv)
    }, 1000/fps)
}
 
requestAnimationFrame(movediv)
```

DIV를 수평으로 움직이는 이 버전에서는 매번 setTimeout () 내에 requestAnimationFrame을 호출하여 초당 프레임을 약 20개로 조절합니다.

## requestAnimationFrame() 취소 실행

setTimeout/ setInterval과 마찬가지로 requestAnimationFrame 호출을 취소 할 수 있으며 동일한 방식으로 호출할 수 있습니다. requestAnimationFrame이 호출되면 변수 내에서 캡쳐한 값인 콜백 함수 실행을 시작할 때의 시점의 값을 cancelAnimationFrame()에 전달하여 콜백 요청을 취소 하는 0이 아닌 정수가 반환됩니다.(requestAnimationFrame when called returns a non 0 integer that can be captured inside a variable and passed into its nemesis counterpart cancelAnimationFrame() to stop it from being invoked again)
아래의 코드는 cancelAnimationFrame을 사용하여 콜백을 중지하기 위해 requestAnimationFrame의 timestamp 매개 변수 값을 2초 동안 기록하는 코드입니다.

```javascript
var reqanimationreference
 
function logtimestamp(timestamp){
    console.log(timestamp)
    reqanimationreference = requestAnimationFrame(logtimestamp)
}
 
requestAnimationFrame(logtimestamp)
 
setTimeout(function(){ // cancel requestAnimationFrame after 2 seconds
    cancelAnimationFrame(reqanimationreference)
}, 2000)
```

데모에 대한 설명
다음은 마우스가 부모 컨테이너(onmouseenter)에 들어갈 때 requestAnimationFrame을 사용하여 DIV의 너비를 계속 변경하고 onmouseleave를 취소를 하는 정교한 예제입니다.

## 결론

requestAnimationFrame()은 실제로 목적과 공통 패턴을 이해하면 개념과 구현이 매우 간단합니다. jQuery 3.0과 같은 많은 프레임 워크는 requestAnimationFrame()을 모든 애니메이션 관련 함수에 내부적으로 활용합니다. 그러나 이 메서드가 기본적으로 작동하는 방식을 이해하면 모든 JavaScript 환경에서이 메서드를 활용할 수 있습니다.