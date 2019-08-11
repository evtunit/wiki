#자바스크립트 클로저를 이해하지 못했습니다.

(...음?역설이겠지...)

제목에서 유추할 수 있듯이 자바스크립트 클로저는 항상 미스테리입니다. 수 많은 글들을 읽고, 업무에서 클로저를 사용해보고, 가끔은 클로저를 사용하는 중에 깨달음없이 클로저를 사용 했었습니다.

최근에 마침내 클로저를 설명해주는 토크 자리에 갔습니다. 이 글에서 클로저를 설명하기 위해 이 방법을 사용할 것입니다. [CodeSmith][(https://www.codesmith.io/)]와r *JavaScript The Hard Parts* 시리즈에 참여한 분에게 공을 돌리겠습니다

## 시작하기 전에

어떤 개념들은 클로저를 살펴보기 전에 더 살펴보는 것이 중요합니다. 그것들 중에 하나가 실행 컨텍스트입니다.

[This article](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/) 는 실행 컨텍스트를 잘 설명한 글입니다. 이 글에서 말하는 내용은:

> 자바스크립트에서 코드가 실행될 때, 코드가 실행되는 환경은 매우 중요하고, 다음 중 하나로 평가되고 있습니다. :
>
> **전역 코드** — 코드가 처음으로 실행되는 기본 환경.
>
> **함수 코드** — 실행의 흐름이 함수의  본문으로 들어 갈 때마다.
>
> (…)
>
> (…), 실행 컨텍스트의 용어는 현재의 코드가 평가되고 있는 환경, 스코프라고 정의해봅니다.

바꾸어 말하면, 프로그램을 실행했을 때 전역 실행 컨텍스트에서 시작합니다. 몇몇 변수들은 전역 실행 컨텍스트 내부에서 선언이 되어져집니다. 전역 변수들을 호출합니다.  프로그램이 함수가 호출 하였을 때, 무슨 일이 일어날까요?

몇 가지 스텝이 있습니다:

1. 자바스크립트는 새로운 실행 컨텍스트, 지역 실행 컨텍스트를 만듦니다.
2. 지역 실행 컨텍스트는 자기 자신의 변수 집합을 가지고 있습니다. 이들 변수들은 지역에서 실행 컨텍스트가 될 것입니다.
3. 새로운 실행 컨텍스트는 실행 스택에 추가됩니다. 실행 스택은 실행 중인 프로그램의 위치를 추적하는 메커니즘이라고 생각합니다.

언제 함수가 끝이 날까요? 언제 반환(return) 구문이 있거나 닫히는 중괄호 } 가 있는 경우. 함수가 끝났을 때 아래의 내용이 발생합니다.:

1. 지역 실행 컨텍스트는 실행 스택에서 분리됩니다.
2. 함수는 반환 값을 호출 컨텍스트로 다시 전달합니다. 호출 컨텍스트는 이 함수를 호출한 실행 컨텍스트로서 글로벌 실행 컨텍스트 또는 다른 지역 실행 컨텍스트입니다. 그 시점에서 반환값을 처리하는 것은 호출 실행 컨텍스트에 달려 있습니다. 반환값은 객체, 배열, 함수, 부울 등 무엇이든 될 수 있습니다. 만약 함수에 반환 구문이 없다면, undefined을 반환합니다.
3. 지역 실행 컨텍스트는 소멸되는 것은 중요 합니다.소멸되다. 지역 실행 컨텍스트 내에서 선언된 모든 변수는 삭제됩니다. 변수는 더이상 사용할 수 없습니다. 그래서 지역 변수라고 부릅니다.

## 매우 기본적인 예

클로저를 알기 전에 다음 코드를 살펴봅시다. 이것은 이 글을 읽는 사람은 아래의 코드가 무엇을 하는지 정확히 알고 있을 것입니다.

```javascript
1: let a = 3
2: function addTwo(x) {
3:   let ret = x + 2
4:   return ret
5: }
6: let b = addTwo(a)
7: console.log(b)
```

자바스크립트 엔진의 실제 작동 방식을  이해하기 위해 자바스크립트 엔진의 실제 작동 방식을 아주 자세하게 알아보도록 합시다.

1행에서 전역 실행 컨텍스트에 새로운 변수 a를 선언하고 숫자 3을 할당합니다.

1. 다음은 까다롭습니다. 2행에서 부터 5행은 실제로 함께 있습니다. 이 부분에서 무슨 일이 일어날까요? 전역 실행 컨텍스트에 addTwo 이름을 가진 새로운 변수를 선언합니다. 그리고 addTwo에 무엇이 할당될까요? 함수를 정의합니다. 두 개의 중괄호 {} 사이에 있는 것이 addTwo에 할당합니다. 코드의 내부에 함수는 검증 되지 않고, 실행되지 않고 미래에 사용될 변수를 저장흡니다. 
2. 그 다음 6행으로 넘어 옵니다. 이 부분은 매우 간단해 보이지만 이곳에서 합쳐져 있지 않습니다. 첫번째로 전역 실행 컨텍스트에서 새로운 변수가 선언이 되고, label을 b로 지정합니다. 변수가 선언되자 마자 undefined의 값을 가집니다.
3. 그다음, 여전히 6행입니다. 연산자를 할당한 것을 볼수 있습니다. 변수 b를 새로운 변수로 할당하기 위해 기다립니다. 그 다음 함수가 호출된 것을 볼 수 있습니다. 변수 뒤에 둥근 괄호 (...)가 표시되면 이 것이 함수가 호출이 되고 있다는 신호 입니다. 순식간에 모든 함수는 무언가(값, 객체 또는 정의되지 않은)를 반환합니다. 함수에서 반환된 것은 변수 b에 할당됩니다. 함수에서 반환된 것은 변수 b에 할당이 됩니다.
4. 그러나 첫번째로 addTwo라고 레이블된 함수를 호출해야 합니다. 자바스크립트는  전역 실행 컨텍스트 메모리에서 addTwo라는 변수를 찾습니다. addTwo변수를 찾으면 step2(2행에서 5행)에서 정의 되어 집니다. 그리고  lo 그리고 addTow는 함수 정의를 포함하고 있습니다. 변수 a는 함수의 인자로 전달이 되어 집니다. 자바스크립트는 전역 실행 컨텍스트 메모리에서 변수를 찾습니다. 변수를 찾으면 값 3을 가지고, 함수의 인자로 숫자 3이 전들이 됩니다. 함수의 실행을 기다립니다.
5. 실행 컨텍스트는 전환이 됩니다. 새로운 지역 실행 컨텍스트가 생성이 되어지고 이것은 addTwo 실행 컨텍스트라는 이름을 가집니다. 실행 컨텍스트는 콜 스택에 쌓여집니다. 지역 실행 컨텍스트에서 무엇이 처음으로 호출이 되나요?
6. 새로운 변수 ret은 지역 실행 컨텍스트에 선언이 되어져있습니다라는 대답은 옳지 않습니다. 옳은 대답은 함수의 첫번째로 함수의 파라미터를 볼 필요가 있습니다. 새로운 변수 x는 지역 실행 컨텍스트에 선언이 되어 있습니다. 그리고 3 값이 인자로 전달이 되어졌기 때문에 변수 x에 숫자 3이 할당되어 집니다.
7. 그 다음 스탭은 새로운 변수 ret은 지역 실행 컨텍스트에 선언이 되어져 있습니다. 값은 undefined가 셋팅이 되어 집니다. (3행)
8. 3행에서 여전히, 추가적인 작업이 필요 합니다. 첫번째로 x의 값이 필요로 합니다. 자바스크립트는 변수 x를 찾습니다. 첫번째로 변수 x를 지역 실행 컨테스트에서 찾습니다. 그리고 하나를 발견하고, 값은 3입니다. 그리고 두번째 피연산자는 숫자 2입니다. 그리고 변수 ret에 더해진 결과값인 5가 할당이 됩니다.
9. 4행. ret 변수의 값이 반환이 되어 집니다. 지역 실행 컨텍스트의 다른 것을 찾습니다. ret은 5값을 포함함니다. 함수는 숫자 5를 반환합니다. 그리고 함수는 끝이 납니다.
10. 4행, 5행. 함수는 끝이납니다.지역 실행 컨텍스트는 소멸이 됩니다. 변수 x 그리고 ret은  사라집니다. x와 ret은 더이상 존재하지 않습니다. 컨텍스트는 콜스택에서 빠져나가고 반환되는 값은 호출 컨텍스트에 반환되어 집니다.  이 경우에 호출 컨텍스트는 전역 실행 컨텍스트 입니다. 함수 addTwo가 전역 실행 컨텍스트에서 호출되었기 때문입니다.
11. 지금 4단계 중 한 부분을 가져 옵니다. 변수 b는 반환되어진 값 (숫자 5)를 얻어 할당이 됩니다. 여전히 작은  프로그램의 6행에 있습니다.
12. 자세히 설명하지는 않지만 8행에서 변수 b의 내용이 콘솔에 보여집니다. 이 예에서는 숫자 5입니다.

매우 간단한 프로그램에 대한 매우 긴 설명이었습니다. 약속 드립니다. 그러나 먼저 다른 쪽을 설명해야 합니다.

## 렉시컬 스코프

렉시컬 스코프에 대해 일부를 이해할 필요가 있습니다. 아래의 예제 코드를 봅시다.

```javascript
1: let val1 = 2
2: function multiplyThis(n) {
3:   let ret = n * val1
4:   return ret
5: }
6: let multiplied = multiplyThis(6)
7: console.log('example of scope:', multiplied)
```

이 아이디어는 지역 실행 컨텍스트 그리고 전역 실행 컨텍스트에서의 변수를 가집니다. 자바스크립트에서 어려운 점은 변수를 찾는 것입니다. 만약 지역 실행 컨텍스트에서 변수를 찾을 수 없다면, 호출 컨텍스트에서 변수를 찾을 것입니다. 그리고 만약 실행 컨텍스트에서 변수를 찾을 수 없다면 전역 실행 컨텍스트에서 찾을 때까지 반복합니다.(만약 전역 실행 컨텍스트에서 찾을 수 없다면 undefined입니다.) 위의 예제가 명확합니다. 만약 스코프에 대해서 이해하지 않았다면 이 부분은 스킵해도 됩니다.

1. 전역 실행 컨텍스트에서 새로운 변수 val1을 선언하고 숫자 2를 할당합니다.
2. 2행-5행. 새로운 변수 multiplyThis를 선언하고 함수를 정의합니다.
3. 6행. 전역 실행 컨텍스트에 새로운 변수 `multiplied`을  선언합니다. 
4. 전역 실행 컨텍스트 메모리에서 변수 multiplyThis를 검색하여 함수로 실행하고 숫자 6을 인자로 전달합니다. 
5. 새로운 함수 호출은 새로운 실행 컨텍스트 입니다. 새로운 지역 실행 컨텍스트가 생성이 됩니다.
6. 지역 실행 컨텍스트에서 변수 n이 선언이 되고 숫자 6이 할당이 됩니다.
7. 3행. 지역 실행 컨텍스트에서 변수 ret이 선언이 됩니다.
8. 3행 (아직 진행중). 두 개의 연산자로 곱셈을 수행합니다. 변수 n과 val1. 지역 실행 컨텍스트에서 n변수를 찾습니다.  6단계에서 선언이 되어 집니다. 이 본문은 숫자 6입니다. 지역 실행 컨텍스트에서 val1의 값을 찾습니다. 지역 실행 컨텍스트는 레이블된 val1을 가지고 있지 않습니다. 호출 컨텍스트를 확인합니다. 호출 컨텍스트는 전역 실행 컨텍스트 입니다. 전역 실행 컨텍스트에서 val1을 찾습니다. 찾았습니다.1단계로 정의가 되었습니다. 이 값은 숫자 2입니다.
9. 3행(아직 진행중).  두개의 연산자로 곱하고 ret 변수에 할당을 합니다.6 * 2 = 12. `ret` 은 12입니다.
10. 변수 ret이 반환이 되었습니다. 지역 실행 컨텍스트는 변수 ret과 n과 함께 소멸이 됩니다. 변수 val1은 아직 소멸되지 않았고, 전역 실행 컨텍스트의 일부분으로 존재 합니다.
11. 6행으로 돌아갑니다. 호출 컨텍스트에서 숫자 12는 multiplied 변수에 할당이 됩니다.
12. 마지막으로 7행, multiplied 의 변수값이 콘솔에 보여집니다.

따라서 이 예제에서는 함수가 호출 컨텍스트에 정의 된 변수에 접근할 수 있음을 기억해야 합니다. 이 것을 렉시컬 스코프라고 합니다.

## 함수를 반환하는 함수

첫번째 예제에서는  addTwo 함수가 숫자를 반환합니다.  In the first example the function `addTwo` returns a number. 함수는 무엇이든 반환할 수 있다는 것을 기억 하세요. 이제 함수가 함수를 반환하고, 클로저를 이해할 수 있는 필수 예제 입니다. 다음은 분석할 예제 입니다.

```javascript
 1: let val = 7
 2: function createAdder() {
 3:   function addNumbers(a, b) {
 4:     let ret = a + b
 5:     return ret
 6:   }
 7:   return addNumbers
 8: }
 9: let adder = createAdder()
10: let sum = adder(val, 8)
11: console.log('example of function returning a function: ', sum)
```

단계별로 진행 하겠습니다.

1. 1행. 전역 실행 컨텍스트에서 변수 val이 선언이 되어지고 숫자 7이 할당이 되어 집니다.
2. 2행–8행. 전역 실행 컨텍스트에  createAdder이름으로 선언이 되어지고, 정의한 함수가 할당이 되어 집니다. 3행에서 7행은 함수 정의를 설명합니다. 그 전에 이 부분에서 함수로 넘어 가지 않습니다. 변수 (createAdder)에 정의된 함수가 저장이 되어 집니다.
3. 9행. 전역 실행 컨텍스트에 adder이름을 가진 새로운 변수를 선언합니다. 일반적으로 adder에 undefined가 할당이 됩니다.
4. 여전히 9행. ();을 볼 수 있습니다. 실행 또는 함수 호출이 필요 합니다. 전역 실행 컨텍스트 메모리에 질의를 하고 createAdder이름을 가진 변수를 찾습니다. 이것은 2단계에서 생성이 되어 집니다. 그리고 호출이 됩니다.
5. 함수 호출. 2행에서. 새로운 지역 실행 컨텍스트는 생성이 되어집니다. 새로운 실행 컨텍스트에서 지역 변수가 만들어 집니다. 엔진은 콜스택에 새로운 컨텍스트를 추가 합니다. 함수는 인사를 가지고 있지 않습니다. 오른쪽으로 이동하고 본문으로 들어갑니다.
6. 여전히 3-6행. 새로운 함수를 선언합니다. 지역 실행 컨텍스트에 addNumbers 변수로 생성합니다. 이 부분이 중요 합니다. addNumbers는 지역 실행 컨텍스트에서 하나만 존재 합니다.addNumbers이름을 가진 지역 변수에서 함수가 정의된 것이 저장됩니다.
7. 7행. addNumbers의 변수에 본문이 반환됩니다. 엔진은 addNumbers의 변수를 찾고, 찾습니다. 함수를 정의 합니다. 좋습니다. 함수는 함수를 정의한 것을 포함하고 그 어느것을 반환할 수 있습니다. 따라서 addNumbers의 정의한 것을 반환합니다. {} 사이에 있는 것을 함수 정의를 구성합니다.또한 호출 스택에서 로컬 실행 컨텍스트를 제거 합니다.
8. 반환 (return)하면 로컬 실행 컨텍스트가 삭제 됩니다.addNumbers 변수는 더 이상 없습니다. 정의한 함수는 여전히 존재하지만 함수에서 반환되며 'addr'에 할당됩니다. 이것은 3단계에서 변수가 생성이 됩니다.
9. 10행. 전역 실행 컨텍스트에서 새로운 변수 sum이 정의 합니다. 일반적으로 undefined을 할당합니다.
10. 다음에 함수를 실행해야합니다. 어떤 함수? addr라는 변수에 정의된 함수. 전역 실행 컨텍스트에서 충분히 찾을 수 있습니다. 두 가지 파라미터를 취하는 함수입니다.
11. 두 개의 파라미터를 검색하여 함수를 호출하고, 올바른 인자를 전달 할 수 있습니다. 첫번째는 1단계에서 정의한 변수  val이며 숫자는 7이고 두번째 숫자 8입니다.
12. 이제 이 함수를 실행해야합니다. 함수 정의는 3-5행에서 요약이 되어 있습니다. 새로운 로컬 실행 컨텍스트가 생성됩니다. 로컬 컨텍스트 내에서 두 개의 새로운 변수인 a와 b가 만들어 집니다. 이전 단계에서 함수에 전달 인수 이므로 7및 8값이 할당 됩니다.
13. 4행. 지역 실행 컨텍스트에 새로운 변수 ret을 선언하였습니다. 
14. 4행. 변수 a의 내용과 변수 b의 내용을 더해지고, 실행이 됩니다. ret 변수에 더해진 결과인 15가 할당이 되어 집니다.
15. ret변수는 함수로 부터 반환이 되어 집니다. 로컬 실행 컨텍스트는 소멸이 되고, 이것은 콜스택, 변수 a, 변수 b, ret은 더 이상 존재하지 않습니다.
16. 반환값은 9단계에서 정의한 sum변수에 할당됩니다.
17. 콘솔에서 sum의 값이 출력이 됩니다.

예상 대로 콘솔에 15가 출력이 될 것입니다. 실제로 많은 일이 벌어집니다. 여기서 몇 가지 요점을 설명하려고 합니다. 첫번째, 함수 정의는 변수에 저장될 수 있으며, 함수 정의는 호출될 때 까지 프로그램에 보이지 않습니다. 둘째, 함수가 호출되 때마다 로컬 실행 컨텍스트가 (일시적으로) 생성됩니다. 해당 실행 컨텍스트는 기능이 완료되면 사라집니다. 함수는 return또는 닫는 괄호 }가 있을 때 수행됩니다.

