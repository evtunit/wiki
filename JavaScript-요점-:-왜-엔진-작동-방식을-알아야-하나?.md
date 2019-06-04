![](https://cdn-images-1.medium.com/max/800/1*SKOyguJ-AC0DmiQ8ZS0FwQ.jpeg)

이 기사에서는 자바 스크립트를 사용하여 애플리케이션을 작성하는 소프트웨어 개발자가 작성된 코드가 올바르게 실행되도록  
엔진에 대해 알아야하는 것을 설명하고자합니다.  


아래에서 전달 된 인수의 lastName 속성을 반환하는 한 줄 함수를 볼 수 있습니다.  
각 객체에 하나의 속성을 추가하기 만하면 성능이 700% 이상 떨어집니다.  

자세히 설명 하겠지만 정적 유형이없는 JavaScript는 이러한 동작을 유발합니다.  
C #이나 Java와 같은 다른 언어보다 이점으로 여겨지면 "Faustian bargain(싸게 사는 물건)"이 될 수 있습니다.

## 최대 속도의 제동
일반적으로 우리는 코드를 실행하는 엔진의 내부를 알 필요가 없습니다.  
브라우저 공급 업체는 엔진의 코드 실행 속도를 높이는 데 많은 시간을 투자합니다.  

좋아!  

다른 사람들이 무거운 짐을 들어 올리도록 해라. 엔진이 어떻게 작동하는지 왜 신경써야 하는가?  

아래 코드 예제에서는 Star Wars 문자의 이름과 성을 저장하는 다섯 개의 객체가 있습니다.  
getName 함수는 lastname의 값을 반환합니다. 이 함수가 10 억 회 실행하는 데 걸리는 총 시간을 측정합니다. 

````
(() => {   
const han = {firstname: "Han", lastname: "Solo"};  
const luke = {firstname: "Luke", lastname: "Skywalker"};  
const leia = {firstname: "Leia", lastname: "Organa"};  
const obi = {firstname: "Obi", lastname: "Wan"};  
const yoda = {firstname: "", lastname: "Yoda"};  
const people = [ han, luke, leia, obi, yoda, luke, leia, obi ];  
const getName = (person) => person.lastname;

console.time("engine");  
for(var i = 0; i < 1000 * 1000 * 1000; i++) {     
 getName(people[i & 7]);   
}  
console.timeEnd("engine"); })();
````

Intel i7 4510U에서 실행 시간은 약 1.2 초입니다. 여태까지는 그런대로 잘됐다. 이제 각 객체에 다른 속성을 추가하고 다시 실행합니다.  

````
(() => {  
const han = {    
 firstname: "Han", 
 lastname: "Solo",     
 spacecraft: "Falcon" 
};  
const luke = {    
 firstname: "Luke", 
 lastname: "Skywalker",     
 job: "Jedi"
};  
const leia = {    
 firstname: "Leia", 
 lastname: "Organa",     
 gender: "female"
};  
const obi = {    
 firstname: "Obi", 
 lastname: "Wan",     
 retired: true
};  
const yoda = {lastname: "Yoda"};
const people = [ han, luke, leia, obi, yoda, luke, leia, obi];
const getName = (person) => person.lastname;
console.time("engine");  
for(var i = 0; i < 1000 * 1000 * 1000; i++) {    
 getName(people[i & 7]);  
}  
console.timeEnd("engine");})();

````
실행 시간은 8.5 초로, 첫 번째 버전보다 7 배 정도 느립니다. 이것은 최고 속도로 브레이크를 치는 것 같은 느낌입니다. 어떻게 그럴 수 있나?  
엔진을 자세히 볼 시간이다

## 연합군 : 통역사 및 컴파일러
엔진은 소스 코드를 읽고 실행하는 부분입니다. 주요 브라우저 공급 업체마다 자체 엔진이 있습니다.  
Mozilla Firefox에는 Spidermonkey, Microsoft Edge에는 Chakra / ChakraCore, Apple Safari에는  
엔진 JavaScriptCore라는 이름이 있습니다. Google 크롬은 Node.js의 엔진이기도 한 V8을 사용합니다.  
2008 년 V8 출시는 엔진 역사에서 중추적 인 순간이었습니다. V8은 브라우저가 자바 스크립트를 비교적 느리게 해석하는 것을 대체했습니다.

이러한 대규모 개선의 원인은 주로 인터프리터와 컴파일러의 결합에 있습니다. 오늘날 4 개의 엔진 모두이 기술을 사용합니다.  
인터프리터는 소스 코드를 거의 즉시 실행합니다. 컴파일러는 사용자 시스템이 직접 실행하는 시스템 코드를 생성합니다.  

컴파일러는 컴퓨터 코드 생성에서 작동하므로 최적화가 적용됩니다.  
컴파일과 최적화 모두 컴파일 단계에서 필요로하는 추가 시간에도 불구하고 더 빠른 코드 실행을 가능하게합니다.  

현대 엔진의 기본 아이디어는 두 세계의 장점을 결합하는 것입니다.
* 인터프리터의 빠른 응용 프로그램 시작.
* 컴파일러의 빠른 실행.

![](https://cdn-media-1.freecodecamp.org/images/xcTig2PibioQS1T5oImy5exOvcn43uda9R42)

두 가지 목표를 달성하는 것은 통역사에게 시작됩니다.  
병렬로, 엔진은 자주 실행되는 코드 부분을 "핫 경로"로 플래그 지정하고 실행 중에 수집 된 컨텍스트 정보와 함께 컴파일러에 전달합니다.  
이 프로세스를 통해 컴파일러는 현재 컨텍스트에 맞게 코드를 적용하고 최적화 할 수 있습니다.  


우리는 컴파일러의 행동을 "Just In Time"또는 단순히 JIT라고 부릅니다.  
엔진이 잘 실행되면 JavaScript가 C ++보다 우수한 특정 시나리오를 상상할 수 있습니다.  
대부분의 엔진 작업이 "상황 별 최적화"로 진행되는 것은 놀라운 일이 아닙니다.  

![](https://cdn-media-1.freecodecamp.org/images/bu47j9z9Dee3O3hHm51ijuARr3aUTtj4j4EH)
인터프리터와 컴파일러 간의 상호 작용

## 런타임 중 정적 유형 : 인라인 캐싱
인라인 캐싱 또는 IC는 JavaScript 엔진에서 주요 최적화 기술입니다.  
인터프리터는 객체의 속성에 액세스하기 전에 검색을 수행해야합니다.  
이 속성은 객체의 프로토 타입의 일부이거나 getter 메서드가 있거나 프록시를 통해 액세스 할 수 있습니다.  
속성 검색은 실행 속도면에서 상당히 비쌉니다.

엔진은 각 객체를 런타임 중에 생성하는 "유형"에 할당합니다.  
V8은 ECMAScript 표준, 숨겨진 클래스 또는 객체 셰이프의 일부가 아닌 이러한 "유형"을 호출합니다.  
두 객체가 동일한 객체 모양을 공유하려면 두 객체가 모두 동일한 순서로 동일한 속성을 가져야합니다.  
따라서 {firstname : "Han", lastname : "Solo"} 객체는 {lastname : "Solo", firstname : "Han"}과 다른 클래스에 할당됩니다.

객체 모양의 도움으로 엔진은 각 속성의 메모리 위치를 알고 있습니다. 엔진은 해당 위치를 해당 속성에 액세스하는 함수에 하드 코딩합니다.  

인라인 캐싱은 조회 작업을 제거합니다. 이것은 거대한 성능 향상을 가져 오지 않습니다.  

이전 예제로 돌아가십시오 : 첫 번째 실행의 모든 ​​객체에는 firstname과 lastname이라는 두 개의 속성 만 같은 순서로 있습니다.  
이 객체 모양의 내부 이름이 p1이라고 가정 해 보겠습니다.  
컴파일러가 IC를 적용하면 함수가 shapeep1 객체를 전달하고 성의 값을 즉시 반환한다고 가정합니다.

![](https://cdn-media-1.freecodecamp.org/images/o4aMw-H7fhu2dKraaGPkHqOiV0lMAJr7ks3j)

그러나 두 번째 실행에서는 5 가지 다른 오브젝트 모양을 처리했습니다.  
각 객체에는 추가 속성이 있었고 yoda에는 firstname이 완전히 누락되었습니다. 여러 객체 모양을 처리하면 어떻게 될까요?  

## 간섭 오리(?) 또는 다중 유형
기능적 프로그래밍에는 좋은 코드 품질이 여러 유형을 처리 할 수있는 기능을 요구하는 "오리 입력 (duck typing)"이라는 잘 알려진 개념이 있습니다.   우리의 경우 전달 된 객체가 lastname이라는 속성을 가진 한 모든 것이 정상입니다.  

인라인 캐싱은 속성의 메모리 위치에 대한 값 비싼 조회를 제거합니다. 각 속성에 액세스 할 때 개체의 개체 모양이 같을 때 가장 효과적입니다.  
이것은 monomorphic IC라고합니다.

우리가 최대 4 개의 다른 물체 모양을 가지고 있다면, 우리는 다형성 IC 상태에 있습니다.  
단일체와 마찬가지로 최적화 된 기계 코드는 이미 네 개의 모든 위치를 "인식"합니다.  
하지만 전달 된 인수가 속한 네 가지 가능한 객체 모양 중 하나를 확인해야합니다. 이로 인해 성능이 저하됩니다.  

우리가 4의 임계 값을 초과하면, 그것은 극적으로 악화됩니다. 우리는 현재 소위 megamorphic IC에 있습니다.  
이 상태에서는 더 이상 메모리 위치의 로컬 캐싱이 없습니다. 대신 전역 캐시에서 조회해야합니다.  
결과적으로 위에 언급 한 극단적 인 성능 저하가 발생합니다.

## 다형성과 megamorphic 활동
아래에서 우리는 2 개의 다른 객체 모양을 가진 다형성 인라인 캐시를 보았습니다.
![](https://cdn-media-1.freecodecamp.org/images/lNpKqU5ShHJkat0PpKIahkpFAwPOHpCJRIiA)

그리고 5 개의 다른 객체 모양을 가진 코드 예제의 megamorphic IC :

![](https://cdn-media-1.freecodecamp.org/images/cDEEsdQECGIz20LpuSYqgtUtjRCD1wD0hRPx)

## 복구에 대한 JavaScript 클래스
좋습니다, 그래서 우리는 5 개의 물체 모양을 가지고 있고 megamorphic IC를 만났습니다. 어떻게 해결할 수 있을까요?  

우리는 엔진이 5 개의 모든 물체를 동일한 물체 모양으로 표시했는지 확인해야합니다.  
즉, 우리가 만드는 객체에는 가능한 모든 속성이 포함되어야합니다.  
우리는 객체 리터럴을 사용할 수 있지만 JavaScript 클래스가 더 나은 해결책이라고 생각합니다.  

정의되지 않은 속성의 경우 단순히 null을 전달하거나 제외하십시오. 생성자는 이러한 필드가 값으로 초기화되는지 확인합니다.

````
(() => {  
  class Person {    
    constructor({      
     firstname = '',      
     lastname = '',      
     spaceship = '',      
     job = '',      
     gender = '',      
     retired = false } = {}){
      Object.assign(this, { firstname, lastname, spaceship, job, gender, retired });    
    }  
  }
 const han = new Person({ firstname: 'Han', lastname: 'Solo', spaceship: 'Falcon'  });  
 const luke = new Person({ firstname: 'Luke', lastname: 'Skywalker', job: 'Jedi'  });  
 const leia = new Person({ firstname: 'Leia', lastname: 'Organa', gender: 'female'  });  
 const obi = new Person({ firstname: 'Obi', lastname: 'Wan', retired: true  });  
 const yoda = new Person({ lastname: 'Yoda' });  
 const people = [ han, luke, leia, obi, yoda, luke, leia, obi ];  
 const getName = person => person.lastname;  
 console.time('engine');  
 for (var i = 0; i < 1000 * 1000 * 1000; i++) {    
  getName(people[i & 7]);  
 }  
 console.timeEnd('engine');})();
````
이 함수를 다시 실행하면 실행 시간이 1.2 초로 돌아갑니다. 작업 완료!

## 개요
최신 JavaScript 엔진은 인터프리터와 컴파일러의 장점 인 빠른 애플리케이션 시작과 빠른 코드 실행을 결합합니다.  

인라인 캐싱은 강력한 최적화 기술입니다. 단일 오브젝트 셰이프 만 최적화 된 기능으로 넘어갈 때 가장 효과적입니다.  

필자의 과감한 예는 인라인 캐싱의 다양한 유형과 megamorphic 캐시의 성능 저하에 따른 영향을 보여 줬습니다.  

JavaScript 클래스를 사용하는 것이 좋습니다. TypeScript와 같은 정적 유형화 된 변환기는 단일형 IC의 가능성을 높입니다.  



