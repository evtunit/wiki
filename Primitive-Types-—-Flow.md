Javascript 에는 여러가지 원시 타입들이 있습니다 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures). 

- Booleans
- Strings
- Numbers
- null
- undefined (Flow 타입에 있는 void)
- Symbols (new in ECMAScript 2015, Flow 에서는 지원하지 않습니다)

원시 타입의 경우 리터럴 값으로 나타납니다.

```js
true;
"hello";
3.14;
null;
undefined;
```

또는 Wrapper Object 로 나타냅니다

```js
new Boolean(false);
new String("world");
new Number(42);
Symbol("foo");
```

리터럴 값의 타입은 소문자입니다.

```js
// @flow
function method(x: number, y: string, z: boolean) {
  // ...
}

method(3.14, "hello", true);
```

wrapper object 들은 대문자로 나타냅니다 (생성자와 동일합니다)

```js
// @flow
function method(x: Number, y: String, z: Boolean) {
  // ...
}

method(new Number(42), new String("world"), new Boolean(false));
```

wrapper object 들은 거의 사용되지 않습니다.

## Booleans 

JS 에서 Booleans 은 `true` 와 `false` 값 입니다. Flow 에서 boolean 타입은 다음과 같은 값을 허용합니다

```js
// @flow
function acceptsBoolean(value: boolean) {
  // ...
}

acceptsBoolean(true);  // Works!
acceptsBoolean(false); // Works!
acceptsBoolean("foo"); // Error!
```

Javascript 에서는 암시적 형변환으로 booleans 값으로 변환할 수 있습니다.

```js
if (42) {} // 42 => true
if ("") {} // "" => false
```

Flow 는 이러한 변환을 이해하고 if 문과 다른 타입의 표현들을 허락합니다. 

non-booleans 의 경우 booleans 타입으로 변환이 필요합니다. Boolean (x) 또는 !!x 같이 변환할 수 있습니다.

```js
// @flow
function acceptsBoolean(value: boolean) {
  // ...
}

acceptsBoolean(0);          // Error!
acceptsBoolean(Boolean(0)); // Works!
acceptsBoolean(!!0);        // Works!
```

`boolean` 과 `Boolean` 은 다른 타입임을 기억하세요 

- `boolean` 은 리터럴 값 또는 a === b 같은 표현식의 결과 값 입니다.
- `Boolean` 은 전역 new Boolean 생성자로 만들어진 wrapper object 입니다.

## Numbers 

다른 언어와 달리 Javascript 에는 오직 한 가지의 숫자 타입만 있습니다.
Numbers 값은 42 또는 3.14 와 같이 표현할 수 있습니다. Javascript 에서는 Infinity 와 NaN 또한 숫자로 판단합니다.
Number 타입은 Javascript 에서 숫자로 판단하는 모든 것을 Number 타입으로 간주합니다.

```js
// @flow
function acceptsNumber(value: number) {
  // ...
}

acceptsNumber(42);       // Works!
acceptsNumber(3.14);     // Works!
acceptsNumber(NaN);      // Works!
acceptsNumber(Infinity); // Works!
acceptsNumber("foo");    // Error!
```

`number` 와 `Number` 이 다르다는 것을 기억하세요 

- `number` 은 3.14 또는 42 또는 parseFloat(x) 의 표현 결과 값과 같은 리터럴 값 입니다. 
- `Number` 의 경우 전역 new Number(x) 생성자로부터 만들어진 wrapper object 입니다.

## Strings 
Strings 은 `"foo"` 와 같은 Javascript 의 값 입니다. `string` 타입은 아래와 같이 표현됩니다.

```js
// @flow
function acceptsString(value: string) {
  // ...
}

acceptsString("foo"); // Works!
acceptsString(false); // Error!
```

Javascript 는 값을 String 타입으로 암묵적 변화를 시킵니다.

```js
"foo" + 42; // "foo42"
"foo" + {}; // "foo[object Object]"
```

Flow 는 숫자와 문자의 연결만 허용합니다.

```js
// @flow
"foo" + "foo"; // Works!
"foo" + 42;    // Works!
"foo" + {};    // Error!
"foo" + [];    // Error!
```

다른 유형을 string 타입으로 변화시킬 때는 명시적이어야 합니다.
String 메서드를 이용하거나 문자열로 변환하는 메서드를 사용하여 변환할 수 있습니다.

```js
// @flow
"foo" + String({});     // Works!
"foo" + [].toString();  // Works!
"" + JSON.stringify({}) // Works!
```

`string`과 `String` 이 다르다는 것을 기억하세요 

- `string` 의 경우 "foo" 같은 리터럴 값 입니다. 
- `String` 의 경우 전역의 new String(x) 생성자로 만들어진 wrapper object 입니다.

## null and void 

Javascript 에는 null 과 undeinfed 가 있습니다.
Flow 는 두 가지를 별개 type 으로 판단합니다.

```js
// @flow
function acceptsNull(value: null) {
  /* ... */
}

function acceptsUndefined(value: void) {
  /* ... */
}

acceptsNull(null);      // Works!
acceptsNull(undefined); // Error!
acceptsUndefined(null);      // Error!
acceptsUndefined(undefined); // Works!
```

`null` 과 `void` 는 다른 type 으로 나타납니다.

## Maybe types 

선택적 값은 ?string, ?number 와 같이 물음표를 추가하여 표현할 수 있습니다.
`?type` 은 null 또는 void 가 될 수 있습니다.

```js
// @flow
function acceptsMaybeString(value: ?string) {
  // ...
}

acceptsMaybeString("bar");     // Works!
acceptsMaybeString(undefined); // Works!
acceptsMaybeString(null);      // Works!
acceptsMaybeString();          // Works!
```

## Optional object properties 

object type 은 속성뒤에 ? 로 선택적 속성을 표현할 수 있습니다.

```js
{ propertyName?: string }
```

선택적 속성은 설정 값 외에도 void 또는 생략 될 수 있습니다. null 은 될 수 없습니다.

```js
// @flow
function acceptsObject(value: { foo?: string }) {
  // ...
}

acceptsObject({ foo: "bar" });     // Works!
acceptsObject({ foo: undefined }); // Works!
acceptsObject({ foo: null });      // Error!
acceptsObject({});                 // Works!
```

## Optional function parameters 

함수는 매개 변수 뒤에 ? 를 붙여 선택적 매개 변수를 가질 수 있습니다.

```js
function method(param?: string) { /* ... */ }
```

void 는 생략될 수 있으나, null 은 될 수 없습니다.

```js
// @flow
function acceptsOptionalString(value?: string) {
  // ...
}

acceptsOptionalString("bar");     // Works!
acceptsOptionalString(undefined); // Works!
acceptsOptionalString(null);      // Error!
acceptsOptionalString();          // Works!
```

## Function parameters with defaults 

함수 매개변수에 기본 값을 가질 수 있는 것은 ECMAScript 2015 의 특징입니다.

```js
function method(value: string = "default") { /* ... */ }
```

void 가 될 수 있으나 null 은 될 수 없습니다.

```js
// @flow
function acceptsOptionalString(value: string = "foo") {
  // ...
}

acceptsOptionalString("bar");     // Works!
acceptsOptionalString(undefined); // Works!
acceptsOptionalString(null);      // Error!
acceptsOptionalString();          // Works!
```

## Symbols 

Symbols 은 현재 Flow 에서 지원되지 않습니다. 더 많은 정보를 원하신다면 두 가지 이슈를 봐주세요 

- [facebook/flow#810](https://github.com/facebook/flow/issues/810)
- [facebook/flow#1015](https://github.com/facebook/flow/issues/1015)