## 마지막, 클로저

다음 코드를 살펴보고 어떤 일이 일어날 지 알아보십시오.

```javascript
 1: function createCounter() {
 2:   let counter = 0
 3:   const myFunction = function() {
 4:     counter = counter + 1
 5:     return counter
 6:   }
 7:   return myFunction
 8: }
 9: const increment = createCounter()
10: const c1 = increment()
11: const c2 = increment()
12: const c3 = increment()
13: console.log('example increment', c1, c2, c3)
```

이전 두 예제에서다루었으므로 이제 실행된 결과를 예측할 수 있습니다.

1. 1행–8행. 새로운 변수 createCounter을 전역 실행 컨텍스트에 생성하고, 정의한 함수를 할당합니다.
2. 9행. 전역 실행 컨텍스트에 increment이름을 가진 새로운 변수를 선언합니다.
3. 다시 9행. creteCounter함수를 호출할 필요가 있고, increment변수의 값을 할당합니다.
4. 1행–8행. 호출 함수. 새로운 지역 실행 컨텍스트를 생성합니다.
5. 2행. 지역 실행 컨텍스트 안에서, counter 이름을 가진 새로운 변수를 선언합니다. counter에 숫자 9가 할당이 되어 집니다.
6. 3행–6행. myFunction이름ㅇ르 가진 새로운 변수를 선언합니다. 변수는 지역 실행 컨텍스트에 선언이 되었습니다. 변수의 본문은 아직 다른 함수를 정의 합니다. 4행과 5행에서 정의를 합니다.
7. 7행. myFunction 변수의 본문은 반환이 되었습니다. 지역 실행 컨텍스트는 삭제되어 집니다. myFunction 그리고 counter는 더 이상 존재하지 않습니다. 호출 컨텍스트에서 반환 되는 것을 제어합니다.
8. 9행. 호출 컨텍스트, 전역 실행 컨텍스트, 값은 createCounter에 의해 반환이 되고  increment가 할당이 되어 집니다. 변수는 증가하고 함수 정의를 포함하고 있습니다. createCounter에 의해 반환이 되어진 함수를 정의합니다. 레이블된 myFunction이 존재 하지 않습니다. 그러나 같은 함수 정의를 합니다. 글로벌 컨텍스트에서, 그것은 increment로 레이블되어 있습니다.
9. 10행. 새로운 변수를 선언합니다.(`c1`)
10. 10행(아직 진행). increment 변수를 찾으니 함수 입니다. 4-5행에 정의 된대로 이전에 반환된 함수 정의가 포함됩니다.
11. 새로운 실행 컨텍스트를 생성합니다. 파라미터들이 없습니다. 함수를 실행하기 시작합니다.
12. 4행. `counter = counter + 1`. 지역 실행 컨텍스트에서 counter변수를 찾습니다. 이 컨텍스트를 만들었고 지역 변수를 선언하지 않았습니다. 전역 실행 컨텍스트를 찾습니다. counter로 레이블된 변수는 이곳에 없습니다. 자바스크립트는 counter = undefined +1을 계산할 것이고, counter로 레이블된 새로운 변수를 선언하고 숫자 1을 할당하고 undefined은 0과 같은 숫자를 할당합니다.
13. 5행. counter의 본문을 리턴하거나 숫자 1을 리턴합니다. 지역 실행 컨텍스트, counter 변수가 소멸 되었습니다.
14. 10행으로 리턴이 됩니다. 리턴된 값(1)은 c1에 할당한 것을 얻습니다.
15. 11행. 10-14 단계를 반복합니다. `c2`는 또한 1을 얻습니다.
16. 12행. 10-14 단계를 반복합니다. c3는 또한 1을 얻습니다.
17. 13행.  c1, c2, c3의 변수의 본문이 로그로 출력 됩니다.

