# 함수 선언식과 함수 표현식
원문 : https://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/

짧은 퀴즈를 시작하겠습니다. 각각의 케이스에서 alert에는 무엇이 있을까요?

Question 1:
```javascript
function foo(){
    function bar() {
        return 3;
    }
    return bar();
    function bar() {
        return 8;
    }
}
alert(foo());
```

```javascript
function foo(){
    var bar = function() {
        return 3;
    };
    return bar();
    var bar = function() {
        return 8;
    };
}
alert(foo());
```

```javascript
alert(foo());
function foo(){
    var bar = function() {
        return 3;
    };
    return bar();
    var bar = function() {
        return 8;
    };
}
```

```javascript
function foo(){
    return bar();
    var bar = function() {
        return 3;
    };
    var bar = function() {
        return 8;
    };
}
alert(foo());
```

만약 8,3,3을 대답할수 없거나 [Type Error: bar is not a function]이 나온다면 이 글을 읽어야 합니다. (실제론 어느 누구든 읽어도 됩니다.😉) 

## 함수 선언식이 무엇인가요?
함수 선언식은 변수 할당을 요구하지 않고 명명된 함수 변수를 정의 합니다. 함수 선언은 독립 실행형 구문으로 발생하며 비-함수 블록 내에 중첩될수 없습니다. 변수 선언의 형제라고 생각하면 도움이 됩니다. 변수 선언이 “var”로 시작하는 것처럼 함수 선언식은 “function”으로 시작해야 합니다.

```javascript
function bar() {
    return 3;
}
```
ECMA 5 (13.0)의 *javascript function Identifier ( FormalParameterListopt ) { FunctionBody }* 문법으로 정의 되어 있습니다.

함수 이름은 scope내에서 볼 수 있으며, scope는 부모 입니다. (그렇지 않으면 도달 할 수 없으므로 좋습니다.)

```javascript
function bar() {
    return 3;
}
 
bar() //3
bar  //function
```

## 함수 표현식이 무엇인가요?
함수 표현식은 더 많은 표현의 문법(일반적으로 변수의 할당)의 일부로 정의 합니다. 함수 표현식을 통해 정의된 함수의 이름은 익명으로 지정할수 있습니다. 함수 표현식은 “function” 으로 시작하면 안됩니다. (따라서 아래의 직접 호출의 예제에서 괄호)

```javascript
//anonymous function expression
var a = function() {
    return 3;
}
 
//named function expression
var a = function bar() {
    return 3;
}
 
//self invoking function expression
(function sayHello() {
    alert("hello!");
})();
```
ECMA 5 (13.0)의 *function Identifieropt ( FormalParameterListopt ) { FunctionBody }* 문법으로 정의 되어 있습니다.
“function”으로 시작하지 않는 표현식 문법이 포함되어진 요구사항으로 인해 불완전 하다고 느낄 수 있다.
(though this feels incomplete since it omits the requirement that the containing syntax be an expression and not start with “function”>의역)

함수 이름(존재 하는 경우)은 해당 scope 밖에서 볼 수 없습니다. (함수 선언식과 대조가 된다.)

## 그래서 함수문은 무엇인가요?
때로는 함수 선언을 위한 가명입니다. 그러나 kangax가 지적했듯이 mozilla에서 함수문은 함수 선언의 확장으로 함수 선언문을 함수문을 허용되는 부분 어디에서나 함수 선언 구문을 사용할 수 있습니다. (in mozilla a Function Statement is an extension of Function Declaration allowing the Function Declaration syntax to be used anywhere a statement is allowed.>어떻게 설명을 하는 것이 더 나을까..) 아직 표준이 아니므로 production development에 권장되지 않습니다.

## 퀴즈에 대해서 설명해줄까요?
좋습니다. 질문 1은 함수 선언을 사용합니다. 즉 hoisted됨을 의미합니다.

## 잠시 기다려주세요. Hoisting이 무엇인가요?
Ben Cherry의 뛰어난 기사를 인용합니다. “[Function declarations and function variables are always moved (‘hoisted’) to the top of their JavaScript scope by the JavaScript interpreter](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)” 

