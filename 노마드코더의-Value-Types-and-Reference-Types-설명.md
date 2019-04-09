## 노마드코더의 Value Types and Reference Types 설명
```javascript
console.log([10]===[10])
console.log({'a':20}==={'a':20})
```
이 둘이 왜 false 값을 리턴하는지 설명.
1. value
```javascript
let a = 50;
let b = a;
console.log(a); // 50
console.log(b); // 50
```
```javascript
let a = 50;
let b = a;
a = 10;
console.log(a); // 10
console.log(b); // 50
```
복사 . 붙여넣기. 
참조를 하지 않음.

1. reference
```javascript
const sexy = ["kimchi","potato"];
const pretty = sexy;
console.log(pretty); // ['kimchi','potato']
sexy.push("hELLO");
console.log(pretty); // ['kimchi','potato','hELLO']
```
pretty를 변경하지 않고 sexy를 변경 하였는데 pretty도 변경이 되었음.
값을 복사하는 것이 아니라 참조하는 것.
sexy는 해당 배열을 참조하고 있는 것.
배열은 둥둥 떠있고, sexy는 해당 배열을 가리키고 있음.
a=50이다라는 것과 다르다. 참조 하는 것이 아니고 해당 값이라고 말하는 것임.
sexy는 해당 배열을 참조하는 것. 해당 배열은 어느 메모리 어딘가에 위치해있음.
pretty는 sexy를 참조하고 있음.
배열을 업데이트하면 pretty, sexy가 동시에 업데이트된 배열을 출력함.
=> 동일한 배열을 참조하고 있음.

```javascript
const sexy = ["kimchi","potato"];
const pretty = sexy;
console.log(pretty); // ['kimchi','potato']
sexy.push("hELLO");
console.log(pretty); // ['kimchi','potato','hELLO']
sexy.push("lalalalalalalal");
console.log(sexy); // ['kimchi','potato','hELLO','lalalalalalalal']
```
sexy가 'lalalalalalalal'를 가지게 됨.
해당 배열을 sexy 또는 pretty에서 업데이트를 함.

둥둥 떠다니는 배열을 sexy, pretty가 각각 참조하는 것임.
```javascript
console.log([10]===[10])
console.log({'a':20}==={'a':20})
```
그래서 반환값이 false인 것이다.
이 둘은 각각의 메모리에 위치한 각기 다른 object이다.

```javascript
const x = {
    a: 'hello'
}
const b = x;
b.a = 'lalaal'

console.log(x)
```
이런 상황에서 x를 b를 통해 업데이트 할 수 있음.

value는 string, number, boolean, NaN, undefined, null가능
reference는 array, object, function에서 사용될 수 있음. 