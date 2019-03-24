## 실행 컨텍스트가 무엇인가?

간단히 말해 실행 컨텍스트는 자바 스크립트 코드가 평가되고 실행되는 환경의 추상 개념입니다.   
JavaScript에서 코드를 실행할 때마다 실행 컨텍스트 내에서 실행됩니다. 

### 실행 컨텍스트의 유형

JavaScript에는 세 가지 유형의 실행 컨텍스트가 있습니다. 

* ### 전역 실행 컨텍스트
  기본 또는 기본 실행 컨텍스트입니다. 함수 안에 들어 있지 않은 코드는 전역 실행 컨텍스트에 있습니다. 그것은 두 가지를 수행합니다.  
  브라우저 객체의 경우 윈도우 객체 인 전역 객체를 생성하고 this 값을 전역 객체와 동일하게 설정합니다. 프로그램에는 전역 실행 컨텍스트가    
  하나만있을 수 있습니다.

* ### 기능적 실행 컨텍스트
  함수가 호출 될 때마다 새로운 실행 컨텍스트가 해당 함수에 대해 작성됩니다.  
  각 함수에는 고유 한 실행 컨텍스트가 있지만 함수가 호출되거나 호출 할 때 만들어집니다.  
  함수 실행 컨텍스트는 여러 개있을 수 있습니다. 새 실행 컨텍스트가 만들어 질 때마다 정의 된 순서로 일련의 단계를 거칩니다.  
  이 단계는 뒷부분에서 설명합니다. 

* ### Eval 함수 실행 컨텍스트
  eval 함수 내에서 실행되는 코드는 자체 실행 컨텍스트를 가져 오지만, 일반적으로 JavaScript 개발자는 eval을 사용하지 않으므로    
  여기서는 다루지 않습니다.  

### 실행 스택

다른 프로그래밍 언어에서 "호출 스택"이라고도 알려진 실행 스택은 코드 실행 중에 작성된 모든 실행 컨텍스트를 저장하는 데 사용되는  
LIFO (Last in, First out) 구조의 스택입니다.

JavaScript 엔진이 스크립트를 처음 발견하면 전역 실행 컨텍스트를 만들어 현재 실행 스택에 푸시합니다.  
엔진이 함수 호출을 찾을 때마다 해당 함수에 대한 새 실행 컨텍스트를 만들고 스택의 맨 위로 밀어 넣습니다.  

엔진은 실행 컨텍스트가 스택의 맨 위에있는 함수를 실행합니다.  
이 함수가 완료되면 스택에 있는 실행 스택을 꺼내고 컨트롤이 현재 스택의 컨텍스트에 도달합니다.  

아래 코드로 이해를 해보자

````
let a = 'Hello World!';
function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}
function second() {
  console.log('Inside second function');
}
first();
console.log('Inside Global Execution Context');
````
![](https://cdn-images-1.medium.com/max/2600/1*ACtBy8CIepVTOSYcVwZ34Q.png)
브라우저에서 위의 코드가로드되면 Javascript 엔진이 전역 실행 컨텍스트를 만들고 현재 실행 스택에 푸시합니다.  
first()에 대한 호출이 발생하면 Javascript 엔진은 해당 함수에 대한 새 실행 컨텍스트를 만들고 현재 실행 스택의 맨 위로 밀어 넣습니다.

second() 함수가 first() 함수 내에서 호출되면 Javascript 엔진은 해당 함수에 대한 새 실행 컨텍스트를 만들고  
현재 실행 스택의 맨 위로 밀어 넣습니다. second() 함수가 끝나면 실행 컨텍스트가 현재 스택에서 해제되고 컨트롤은 그 아래의 실행 컨텍스트,    
즉 first() 함수 실행 컨텍스트에 도달합니다.

first()가 끝나면 실행 스택이 스택에서 제거되고 컨트롤이 전역 실행 컨텍스트에 도달합니다.  
모든 코드가 실행되면 JavaScript 엔진은 현재 스택에서 전역 실행 컨텍스트를 제거합니다.

### 실행 컨텍스트는 어떻게 생성되는가?

지금까지는 JavaScript 엔진이 실행 컨텍스트를 관리하는 방법을 살펴 보았습니다.  
이제 JavaScript 엔진에서 실행 컨텍스트를 만드는 방법을 이해해 봅시다.

실행 컨텍스트는 1) 생성 단계, 2) 실행 단계의 두 단계로 생성됩니다.

