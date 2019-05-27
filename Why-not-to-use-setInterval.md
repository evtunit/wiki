# 왜 setInterval을 쓰면 안되는가

> 최근에 저는 10초마다 ajax을 보내는 것과 같이 특정한 시간 간격 후에 반복적으로 기능에 call 하는 요구 조건을 발견했어요. 최상의 옵션은 설정된 것 같습니다. `setInterval`이 내 얼굴을 크래커처럼 날려버렸어요 :)

setInterval이 왜 악한지 이해하기 위해서 우리는 자바스크립트가 본질적으로  single threaded라는 사실을 명심해야 합니다. 

즉, 한 번에 두 개 이상의 작업을 수행하지 않는다는 것을 의미합니다.

function이 `setInterval` 에서 언급된 지연 시간보다 오래 걸리는 경우(예: 제때 완료되지 못할 수 있는 ajax call), breathing room이 없거나 setInterval이 리듬을 깨뜨립니다.

```js
    var fakeCallToServer = function() {
        setTimeout(function() {
            console.log('returning from server', new Date().toLocaleTimeString());
        }, 4000);
    }



    setInterval(function(){ 

        let insideSetInterval = new Date().toLocaleTimeString();

        console.log('insideSetInterval', insideSetInterval);

        fakeCallToServer();
    }, 2000);

//insideSetInterval 14:13:47
//insideSetInterval 14:13:49
//insideSetInterval 14:13:51
//returning from server 14:13:51
//insideSetInterval 14:13:53
//returning from server 14:13:53 
//insideSetInterval 14:13:55
//returning from server 14:13:55
```
> Try above code snippets in your console

console.log 문에서 볼 수 있습니다. `setInterval` 은 ajax call이 오든 안 오든 상관하지 않고 계속해서 ajax call을 보냅니다.

이렇게 하면 서버에서 많은 요청을 한 번에 대기열에 올릴 수 있습니다.

이제 `setInterval`에서 동기식 연산을 시도하겠습니다.


<br>

```js
var counter = 0;

var fakeTimeIntensiveOperation = function() {

    for(var i =0; i< 50000000; i++) {
        document.getElementById('random');
    }

    let insideTimeTakingFunction  = new Date().toLocaleTimeString();

    console.log('insideTimeTakingFunction', insideTimeTakingFunction);
}



var timer = setInterval(function(){ 

    let insideSetInterval = new Date().toLocaleTimeString();

    console.log('insideSetInterval', insideSetInterval);

    counter++;
    if(counter == 1){
        fakeTimeIntensiveOperation();
    }

    if (counter >= 5) {
       clearInterval(timer);
    }
}, 1000);

//insideSetInterval 13:50:53
//insideTimeTakingFunction 13:50:55
//insideSetInterval 13:50:55 <---- not called after 1s
//insideSetInterval 13:50:56
//insideSetInterval 13:50:57
//insideSetInterval 13:50:58
```

`setInterval`이 시간 집중적인 운용을 경험할 때, 

a) 트랙을 오르려고 하거나 

b) 새로운 리듬을 창조하는 두 가지 중 하나를 수행합니다. 

여기 크롬은 새로운 리듬을 만들어냅니다.

<br>

## 결론

비동기식 작업의 경우 `setTimeInterval`을 설정합니다. 간격은 요청의 긴 대기열을 생성하여 매우 역효과를 냅니다.

시간 집약적인 동기화 작업의 경우 `setTimeInterval`을 설정합니다. 간격이 리듬을 깨뜨릴 수 있습니다.

또한 `setInterval` 코드 블록에서 오류가 발생하면 실행을 중지하지 않고 잘못된 코드를 계속 실행합니다. 

말할 것도 없이 그들은 말할 것도 없고 clearInterval(간격) 기능으로 중지합니다.

또는 시간에 민감한 작업의 경우 `setTimeout`을 반복적으로 사용할 수 있습니다.