Try this out for yourself and see what happens. You’ll notice that it is not logging `1`, `1`, and `1` as you may expect from my explanation above. Instead it is logging `1`, `2` and `3`. So what gives?

Somehow, the increment function remembers that `counter `value. How is that working?

Is `counter `part of the global execution context? Try `console.log(counter)`and you’ll get `undefined`. So that’s not it.

Maybe, when you call `increment`, somehow it goes back to the the function where it was created (`createCounter`)? How would that even work? The variable `increment` contains the function definition, not where it came from. So that’s not it.

So there must be another mechanism. **The Closure.** We finally got to it, the missing piece.

Here is how it works. Whenever you declare a new function and assign it to a variable, you store the function definition, *as well as a closure*. The closure contains all the variables that are in scope at the time of creation of the function. It is analogous to a backpack. A function definition comes with a little backpack. And in its pack it stores all the variables that were in scope at the time that the function definition was created.

So our explanation above was *all wrong*, let’s try it again, but correctly this time.

```javascript
1: function createCounter() {
 2:   let counter = 0
 3:   const myFunction = function() {
 4:     counter = counter + 1
 5:     return counter
 6:   }
 7:   return myFunction
 8: }
 9: const increment = createCounter()
10: const c1 = increment()
11: const c2 = increment()
12: const c3 = increment()
13: console.log('example increment', c1, c2, c3)
```