### 생성 단계
실행 컨텍스트는 생성 단계에서 생성됩니다.  
다음 단계는 생성 단계에서 발생합니다.:

1. 어휘환경(Lexical Environment) 구성 요소가 생성됩니다.
2. 가변환경(Variable Environment) 구성 요소가 생성되었습니다.

따라서 실행 컨텍스트는 개념적으로 다음과 같이 나타낼 수 있습니다.
````
ExecutionContext = {
  LexicalEnvironment = <ref. to LexicalEnvironment in memory>,
  VariableEnvironment = <ref. to VariableEnvironment in  memory>,
}
````

### 어휘환경(Lexical Environment)
공식 ES6 문서는 어휘 환경을 다음과 같이 정의합니다.
````
어휘 환경은 ECMAScript코드의 어휘 중첩 구조를 기반으로 식별자와 특정 변수 및 함수와의 연관을 정의하는 데 사용되는 사양 유형입니다.  
어휘 환경은 환경기록과 null 가능성이 있습니다.
````

간단히 말해, 어휘환경은 식별자 변수 매핑을 유지하는 구조입니다.   
(여기서 식별자는 변수 / 함수의 이름을 가리키며 변수는 실제 객체 [함수 객체 및 배열 객체 포함] 또는 원시적인 값에 대한 참조입니다.

예를 들어, 다음 단편을 고려하십시요
````
var a = 20;
var b = 40;
function foo() {
  console.log('bar');
}
````

따라서 위의 단편에 대한 어휘 환경은 다음과 같습니다.
````
lexicalEnvironment = {
  a: 20,
  b: 40,
  foo: <ref. to foo function>
}
````


각 어휘 환경에는 세 가지 구성 요소가 있습니다.

1. 환경기록
2. 외부 환경에 대한 참조
3. 바인딩

### 환경기록
환경 기록은 변수 및 함수 선언이 어휘 환경 내에 저장되는 장소입니다.

환경 기록에는 두 가지 유형이 있습니다. : 

* 선언적 환경 기록
  이름에서 알 수 있듯이, 변수와 함수의 선언을 포함합니다.  
  기능 코드의 어휘 환경에 선언적 환경기록이 포함되어 있습니다.
* 객체 환경 기록
  글로벌 코드의 어휘 환경은 객관적인 환경기록이 포함되어 있습니다. 변수 및 함수의 선언과는 별도로 객체 환경 기록은  
  글로벌 바인딩 개체(브라우저 창 개체)도 포함되어 있습니다.  
  따라서 바인딩 개체의 각 속성(브라우저의 경우 브라우저 창 개체에 제공되는 속성과 메소드를 포함합니다)마다 새로운 항목이 기록에 만들어집니다.

````
**NOTE**  
함수코드의 경우, 환경 기록에는 함수에 전달 된 인덱스와 arguments들 사이의 매핑과 함수에 전달 된 arguments의 길이를 포함하는  
arguments 객체도 포함됩니다. 예를 들어, 아래 함수에 대한 arguments 객체는 다음과 같습니다.
````

````
function foo(a, b) {
  var c = a + b;
}
foo(2, 3);
// argument object
Arguments: {0: 2, 1: 3, length: 2},
````

### 외부 환경에 대한 참조
외부 환경에 대한 참조는 외부 환경에 액세스 할 수 있음을 의미합니다.  
즉, JavaScript 엔진은 현재 어휘 환경에서 찾을 수 없는 경우 바깥 환경의 변수를 찾을 수 있습니다.

### 바인딩
컴포넌트 안에서 값이 결정되거나 설정됩니다.

전역 실행 컨텍스트에서 this 값은 전역 개체를 참조합니다. (브라우저에서는 Window 객체를 참조)

함수 실행 컨텍스트에서 값은 함수가 호출되는 방식에 따라 다릅니다.  
객체 참조에 의해 호출되면 this 값은 해당 객체로 설정되고, 그렇지 않으면이 값은 전역 객체로 설정되거나 정의되지 않습니다(엄격 모드에서).  
  
예 :
````
const person = {
  name: 'peter',
  birthYear: 1994,
  calcAge: function() {
    console.log(2018 - this.birthYear);
  }
}
person.calcAge(); 
// 'this' refers to 'person', because 'calcAge' was called with   
// 'person' object reference
const calculateAge = person.calcAge;
calculateAge();
// 'this' refers to the global window object, because no object reference was given
````

추상적으로, 어휘 환경은 의사 코드에서 다음과 같이 보입니다.
````
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
    }
    outer: <null>,
    this: <global object>
  }
}

FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
    }
    outer: <Global or outer function environment reference>,
    this: <depends on how function is called>
  }
}
````

### 가변환경
환경 기록이 실행 컨텍스트 내에서 변수 문으로 만든 바인딩을 보유하는 어휘 환경입니다.

위에서 설명한 것처럼 변수 환경은 어휘 환경이기도 하므로 위에 정의 된 어휘 환경의 모든 특성과 구성 요소를 갖습니다.

ES6에서 어휘 환경 구성 요소와 가변 환경 구성 요소의 차이점 중 하나는 함수 선언과 변수(let 및 const) 바인딩을 저장하는 데 사용되는 반면,   
후자는 변수(var) 바인딩 만 저장하는 데 사용된다는 것입니다.

### 실행 단계
이 단계에서 모든 변수에 대한 할당이 완료되고 코드가 최종적으로 실행됩니다.

#### 예제
위의 개념을 이해하는 몇 가지 예를 살펴 보겠습니다.

````
let a = 20;
const b = 30;
var c;
function multiply(e, f) {
 var g = 20;
 return e * f * g;
}
c = multiply(20, 30);
````

위의 코드가 실행되면 JavaScript 엔진이 전역 실행 컨텍스트를 만들어 전역 코드를 실행합니다.  
따라서 전역 실행 컨텍스트는 생성 단계에서 다음과 같이 보입니다.

````
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      a: < uninitialized >,
      b: < uninitialized >,
      multiply: < func >
    }
    outer: <null>,
    ThisBinding: <Global Object>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      c: undefined,
    }
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
````

실행 단계에서 변수 할당이 수행됩니다. 따라서 전역 실행 컨텍스트는 실행 단계에서 이와 비슷하게 보입니다.

````
GlobalExectionContext = {
LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      a: 20,
      b: 30,
      multiply: < func >
    }
    outer: <null>,
    ThisBinding: <Global Object>
  },
VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      c: undefined,
    }
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
````

함수 multiply(20, 30)에 대한 호출이 발생하면 새 함수 실행 컨텍스트가 만들어져 함수 코드가 실행됩니다.  
따라서 함수 실행 컨텍스트는 생성 단계에서 다음과 같이 보입니다.

````
FunctionExectionContext = {
LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      Arguments: {0: 20, 1: 30, length: 2},
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      g: undefined
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
````

그런 다음 실행 컨텍스트는 실행 단계를 거쳐 함수 내의 변수에 대한 할당이 완료되었음을 의미합니다.  
따라서 함수 실행 컨텍스트는 실행 단계에서 다음과 같이 표시됩니다.

````
FunctionExectionContext = {
LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      Arguments: {0: 20, 1: 30, length: 2},
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      g: 20
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
````

함수가 완료된 후 반환 값은 c 안에 저장됩니다. 그래서 글로벌 어휘 환경이 업데이트됩니다.   
그 후, 전역 코드가 완료되고 프로그램이 완료됩니다.

````
**NOTE**
let 및 const 정의 변수는 생성 단계에서 연관된 값이 없지만 var 정의 변수는 undefined로 설정됩니다.
````

이는 생성 단계에서 함수 선언이 환경에 전체적으로 저장되는 동안 변수 및 함수 선언에 대해 코드가 검색되고 변수가 초기에  
undefined(var의 경우)로 설정되거나 초기화되지 않은 상태로 유지되기 때문입니다. (let과 const의 경우)

이것이 var 정의 변수가 선언되기 전에 (정의되지는 않았지만) var 정의 변수에 액세스 할 수 있지만  
let 및 const 변수가 선언되기 전에 액세스 할 때 참조 오류가 발생하는 이유입니다.

우리는 이것을 호이스팅이라고 부른다.

````
**NOTE**
실행 단계에서 JavaScript 엔진이 let 변수의 값을 소스 코드에서 선언 된 실제 위치에서 찾지 못하면 정의되지 않은 값을 할당합니다.

````

## 결론
그래서 우리는 JavaScript 프로그램이 내부적으로 어떻게 실행되는지를 논의했습니다.  
이러한 모든 개념을 멋진 JavaScript 개발자로 배울 필요는 없지만 위의 개념을 제대로 이해하면 호이스팅,  
범위 및 클로저와 같은 다른 개념을보다 쉽고 깊이 이해할 수 있습니다.

출처 : https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0