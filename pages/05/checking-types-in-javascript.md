# Javascript에서 타입 체크 하기
 
> 원문: [Checking Types in Javascript](http://tobyho.com/2011/01/28/checking-types-in-javascript) 


Javascript 변수가 배열인지 확인하는 올바른 방법이 무엇인지 궁금해 해본적이 있습니까?
 
구글 검색을 하면 훌륭하고 다양한 대답을 얻을 수 있을 것이다. 그러나, 불행하게도 정답은 없다. 이는 자바스크립트의 안타까운 점 중 하나인데 다양하고 많은 구현들이 있을 뿐만 아니라 어떻게  확인 하는지에 대한 다양한 의견들도 있다.
 
이 포스팅은 자바스크립트의 다양한 타입 체크 기법과 각각의 장단점, 그리고 그것들이 존재하는 이유에 대해 종합적으로 설명하고자 한다.
 
## typeof 연산자
 
첫째로 `typeof` 연산자에 대해 알아보자. 이 편리한 연사자는 자바스크립트트 값의 'type'을 제공한다.
 
```js
typeof 3 // "number"
typeof "abc" // "string"
typeof {} // "object"
typeof true // "boolean"
typeof undefined // "undefined"
typeof function(){} // "function"
```
 
위에 결과는 원하는 타입 값이 올바르게 반환되었다.
 
```js
typeof [] // "object"
```
 
array의 타입이 `object`라고 반환되었다. 뭔가 이상하지 않은가?
 
```js
typeof null // "object"
```
 
이제 정말 잘못된 것 같아보인다.
 
`typeof`는 날짜, RegExp, 사용자 정의 객체, DOM 요소 및 기타 거의 모든 것에 대해 `object`를 반환할 것이다. 그래서 typeof는 다른 종류의 원시 값을 구별하는 데 꽤 능하지만, 배열과 null을 포함하는 다른 종류의 객체들을 구별하는 데 있어서는 전혀 쓸모가 없다.
 
## instanceof 연산자
 
`instanceof` 연산자는 객체가 특정 타입의 인스턴스인지 여부를 알려준다.  여기서 타입이란 생성자를 의미한다.  예를 들어
 
```js
function Animal(){}
var a = new Animal()
a instanceof Animal // true
```
 
또한 객체의 생성자 특성을 사용하여 아래의 검사를 수행할 수 있다.
 
```js
a.constructor === Animal // true
```
 
그러나 생성자 점검은 두 가지 문제가 있다. 첫째,  `prototype 체인`을 확인하지 않는다.
 
```js
function Cat(){}
Cat.prototype = new Animal()
Cat.prototype.constructor = Cat
var felix = new Cat()
felix instanceof Cat // true
felix instanceof Animal // true
felix.constructor === Cat // true
felix.constructor === Animal // false
```
 
두 번째 문제는 질의 객체가 null이거나 undefined인 경우 심각한 문제를 발생한다.
 
```js
felix = null
felix instanceof Animal // true
felix.constructor === Animal // throws TypeError
```
 
`instanceof`는 모든 기본 타입에는 동작한다.
 
```js
[1, 2, 3] instanceof Array // true
/abc/ instanceof RegExp // true
({}) instanceof Object // true
(function(){}) instanceof Function // true
```
 
그럼 아래는 동작할까?
 
```js
3 instanceof Number // false
true instanceof Boolean // false
'abc' instanceof String // false
```
 
이게 어찌된 일일까? `instanceof`는 원시 값에는 동작하지 않는다. Javascript의 원시 타입은 `string`, `number`, `boolean`, `null`, `undefined`이다.  `null`과 `undefined`를 제외하고는 `instanceof`는 동작하지 않는다고 말해야한다.  왜냐하면 다른 값들은  어떤 객체의 인스턴스가 아니기 때문이다.
 
```js
null instanceof Boolean // false
undefined instanceof Array // false
```
 
생성자 속성(property)을 확인하면 원시 타입 `number`, `string` 그리고 `boolean`에 대하여 동작한다.
 
```js
(3).constructor === Number // true
true.constructor === Boolean // true
'abc'.constructor === String // true
```
 
위에 예제가 동작하는 이유는 원시 값의 속성을 참조할 때마다 자바스크립트가 다음과 같이 객체 래퍼로 자동으로 값을 감싸기 때문이다.
 
```js
var wrapper = new Number(3)
```
 
객체 래퍼로 감싸는 것은 내부적으로 일어난다.  위의 경우에는 `Number` 인스턴스를 래퍼로 사용했지만, 어느 원시 값을 사용하느냐에 따라 `Boolean`이나 `String`이 될 수도 있다. 이 시점에서 프로토타입 체인을 통해 Number 프로토타입의 속성을 얻을 수 있다. 예를 들어 명시적으로 래퍼로 감싸면 `instanceof` 연산자가 정상적으로 동작한다.
 
```js
new Number(3) instanceof Number // true
new Boolean(true) instanceof Boolean // true
new String('abc') instanceof String // true
```
 
하지만 이렇게 하는 것은 해당 원시값이 어떤 타입의 값인지 알고 있어야하기 떄문에 무의미할 것이다.
 
## instanceof의 cross-window 이슈
 
`instanceof`는 또다른 문제를 가지고 있다는 것이 밝혀졌다. 다른 window에서 오는 객체를 테스트하려고 하면 문제가 생긴다. 여기서 다른 window는 `<iframe>`, `<frame>` 또는 팝업 윈도우를 의미한다.
 
```js
var iframe = document.createElement('iframe')
document.body.appendChild(iframe)
var iWindow = iframe.contentWindow // get a reference to the window object of the iframe
iWindow.document.write('<script>var arr = [1, 2, 3]</script>') // create an array var in iframe's window
iWindow.arr // [1, 2, 3]
iWindow.arr instanceof Array // false
```
 
위의 예제에선 iframe 컨텍스트 안에 arr 변수를 생성하고 그 안에 배열 [1, 2, 3]을 할당했다. 그러나 arr 변수가 Array의 인스턴스 인지 확인하자 `false`반환했다. 이유가 뭘까?
 
```js
Array === iWindow.Array // false
```
 
iframe 배열은 우리의 배열과 같은 배열이 아니다. 이것은 모든 내장 객체에 해된다. 모두 두 가지 버전이 존재한다. 즉,  평행우주를 가지고 있다고 보면된다.
 
이것은 iframe 내에서 생성된 배열이 iframe내의 배열 생성자의 인스턴스일 뿐이라는 것을 의미한다.
 
```js
iWindow.arr instanceof iWindow.Array // true
```
 
`open()` 함수를 사용하여 생성된 window에도 동일한 현상이 발생한다. 자바스크립트 커뮤니티 내에서 `instanceof` 사용이 다소 제한되는것은 이 cross-window 문제 때문이다.
 
## 덕타이핑
 
`typeof`나 `instanceof`의 예제도 만족스럽지 않기 때문에, 많은 사람들이 덕타이핑에 의존한다. 덕타이핑은 행동을 점검하는 것을 의미한다.
 
> 덕타이핑: .만약 그것이 오리처럼 보이고 오리처럼 운다면, 그것은 내가 생각하는 한 오리다.
 
때문에 덕타이핑을 사용한 isArray는 다음과 같을 것이다.
 
```js
// source: http://forums.devshed.com/javascript-development-115/javascript-test-whether-a-variable-is-array-or-not-33051.html
function isArray(obj){
    return (typeof(obj.length)=="undefined") ?
        false:true;
}

 
또는 
 
// source: http://www.hunlock.com/blogs/Ten_Javascript_Tools_Everyone_Should_Have
function isArray(testObject) {
    return testObject &&
	!(testObject.propertyIsEnumerable('length')) &&
	typeof testObject === 'object' &&
	typeof testObject.length === 'number';
}
```
 
jQuery에서 객체가 윈도우인지 확인하는 함수는 아래와 같다.
 
```js
isWindow: function( obj ) {
    return obj && typeof obj === "object" && "setInterval" in obj;
}
```
 
isWindow 함수는 아래와 같이 true를 반환하도록 속일 수 있다.
 
```js
$.isWindow({setInterval: 'bah!'}) // true
```
 
덕타이핑의 문제점은 아래와 같다.
 
1.  부정확하다.
2. 테스트 대상의 property 집합은 임의적이므로, 다른 사람들이 덕타이핑을 수행하는 해당 방법에 동의할 가능성은 거의 없다.
 
그렇기때문에, 덕타이핑을 별로 좋아하지 않는다.
 
## Object.prototype.toString 메서드
 
Object.prototype.toString 메서드를 사용하면 객체의 타입에 대한 정보를 얻을 수 있다.
 
```js
Object.prototype.toString.call(3) // "[object Number]"
Object.prototype.toString.call([1, 2, 3]) // "[object Array]"
Object.prototype.toString.call({}) // "[object Object]"
```
 
이 메서드는 일반적으로 잘 사용되지 않는다. 프로토타입 체인에 있는 Array.prototype.toString, Number.prototype.toString 등의 또 다른 toString메서드가 존재하기 때문이다. 해당 메서드는 네이티브 타입을 확실히 구분하지만 사용자 정의 타입에선 모두 "[object Object]"를 반환한다.
 
```js
Object.prototype.toString.call(new Animal) // "[object Object]"
```
 
그러나 이건 다른 window 컨텍스트에서도 동작한다.
 
```js
Object.prototype.toString.call(iWindow.arr) === "[object Array]" // true
```
 
IE의 다른 window(popup 같은)는 예외로 한다.
 
```js
var pWindow = open("")
pWindow.document.write('<script>var arr = [1, 2, 3]</script>')
Object.prototype.toString.call(pWindow.arr) // IEd에선 "[object Object]" 반환한다. 다른 브라우저는 "[object Array]"를 반환
```
 
이것은 이상하다. 왜냐하면 iframe에서는 정상적으로 동작하기 때문이다. 이 방법은 IE 버그에도 불구하고 네이티브 타입을 구별하기 위해 선호되는 방법이 되었다. (어차피 아무도 팝업 창을 사용하지 않으니..)
 
###  Function.prototype.toString 메서드 
 
타입 정보를 확인하는 또다른 방법은 `Function.prototype.toString`메서드를 사용하는 것이다.
 
```js
Function.prototype.toString.call((3).constructor)
// "function Number() {
//    [native code]
// }"
```
 
이 메서드는 함수의 완전한 source를 제공한다. 네이티브 함수의 경우, 내부에 "[native code]"라고 되어있다.  아래의 도우미 함수를 사용하면 함수의 이름을 파싱하여 객체의 타입을 알아낼 수 있다.
 
```js
function type(obj){
	var text = Function.prototype.toString.call(obj.constructor)
	return text.match(/function (.*)\(/)[1]
}
type("abc") // "String"
```
 
이 도우미 함수(type)는 사용자 정의 객체에서도 사용할 수 있다.
 
```js
type(new Animal) // "Animal"
```
 
이 코드는 `instanceof`와 마찬가지로 IE의 팝업 윈도우에 문제가 있다.  왜냐하면 `Function.prototype.toString`을 다른 평행우주에서 온 생성자와 함께 부르면,생성자는 객체(["object Object]")로 인식하기 떄문에 매개변수를 거부하고 "Function expected" 에러를 내뱉는다. 이 문제는 toString 메서드를 간접적으로 참조함으로써 해결할 수 있다.
 
```js
function type(obj){
	var text = obj.constructor.toString()
	return text.match(/function (.*)\(/)[1]
```
 
이제,  위의 함수는 IE의 팝업 윈도우에도 적용된다! 하지만 이 수정은 다른 문제점을 야기하기도 한다.
 
```js
Array.toString = function(){ return "function NotArray(){ }" }
type([1,2,3]) // "NotArray"
```
 
그럼에도 꽤 괜찮아 보인다. 이제 사용자 정의 타입으로 잠시 돌아가보자. 이 방법을 사용하면 동일한 이름의 두 가지 다른 사용자 정의  타입을 구별할 수 없다.
 
```js
var f = function Animal(){ "something" }
 
var g = function Animal(){ "something entirely different" }
type(new f) // "Animal"
type(new g) // "Animal"
```
 
이러한 이유로 이 방법은 사용자 정의 타입에 한하여 `instanceof`보다 못하다. 이 접근방식의 또 다른 분명한 문제는 성능이다.
 
## DOM 요소 및 호스트 개체
 
지금까지 DOM 요소와 호스트 객체에 대한 타입 점검에 대해 언급하지 않았다. 어렵기 때문이다. 덕타이핑을 제외하고, 위에서 언급한 어떤 방법도 모든 브라우저에서 작동하지 않을 것이다. 그러나 IE7 이하를 제외하면 실제로 동작하게 할 수 있다. 아래의 출력은 [Tutti](http://tutti.tobyho.com/)를 사용하여 생성되었다.
 
```js
> var div = document.createElement('div')
> typeof div
Safari 5.0 => object
Firefox 3.6 => object
IE 7.0 => object
IE 8.0 => object
Opera 11.01 => object
> div instanceof Element
Safari 5.0 => true
Firefox 3.6 => true
IE 7.0 => Error: 'Element' is undefined
IE 8.0 => true
Opera 11.01 => true
> div instanceof HTMLDivElement
Safari 5.0 => true
Firefox 3.6 => true
IE 8.0 => true
IE 7.0 => Error: 'HTMLDivElement' is undefined
Opera 11.01 => true
```
 
첫째로, `typeof` 는 예상대로 쓸모 없어보인다. 다음으로, IE 7을 제외한 모든 브라우저는 div가 HTMLDivElement뿐만 아니라 Element의 인스턴스임을 인식한다. IE7에서는 생성자가 존재하지도 않는다.
 
```js
> Object.prototype.toString.call(div)
Safari 5.0 => [object HTMLDivElement]
Firefox 3.6 => [object HTMLDivElement]
IE 7.0 => [object Object]
IE 8.0 => [object Object]
Opera 11.01 => [object HTMLDivElement]
```
 
IE에서 `Object.prototype.toString` 결과는 유용하지 않다.
 
```js
> div.constructor.toString()
Safari 5.0 => [object HTMLDivElementConstructor]
Firefox 3.6 => [object HTMLDivElement]
IE 7.0 => Error: 'div.constructor' is null or not an object
IE 8.0 => [object HTMLDivElement]
Opera 11.01 => function HTMLDivElement() { [native code] }
```
`Function.prototype.toString`는 IE8에선 유용하지만, 브라우저마다 약간 다른 결과를 가진다.
 
```js
> typeof window
Safari 5.0 => object
Firefox 3.6 => object
IE 8.0 => object
IE 7.0 => object
Opera 11.01 => object
> window instanceof Window
Safari 5.0 => ReferenceError: Can't find variable: Window
Firefox 3.6 => true
IE 8.0 => true
IE 7.0 => Error: 'Window' is undefined
Opera 11.01 => ReferenceError: Undefined variable: Window
> Object.prototype.toString.call(window)
Safari 5.0 => [object DOMWindow]
Firefox 3.6 => [object Object]
IE 8.0 => [object Object]
IE 7.0 => [object Object]
Opera 11.01 => [object Window]
> window.constructor
Safari 5.0 => function Object() {
    [native code]
}
Firefox 3.6 => function Object() {
    [native code]
}
IE 8.0 => [object Window]
IE 7.0 => undefined
Opera 11.01 => function Object() { [native code] }
```
 
`window`을 사용하면 이 메서드 들중 어느것도 브라우저에서 작동하지 않았다. 원하는 경우 다른 호스트 객체를 테스트해 볼 수 있지만, 일반적으로 테스트는 불가능해 보인다. 그러나, 이 테스팅에서 XMLHttpRequest 객체 및 DOM 및 텍스트 요소는 IE7 이하에 대한 지원을 중단할 수 있는 경우 인스턴스를 사용하여 실행할 수 있는 것으로 보인다.
 
## 우리가 배운 것
 
자바스크립트에서 타입을 확인하는 것은 매우 엉망이다. 비록 어떤 특정한 유형을 확인하는 것이 그렇게 어렵지는 않지만, 그것은 확실히 여러 유형에 걸쳐 일관성이 없고, 아마도 그 과정에서 많은 선택을 해야만 했을 것이다. 그래서 그것은 모든 다른 선택사항들에 대해 아는 것에 대해 도움을 줄 것이다. 다가오는 포스팅에서 작은 코드 조각으로 이 모든 것을 쉽게 만들도록 노력할 것이다.
 
## 출처
 
-   [Remedial Javascript](http://javascript.crockford.com/remedial.html) by Crockford
-   [instanceof considered harmful](http://perfectionkills.com/instanceof-considered-harmful-or-how-to-write-a-robust-isarray/)    
-   [An SO question w some helpful info](http://stackoverflow.com/questions/332422/how-do-i-get-the-name-of-an-objects-type-in-javascript)  
-   Crockford [calling instanceof useless](http://groups.google.com/group/comp.lang.javascript/msg/1afbcb0da1cd4aef)