1. Lines 1–8. We create a new variable `createCounter` in the global execution context and it get’s assigned function definition. Same as above.
2. Line 9. We declare a new variable named `increment` in the global execution context. Same as above.
3. Line 9 again. We need call the `createCounter` function and assign its returned value to the `increment` variable. Same as above.
4. Lines 1–8 . Calling the function. Creating new local execution context. Same as above.
5. Line 2. Within the local execution context, declare a new variable named `counter`. Number `0` is assigned to `counter`. Same as above.
6. Line 3–6. Declaring new variable named `myFunction`. The variable is declared in the local execution context. The content of the variable is yet another function definition. As defined in lines 4 and 5. Now we also create a *closure* and include it as part of the function definition. The closure contains the variables that are in scope, in this case the variable `counter` (with the value of `0`).
7. Line 7. Returning the content of the `myFunction` variable. Local execution context is deleted. `myFunction` and `counter` no longer exist. Control is returned to the calling context. So we are returning the function definition *and its closure*, the backpack with the variables that were in scope when it was created.
8. Line 9. In the calling context, the global execution context, the value returned by `createCounter` is assigned to `increment`. The variable increment now contains a function definition (and closure). The function definition that was returned by `createCounter`. It is no longer labeled `myFunction`, but it is the same definition. Within the global context, it is called `increment`.
9. Line 10. Declare a new variable (`c1`).
10. Line 10 (continued). Look up the variable `increment`, it’s a function, call it. It contains the function definition returned from earlier, as defined in lines 4–5. (and it also has a backpack with variables)
11. Create a new execution context. There are no parameters. Start execution the function.
12. Line 4. `counter = counter + 1`. We need to look for the variable `counter`. Before we look in the *local* or *global* execution context, let’s look in our backpack. Let’s check the closure. Lo and behold, the closure contains a variable named `counter`, its value is `0`. After the expression on line 4, its value is set to `1`. And it is stored in the backpack again. The closure now contains the variable `counter` with a value of `1`.
13. Line 5. We return the content of `counter`, or the number `1`. We destroy the local execution context.
14. Back to line 10. The returned value (`1`) gets assigned to `c1`.
15. Line 11. We repeat steps 10–14. This time, when we look at our closure, we see that the `counter` variable has a value of 1. It was set in step 12 or line 4 of the program. Its value gets incremented and stored as `2` in the closure of the increment function. And `c2` gets assigned `2`.
16. Line 12. We repeat steps 10–14, `c3` gets assigned `3`.
17. Line 13. We log the content of variables `c1`, `c2` and `c3`.

