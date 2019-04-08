자바스크립트는 객체 형태를 지향하는 언어이다. 이 뜻은 자바스크립트의 대부분이 객체로 이루어져있다는 것을 의미한다. 

예를들어 function은 **object**이다. 객체가 아닌 유일한 요소는 기본 데이터 유형이다 (string, number, boolean, null, undefined).

기본데이터 유형은 불변성을 가지고 있어 생성되면 수정할 수 없다.


> 둘 사이의 차이점 중 하나는 원시 데이터 유형은 값으로 전달되고 개체는 참조로 전달된다는 것이다.

<br>

### 원시 데이터 유형은 값으로 전달되고 객체는 참조로 전달된다.

이게 무슨 의미인지는 다음과 같이 생각할 수 있다.

**Value는 원본을 `복사` 한 것을 생성하는 것을 의미한다.**

두명의 쌍둥이가 있다고 가정해보자: 그들은 정확히 똑같이 태어났지만, 첫 째가 전쟁에서 다리를 잃었다고 둘째 까지 다리를 잃지는 않는다.

**원본에 대한 `가명(별명)`이 참조에 의해 생성**

예를 들어 엄마가 나를 부를때 'Pumpkin Pie'라고 부를 때, 내 이름은 Margaret이지만, 갑자기 'Pumpkin Pie'라는 복제품이 생기지는 않는다. 즉, 비슷한 의미로 나는 한 사람이지만 여러개의 이름으로 불릴 수 있다.

> 개체의 동작 방식을 먼저 할당 연산자(=)와 함께 값을 할당하고, 두 번째 매개변수로서 함수에 전달했을 때 검토해보자.

<br>

## 1. 원시 객체에 `=` 연산자를 사용하여 값 할당

**초기 데이터 유형의 경우 =로 할당되는 방법**

```js
var name = "Carlos";
var firstName = name;
name = "Carla";
console.log(name); // "Carla"
console.log(firstName); // "Carlos"
```
이 결과는 너무 간단하게도 일치하는 연산자이다. 여기서 실제로 일어나는 일은 다음과 같이 단순화할 수 있다.

- 변수 `name`이 생성 되었을 때 'Carlos'라는 값을 할당 받았다. JavaScript는 이를 위해 메모리 스팟을 할당한다.
- 변수 `firstName`이 생성이 되었을 때 `name`의 값을 _복사_하게 된다. `firstName`은 자체적으로 메모리 스팟을 갖고 있고 `name`과는 독립되어있다. 이 시점에서 `firstName`은 'Carlos'라는 값을 갖게 된다.
- 그리고 `name`의 값이 'Carla'로 바뀌고, `firstName`은 여전히 원래의 값을 가지고 있는데 그 이유는 다른 메모리 스팟을 가지고 있기 때문이다.

원형으로 작업할 때 = operator는 원래 변수의 사본을 생성한다. 그게 가치관이야.

**기본적으로 할당된 값을 갖고 작업할 때는 변수를 복사하여 생성하는 것이 좋다.**

<br>

## 객체에 대해 =가 할당되는 방법

```js
var myName = {
  firstName: "Carlos"
};
var identity = myName;
myName.firstName = "Carla";
console.log(myName.firstName); // "Carla"
console.log(identity.firstName); // "Carla"
```

객체를 감싸고 있는 변수에 대한 output은 동일하게 작동한다. 값 할당에 대한 부분은 =가 동일하게 동작하기 때문이다

- 변수 `myName`은 생성될 때 객체로 생성이 되고 `firstName` 프로퍼티를 생성한다. 그 후 `firstName`은 'Carlos'라는 값을 갖게된다. 자바스크립트는 객체를 포함하고 있는 `myName`를 메모리스팟에 할당하게 된다.
- 변수 `identity`는 생성이 되고 `myName`을 가르키게 된다. 여기서 이 변수는 `identity`에 대한 메모리 공간이 없게 되고 오롯이 `myName`의 값만 바라보게 된다
- `myName`의 프로퍼티인 `firstName`의 값이 'Carla'로 변경이 되면서 `identity`의 값 또한 변경이 된다.

`myName.firstName`를 기록할 때 새 값이 표기가 되고 이 부분은 매우 간단하다. 그러나 `identity.firstName`을 기록할 때 `myName.firstName`까지 새로운 'Carla'값잉 할당이 된다. 이 것은 `identity.firstName`이 오로지 `myName.firstName`의 메모리 장소를 따라가기 때문이다.

> Object로 작업할 때 =로 할당되는 것은 원래의 객체에 대해 비슷한 객체를 생성하는 것이고 새로운 객채를 생성하지 않는다. "by reference"라는 말이 바로 그 뜻이다.

## 2. 함수에 기본값 및 개체 전달

**기본 데이터 유형이 값에 따라 함수에 전달됨**

함수 내에서 초기 데이터 유형의 값을 변경하면 외부 범위의 변수에 영향을 주지 않음:

```js
var myName = "Carlos";
function myNameIs(aName){
  aName = "Carla";
}
myNameIs(myName);
console.log(myName); // "Carlos"
```

function이 호출된 후 `myNameIs`내부에 있는 변수 `myName`를  바꾸려고 할 때, 이 것은 여전히 'Carlos'라는 값을 가지고 있다.
it still has the value "Carlos".
Even if we are changing the myName variable inside of the function myNameIs, when we print it after calling the function, it still has the value "Carlos". That is because when primitive types are passed, they are passed by value.

We are passing a copy of myName: anything you do to myName inside the body of the function won't affect the myNamevariable in the global scope because you are passing a copy of myName, and not the original myName variable.

**Objects are passed to a function by reference**

When you are passing something by reference, you are passing something that points to something else, not a copy of the object. So since JavaScript passes objects by reference, when you change a property of that object within the function, the change will be reflected in the outer scope:

```js
var myName = {};
function myNameIs(aName){
  aName.firstName = "Carla";
}
myNameIs(myName);
console.log(myName); // Object {firstName: "Carla"}
```

Now if I log the myName variable after having invoked the function myNameIs, it logs an object with a key of firstName with a value equal to "Carla". The object did change in the global scope when we passed it to the function.

This is because when you pass an object into the function, you are not passing a copy. You are passing something that points to the myName object. So when you change a property of that object in the function, you are changing the property of the object in the outer scope.

But there is a subtlety you should be aware of:

```js
var myName = {
  firstName: "Carla"
};
function myNameIs(aName){
  aName = {
    nickName: "Carlita"
  };
}
myNameIs(myName);
console.log(myName); // Object {firstName: "Carla"}
```

Here it prints the value of the variable myName in the outer scope and didn't add a nickName property to the object this time. Why is that ? If you look carefully, what we are doing in the function is trying to reassign the myName object a new value.

But you can’t change what myName points to, you can only change a property of myName to something else, like this:

```js
var myName = {
  firstName: "Carla"
};
function myNameIs(aName){
  aName.nickName = "Carlita";
}
myNameIs(myName);
console.log(myName); // Object {firstName: "Carla", nickName: "Carlita"}
```