함수 선언이 상단으로 이동하게 되면 전체 함수 본문이 상단으로 이동하게 되므로 인터프리터가 질문1의 코드가 끝난 후에는 다음과 같이 실행됩니다. 

```javascript
//**Simulated processing sequence for Question 1**
function foo(){
    //define bar once
    function bar() {
        return 3;
    }
    //redefine it
    function bar() {
        return 8;
    }
    //return its invocation
    return bar(); //8
}
alert(foo());
```

## 하지만...하지만..우리는 항상 반환문이 도달 할 수 없다는 것을 배워왔습니다.
Javascript실행에는 Context(ECMA 5가 LexicalEnvironment, VariableEnvironment, ThisBinding으로 분리되는 것) 및 
Process (순서대로 호출될 명령문 세트)가 있습니다. 실행 scope가 입력되면 선언이 VariableEnvironment에 기여합니다. Context와 Process는 return과 같은 Statement와 구별되고, process의 규칙에 대한 적용을 받지 않습니다.

## 함수 표현식이 호이스트 되기도 합니까?
그것은 표현식에 따라 달라집니다. 퀴즈 2번째의 첫번째 표현식을 보겠습니다. 

```javascript
var bar = function() {
    return 3;
};
```
왼쪽의 var bar은 변수 선언 입니다. 변수 선언은 상단으로 이동이 되었지만 할당 표현식은 그렇지 않습니다. 따라서 bar가 상단으로 올라가면 인터프리터는 초기에 var bar = undefined을 설정 합니다. 함수 정의 자체는 호이스트 되지 않습니다. 
(ECMA 5 12.2 에서 초기화가 이뤄진 변수에는 변수가 만들어질 때가 아니라 VariableStatement가 실행이 될 때 AssignmentExpressionr값이 할당됩니다.)

따라서 퀴즈 2의 코드는 보다 직관적인 순서로 실행이 됩니다.

```javascript
//**Simulated processing sequence for Question 2**
function foo(){
    //a declaration for each function expression
    var bar = undefined;
    var bar = undefined;
    //first Function Expression is executed
    bar = function() {
        return 3;
    };
    // Function created by first Function Expression is invoked
    return bar();
    // second Function Expression unreachable
}
alert(foo()); //3
```

## 좋습니다. 그 말이 맞는 것 같습니다. 그건 그렇고 질문 3에 대해서 틀렸는데 Firebug에서 error가 발생 했습니다.
HTML 파일에 저장하고 Firefox에서 실행해보세요. 또는 IE8, Chrome 또는 Safari 콘솔에서 실행해보세요. 분명히 Firebug 콘솔은 “global” scope에서 실행될 때 함수 호이스팅을 연습하지 않습니다. (실제로 “global”이 아니지만 특별한 “Firebug”의 scope - “Firebug”의 콘솔에서는 “this == window”이 실행됩니다.)

퀴즈 3은 퀴즈 1과 유사한 논리를 기반으로 합니다. 이번에는 hoisted된 foo 함수 입니다.

## 이제 퀴즈 4가 쉽습니다. 여기에 함수가 없습니다.
거의. 호이스팅이 모두 없으면 TypeError는 “bar not defined”가 되고 “bar not a function”이 아닙니다. 함수 호이스팅가 없지만 가변 호이스팅이 있습니다. 따라서 bar는 앞에 선언되었지만 값은 정의되지 않습니다. 모든 것은 다른 순서로 실행합니다. (Everything else runs to order. 여기에서 order 해석은...?)

```javascript
//**Simulated processing sequence for Question 4**
function foo(){
    //a declaration for each function expression
    var bar = undefined;
    var bar = undefined;
    return bar(); //TypeError: "bar not defined"
    //neither Function Expression is reached
}
alert(foo());
```

## 어떤 것을 주의 해야합니까?
함수 선언식은 함수가 아닌 블록(ex:if) 내에서 공식적으로 금지됩니다. 그러나 모든 브라우저는 이를 허용하고 다른 방식으로 해석합니다. 
예를 들어 Firefox 3.6의 다음 코드 스니펫은 함수 선언을 함수 구문으로 해석하므로 x는 정의되지 않았으므로 오류가 발생합니다. 그러나 IE8, Chrome 5 및 Safari 5에서는 함수 x가 반환됩니다.(표준 함수 선언식에서 예상 한대로)