So now we understand how this works. The key to remember is that when a function gets declared, it contains a function definition and a closure. The closure is a collection of all the variables in scope at the time of creation of the function.

You may ask, does any function has a closure, even functions created in the global scope? The answer is yes. Functions created in the global scope create a closure. But since these functions were created in the global scope, they have access to all the variables in the global scope. And the closure concept is not really relevant.

When a function returns a function, that is when the concept of closures becomes more relevant. The returned function has access to variables that are not in the global scope, but they solely exist in its closure.

## Not so trivial closures

Sometimes closures show up when you don’t even notice it. You may have seen an example of what we call partial application. Like in the following code.

```javascript
let c = 4
const addX = x => n => n + x
const addThree = addX(3)
let d = addThree(c)
console.log('example partial application', d)
```

In case the arrow function throws you off, here is the equivalent.

```javascript
let c = 4
function addX(x) {
  return function(n) {
     return n + x
  }
}
const addThree = addX(3)
let d = addThree(c)
console.log('example partial application', d)
```

We declare a generic adder function `addX` that takes one parameter (`x`) and returns another function.

The returned function also takes one parameter and adds it to the variable `x`.

The variable `x` is part of the closure. When the variable `addThree` gets declared in the local context, it is assigned a function definition and a closure. The closure contains the variable `x`.

So now when `addThree` is called and executed, it has access to the variable `x` from its closure and the variable `n` which was passed as an argument and is able to return the sum.

In this example the console will print the number `7`.

![img](https://miro.medium.com/max/60/1*ZrJKJqBsksWd-8uKM9OvgA.png?q=20)

![img](https://miro.medium.com/max/1400/1*ZrJKJqBsksWd-8uKM9OvgA.png)

## 결론

The way I will always remember closures is through **the backpack analogy**. When a function gets created and passed around or returned from another function, it carries a backpack with it. And in the backpack are all the variables that were in scope when the function was declared.

> *If you enjoyed reading this, don’t forget the applause. 👏* 
> Thank you.

[
](https://medium.com/dailyjs?source=post_sidebar--------------------------post_sidebar-)