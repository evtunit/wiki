# 자바스크립트의 기본 타입 & 참조 타입

다른 언어들과는 다른 자바스크립트의 pass-by-value 에 대한 설명입니다.

## Facts

- 자바스크립트는 원시와 참조 2 종류의 타입을 가지고 있다.
- 변수가 복사 되었을 때, 메모리 안의 값이 복사된다.
- 모든 변수가 생성된 후에 고정된 양의 메모리가 할당됩니다.
- 변수가 복사 될 때 ,메모리 내의 값이 복사됩니다.

## Primitive Types
메모리안의 원시 타입은 실제 값(e.g. boolean true, number 42) 입니다. 
원시 타입은 고정된 양의 메모리에 저장 될 수 있습니다.

- null
- undefined
- Boolean
- Number
- String

원시 타입은 스칼라 또는 단순한 타입이라고도 한다.

## Reference Types
참조 유형은 다른 값들을 포함 할 수 있습니다.
참조 유형의 내용은 변수에 이용될 수 있는 고정된 메모리 양에 맞지 않을 수 있습니다.
참조 유형의 메모리안의 값은 참조 유형 자체의 값 입니다.

- Array
- Object
- Function

참조 유형은 복합, 컨테이너 유형이라고도 합니다.

## Code Examples

#### Copying a primitive:

```js
var a = 13         // assign `13` to `a`
var b = a          // copy the value of `a` to `b`
b = 37             // assign `37` to `b`
console.log(a)     // => 13
```

원본 값은 바뀌지 않습니다. 우리는 오직 변경된 값만 확인할 수 있습니다.

#### Copying a reference:

```js
var a = { c: 13 }  // assign the reference of a new object to `a`
var b = a          // copy the reference of the object inside `a` to new variable `b`
b.c = 37           // modify the contents of the object `b` refers to
console.log(a)     // => { c: 37 }
```

참조가 복사된 후 원본도 변경됩니다.