```javascript
function foo() {
    if(false) {
        function x() {};
    }
    return x;
}
alert(foo());
```

## 함수 선언을 사용하면 혼동을 일으킬 수 있지만 이점은 있습니까?
함수 선언이 용서가 된다고 주장할 수 있습니다. 선언되기 전에 함수를 사용하려고 하면 호이스팅이 명령을 수정한 후에 문제는 발생하지 않고 함수가 호출됩니다. 그러나 이런 종류의 용서는 단단한 코딩을 권장하지 않으며 장기적으로 아마 그것을 막기보다는 놀라움을 조장할 가능성이 큽니다. 결국 프로그래머는 이유에 따라 특정 순서로 명령문을 정렬합니다.

## 그리고 함수 표현식을 선호하는 다른 이유가 있습니까?

어떻게 아셨나요?

a) 함수 선언은 Java 스타일 메서드 선언을 모방하려는 의도였지만 Java 메서드는 매우 다른 존재라고 느낍니다. Javascript 함수에서는 값이 있는한 살아있는 객체 입니다. Java 메소드는 메타 데이터 저장 공간입니다. 다음의 스니펫은 모두 함수를 정의하지만 함수 표현식만이 우리가 객체를 생성하고 있음을 암시합니다.

```javascript
//Function Declaration
function add(a,b) {return a + b};
//Function Expression
var add = function(a,b) {return a + b};
```
b) 함수 표현식은 보다 다양합니다. 함수 선언식은 "명령문"으로만 존재할 수 있습니다. 현재 변수가 부모 변수인 객체 변수를 만드는 것뿐 입니다. 
반대로 함수 표현식(정의에 의한)은 더 큰 구조의 일부 입니다. 익명 함수를 만들거나 함수를 프로토 타입에 할당하거나 다른 객체의 속성으로 할당하려면 함수표현식이 필요합니다. 
curry 또는 compose와 같은 고차 애플리케이션을 사용하여 새로운 함수를 만들 때마다 함수 표현식을 사용합니다. 함수 표현식 함수형 프로그래밍은 분리할 수 없습니다. 

```javascript
//Function Expression
var sayHello = alert.curry("hello!");
```

## 함수 표현식에 단점이 있습니까?
일반적으로 함수 표현식에 의해 생성된 함수는 이름이 지정되어 있지 않습니다. 예를 들어 다음 함수는 익명이며, 오늘날은 이름 없는 함수에 대한 참조 일뿐입니다.

```javascript
var today = function() {return new Date()}
```

이게 정말 중요할까요? [Nick Fitzgerald](http://fitzgeraldnick.com/)가 익명 함수로 디버깅을 하는 것은 좌절감을 줄 수 있다고 지적한 것처럼 말입니다. 그는 명명된 함수 표현식을 해결 방법으로 사용할 것을 제안합니다.


```javascript
var today = function today() {return new Date()}
```

그러나 Asen Bozhilov가 지적한 대로(그리고 Kangax의 문서) NFE는 IE 9 하위 브라우저에서 올바르게 작동하지 않습니다.

## 결론은?
잘못 놓여진 함수 선언식은 오해의 소지가 있을 수 있으며 면수에 할당된 함수 표현식을 대신 사용할 수 있는 상황이 거의 없습니다. 그러나 함수 선언식을 사용해야 하는 경우 함수 선언식이 속한 범위를 맨 위에 놓으면 혼돈을 최소화 할수 있습니다. 나는 if 선언문에 함수 선언식을 두지 않을 것입니다. 

이 모든 것을 말하면 함수 선언을 사용하는 것이 타당한 상황에서 스스로를 찾을 수 있습니다. 괜찮아. 규칙에 엄격한 준수는 위험하며 때로는 삐뚤어진 코드가 됩니다. 훨씬 더 중요한 것은 스스로 자신의 정보에 입각한 결정을 내릴 수 있도록 개념을 이해한다는 것입니다. 이 글이 도움이 되길 바랍니다.