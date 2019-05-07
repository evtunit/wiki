함수는 자바스크립트에서 첫번째 클래스 시민으로 간주되므로 JS에서 함수를 만드는 개념으로 명확해야한다.  
  
  
![](https://cdn-images-1.medium.com/max/1600/1*dAwQkc-E0j1AcpdPeGznzg.png)  

다른 프로그래밍 언어와 달리 자바스크립트는 3가지 방법으로 함수를 만들 수 있다.
1. 함수 선언
2. 함수 표현식
3. 명명된 함수 표현식

## 함수 선언
````
function [name](param1, param2, ...param3) {
    // Function Body & Logic
}
````
이 경우, 각각의 [함수 명] 앞에 [함수 키워드] 를 붙입니다.  
이 접근법의 한 가지 주요 이점은 함수 전체가 호이스트 된다는 것입니다.  
이 때문에 함수를 선언하기 전에 함수를 실행 할 수 있습니다.  
일부 논리를 함수 본문으로 추상화하고 나중에 실제 구현이 완료 될 때 유용합니다.  

````
var num1 = 10;
var num2 = 20;
var result = add(num1, num2); // ==> 30 [Executing before declaring]
function add(param1, param2) {
    return param1 + param2 ;
}
````
모범사례 : JavaScript 호이스팅 대신 함수를 먼전 선언하고 실행하시오.  


## 함수 표현식
어떤 값을 다른 변수에 할당하는 명령문은 표현식으로 간주됩니다.
````
var a = 100;
var b = 'Hello World';
````
함수 표현식의 경우 이름없이 함수가 생성된 다음 변수에 할당 됩니다.  
````
var [name] = function(param1, param2, ...param3) {
    // Function Body & Logic
}
foo(1,3,4);
````
한계 : 기능은 정의될 때까지 실행할 수 없다.
````
var num1 = 10;
var num2 = 20;
var result = add(num1, num2);  
// Uncaught TypeError: add is not a function
var add = function(param1, param2) {
    return param1 + param2 ;
} 
````
아래의 접근법이 효과가 있을 것이다.

````
var num1 = 10;
var num2 = 20;
var add = function(param1, param2) {
    return param1 + param2 ;
}
var result = add(num1, num2); // ==> 30
````

## 명명된 함수 표현식 - 두 접근법의 조합
이제 선언과 표현 함수의 차이점을 이해했으니, 두 가지를 함께 섞으면 어떤 일이 벌어지는지 알아봅시다.  

````
var num1 = 10;
var num2 = 20;
var addVariable = function addFunction(param1, param2) {
    return param1 + param2 ;
}
````
함수가 더 이상 익명이 아니며 addFunction 이라는 이름이 있다는 사실에 주의하십시오.  
또한 변수 - addVariable에 할당됩니다.  

함수 키워드에 이름을 추가했다고 해서 그 이름으로 실행될 수 있는 것은 아닙니다.  

````
var result = addFunction(num1, num2); 
// ==> Uncaught ReferenceError: addFunction is not defined
````
오히려 할당된 변수 이름 addVariable만 사용하여 참조하고 실행할 수 있습니다.  

````
var result = addVariable(num1, num2); 
// ==> 30
````

## 고려해야 할 중요한 사항 : 
1. addFunction은 디버깅 도구의 호출 스택에서 addVariable을 덮어씁니다.

![명명된 함수 표현식](https://cdn-images-1.medium.com/max/1600/1*CPUa1X3-O55XPPMz7MBVkA.png)

![함수 표현식](https://cdn-images-1.medium.com/max/1600/1*QsAXJNRrrI49ZkDo3WCrnA.png)

2. 명명된 함수 식인 경우 addFunction() 외부 호출 오류
````
Uncaught ReferenceError : addFunction이 정의되지 않음
그러나 당신은 그것을 함수 내부로부터 부를 수 있으며 그것은 효과가 있을 것이다.

var num1 = 10;
var num2 = 20;
var addVariable = function addFunction(param1, param2) {
   var res = param1 + param2;
   if (res === 30) {
        res = addFunction(res, 10);
   }
   return res;
}
var result = addVariable(num1, num2); // ==> 40
````

이 시나리오에서는 res가 30인지 확인하고 있다.
만일 그렇다면, 최종 출력 40을 반환하는 파라미터로 30 & 10의 이름 addFunction으로 다시 같은 함수를 호출한다.

3. IE8 이하에서 명명된 함수 표현식을 사용할 때의 한가지 심각한 주의사항은 완전히 다른 두 시간에 두 개의 함수 개체가  
   완전히 분리된 기능 개체로 잘못 생성된다는 것이다.(더블 테이크)
   * IE8을 지원해야 할 경우 익명 함수 표현이나 함수 선언은 고수하되 명명된 함수 표현은 피하는 것이 가장 좋다.

이 기사가 명명된 함수 표현과 함께 함수 선언과 함수 표현식 간의 차이점에 대한 명확한 맥락을 당신에게 줄 수 있기를 바란다.  
