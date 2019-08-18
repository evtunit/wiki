# Higher Order Functions in JavaScript

번역 : [http://www.zsoltnagy.eu/higher-order-functions-in-javascript/](http://www.zsoltnagy.eu/higher-order-functions-in-javascript/)

이 기사에서는 고차 함수에 대해 학습합니다. 함수형 프로그래밍의 중요한 초석은 고차 함수입니다. 대부분 functional 스타일로 프로그램을 작성하려면 고차 함수의 기본 사항을 숙지해야합니다.

먼저, 고차 함수의 간단한 정의를 배웁니다.
이미 사용중인 고급 함수를 보여줌으로써 정의를 기억할 것입니다.
고차 함수 예제를 구현하여 이 글을 마치겠습니다.

### What are higher order functions?

> 고차 함수는 함수 인수를 받거나 함수를 반환하는 함수입니다.

또는 이 정의에서 모순된 의미가 아닙니다.

이전 섹션에서 JavaScript 함수는 값이라는 것을 배웠습니다. 따라서 함수는 다른 함수로 전달 될 수 있으며 함수의 반환 값일 수도 있습니다.

이것은 고차 함수를 이해하는 데 필요한 모든 이론입니다. 고차 함수라는 이름은 처음에는 무서워 보이지만 실제로는 아무것도 두렵지 않습니다. 함수를 인수로 전달하거나 반환 값으로 반환하면됩니다.

특히 JavaScript 미니 코스를 겪은 경우 이미 많은 고차 함수를 알고있을 수 있습니다.

```javascript
setTimeout( () => console.log('done'), 1000 );
```

고차 함수의 좋은 예는 setTimeout 입니다. `setTimeout`의 첫 번째 인수는 함수이므로 `setTimeout` 은 고차 함수입니다.

```javascript
document.querySelector( '.js-submit' )
   .addEventListener( 'click', submitCallback );
```

DOM 노드에 이벤트를 추가 할 때 이벤트를 등록하는 함수는 고차 함수입니다.

`setTimeout` 과 `addEventListener` 는 모두 함수 인자를 가지고 있기 때문에 고차 함수입니다. 이제 함수를 반환하는 예제를 보자.

컨텍스트 바인딩은 바인딩 된 `this` 값이있는 함수를 반환합니다. 따라서 `bind` 는 고차 함수입니다.

```javascript
const area = function() {
	return this.width * this.height;
};

const boundArea = area.bind({ width: 2, height: 3 });

boundArea();
```

기술적으로 `bind` 는 프로토 타입 체인 상속을 통한 '영역'객체의 방법입니다. `bind` 는 독립형 함수이고 `area`는 인수 인 형식으로 메소드 호출을 항상 다시 작성할 수 있습니다.

```javascript
const bind = function( f, ...args ) {
    return f.bind( ...args );
}

const boundArea = bind( area, { width: 2, height: 3 } );
```

다시 작성한 후에는 `bind` 가 함수 인수를 허용 할뿐만 아니라 함수도 반환한다는 것을 분명히 알 수 있습니다.

### Writing higher order functions

사용법을 보여주기 위해 고차 함수를 직접 작성하고 싶을 수도 있습니다.

cents를 나타내는 정수 값을 화폐로 형식화하는 작업이 있다고 가정하십시오. 요청에는 통화 기호 지정 및 소수 구분 기호와 같은 일부 사용자 정의가 포함됩니다.

템플릿 리터럴 반환 값이 익숙하지 않은 경우 문자열 및 템플릿 리터럴에 대한 내 기사[strings and template literals](http://www.zsoltnagy.eu/strings-and-template-literals-in-es6/).를 읽으십시오.

```javascript

const formatCurrency = function( 
    currencySymbol,
    decimalSeparator  ) {
    return function( value ) {
        const wholePart = Math.trunc( value / 100 );
        let fractionalPart = value % 100;
        if ( fractionalPart < 10 ) {
            fractionalPart = '0' + fractionalPart;
        }
        return `${currencySymbol}${wholePart}${decimalSeparator}${fractionalPart}`;
    }
}

> getLabel = formatCurrency( '$', '.' );

> getLabel( 1999 )
"$19.99"

> getLabel( 2499 )
"$24.99"
```


`formatCurrency` 는 고정 통화 기호와 소수점 구분 기호가 있는 함수를 반환합니다.

포맷터에 값을 전달한 다음 전체 부분과 소수 부분을 추출하여 이 값의 형식을 지정합니다. ES6 수학 확장 _trunc_ 을 사용하여 결과를 자릅니다.

이 함수의 반환 값은 통화 기호, 전체 부분, 소수 구분 기호 및 소수 부분을 연결하는 템플릿 리터럴로 구성됩니다.

통화 기호와 소수 구분 기호는 반환 된 함수에 전달되지 않으며 고정 된 값입니다.

우리는 정수 값을 getLabel 함수에 전달할 수 있고 포맷 된 표현을 다시 얻습니다.

따라서`formatCurrency` 고차 함수는 사용 가능한 포맷터 함수를 리턴 했습니다.

### The undesirable forEach and map-reduce-filter

일부 고차 함수는 JavaScript에서 배열을 처리하는 데 유용합니다. 실제로 대부분의 기능적 스타일로 코드를 작성할 때 종종 루프 대신 이러한 함수를 사용합니다. 이러한 기능은 다음과 같습니다.

-   `map`,
-   `reduce`,
-   `filter`.

내가 왜`forEach` 방법을 언급하지 않았습니까? 결국, 우리는 루프에 대해 이야기하고 있습니다.

`forEach` 의 문제점은 함수형 프로그래밍에서 완전히 쓸모없는 함수라는 것입니다. 순수하게 기능적인 스타일로 코드를 작성할 때 `forEach` 를 사용하는 것은 의미가 없습니다. 간단한 예를 봅시다.

```javascript
const values = [1, 2, 3, 3, 5];
let sum = 0;

values.forEach( v => { sum += v; } );

console.log( sum );
```


`forEach` 헬퍼는 `값` 을 반복하고 배열의 각 값에 대한 첫 번째 인수를 호출합니다. 이 `forEach` 호출의 주요 효과는 '정의되지 않음'이 반환된다는 것입니다. 이 관점에서 함수 본문에 무엇이 있는지는 중요하지 않습니다. 함수 본문 `sum + = v` 를 실행하면 _side-effect_ 가 생성되어 `forEach` 도우미의 범위를 벗어난 컨텍스트를 수정합니다.

순수 기능 프로그래밍은 _side-effect free_ 입니다. 배열의 `forEach` 도우미는 사용 가능한 값을 반환하지 않습니다. 따라서 `forEach` 를 사용하는 유일한 이유는 `forEach`의 콜백 내에서 부작용에 의존하는 것입니다.

> 기술 인터뷰에서, 후보자들이 함수형 프로그래밍을 사용하겠다고 선언 할 때 종종 당황스러워 보이므로 `for` 루프 대신에`forEach` 도우미를 사용합니다. 이 함정에 들어 가지 마십시오.


배열을 조작하는 더 나은 기능이 있습니다. `reduce`로 같은 결과를 얻을 수 있습니다 :

```javascript
const values = [1, 2, 3, 3, 5];
const sum = values.reduce( (accumulator, v) => accumulator + v, 0 );
console.log( sum ); // 14
```

Reduce는 함수 인수가있는 고차 함수입니다. 이 함수 인수는 배열의 각 요소에서 실행됩니다. 배열에서 누산기 변수와 하나의 값을 가져옵니다. 이 함수 인수의 반환 값은 누산기의 새로운 값입니다. 이 새로운 값은 다음 요소에 속하는 다음 호출에서 사용됩니다.

각 반복에서 누산기 변수의 상태와 다음 배열 값을 인쇄하겠습니다.

```javascript
const values = [1, 2, 3, 3, 5];
const sum = values.reduce( (accumulator, v) => {
    const result = accumulator + v;
    console.log( `accumulator, v, result: ${ accumulator }, ${ v }, ${ result }` );
    return result;
}, 0 );
```

다음 값이 콘솔에 인쇄됩니다.

```javascript
accumulator,  v,  result:  0,  1,  1
accumulator,  v,  result:  1,  2,  3
accumulator,  v,  result:  3,  3,  6
accumulator,  v,  result:  6,  3,  9
accumulator,  v,  result:  9,  5,  14
```

그것이 당신이 감소에 대해 알아야 할 전부입니다. 곧 운동을 할 수 있으며 실제로 운동을 사용할 수 있습니다.

다음 함수는`map`입니다. Map은 배열의 각 요소를 가져 와서 콜백 함수를 사용하여 변환하고 변환 된 값의 배열을 반환하는 고차 함수입니다.

예를 들어, 다음 식을 사용하면 다른 2의 거듭 제곱을 얻을 수 있습니다.

```javascript
> [0, 1, 2, 3, 4].map( v => 2 ** v );
[1, 2, 4, 8, 16]
```

`**` 는 `ES2016` 에 도입 된 지수 연산자입니다. `2 ** v` 를 “2의 거듭 제곱”으로 읽습니다.

이제 `2` 의 처음 50의 힘을 만들어 봅시다. 손으로 51 개의 요소로 구성된 배열을 만들고 싶지 않다고 가정하면 `null` 요소로 배열을 만들 수 있습니다.

```javascript
new Array( 51 ).fill( null )
```

`map` 함수의 콜백은 배열의 현재 요소의 인덱스 인 두 번째 인수를 허용 할 수 있습니다.

```javascript
> new Array( 51 ).fill( null ).map( (item, index) => index )
(51) [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50]
```

한 단계 만 남았습니다. `index` 를 반환하는 대신 `2 ** 인덱스`를 반환해야합니다.

```javascript
> new Array( 51 ).fill( null ).map( (item, index) => 2 ** index )
(51) [1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048, 4096, 8192, 16384, 32768, 65536, 131072, 262144, 524288, 1048576, 2097152, 4194304, 8388608, 16777216, 33554432, 67108864, 134217728, 268435456, 536870912, 1073741824, 2147483648, 4294967296, 8589934592, 17179869184, 34359738368, 68719476736, 137438953472, 274877906944, 549755813888, 1099511627776, 2199023255552, 4398046511104, 8796093022208, 17592186044416, 35184372088832, 70368744177664, 140737488355328, 281474976710656, 562949953421312, 1125899906842624]
```

적어도 처음 20 개 값을 알고있는 소프트웨어 개발자 친구가 있습니다. 그래도이 숫자를 쓰는 것보다 빠르기 때문에`map`을 사용하여이 배열을 만듭니다.

세 번째 고차원 배열 함수는 '필터'입니다. 우리는 배열에서 일부 요소를 버리고 나머지 요소 만 유지하는 배열을 반환하려고 할 수 있습니다.

예를 들어 배열에서 모든 음수 요소를 버리고 싶다고 가정 해보십시오.

```javascript
> [-1, 5, 2, -4, -2, 2].filter( v => v >= 0);
[5, 2, 2]
```

`array.filter (f)`는 그 요소들을 필터로 유지하여`f (element)`가 참된 값을 반환합니다.

truthy 값이 무엇인지 모르면 [this article](http://www.zsoltnagy.eu/javascript-tutorial-for-absolute-beginners/) 다음을 확인하세요

### Chaining map-reduce-filter


반환 배열을 매핑하고 필터링합니다.

어레이에서 맵, 축소 및 필터가 작동합니다.

따라서 일련의 맵 및 필터 호출을 서로 연결할 수 있으며 끝에 콜을 줄 수도 있습니다.

예를 들어, 문자열 배열이 있고`a`로 시작하는 가장 긴 문자열의 길이를 찾는 데 관심이 있다고 가정하십시오.

다음 단계를 사용하여 솔루션을 찾을 수 있습니다.

1. 'a'로 시작하지 않는 요소를 필터링하십시오.
2. 요소를 길이에 맵핑하십시오.
3. 이 길이의 값들을 최대값으로 한정해라.

```javascript
const words = ['ab', 'abc', 'bcde'];

words
    .filter( w => w.startsWith( a ) )
    .map( w => w.length )
    .reduce( (acc, v) => Math.max( acc, v ), 0 );
```

참고 : 기술적으로는 최대 배열을 사용하기 위해 reduce를 사용할 필요가 없습니다. 우리는 방금 쓸 수있었습니다 :

```javascript
Math.max( ...wordLengths );
```

더 많은 준비가 되셨다면이 블로그 게시물에서 맵 감소 필터 연습을 읽어보십시오. SQL 문을 map-reduce-filter 호출로 변환해야합니다.

비디오 버전을 선호하는 경우 여기에서 확인하십시오.

<iframe width="560" height="315" src="https://www.youtube.com/embed/uhQptpmffhY" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen=""></iframe>

### Summary

고차 함수는 함수 인수를 받거나 함수를 반환하는 함수입니다.

당신은 아마도 그것들에 대해 생각하지 않고 많은 고차 함수를 이미 사용하고있을 것입니다. `setTimeout`, 이벤트 리스너 콜백 또는`bind` 함수를 생각해보십시오.

`map`,`reduce` 및`filter`와 같은 일부 고차 함수는 배열을보다 쉽게 ​​처리하는 데 도움이됩니다.

함수형 프로그래밍에 대한 자세한 내용을 보려면 Safari Books Online : [Functional Dive into Functional JavaScript](https://www.safaribooksonline.com/library/view/deep-dive-into/9781787281899)에서 5 성급 코스를 확인하십시오. /). 이 과정을 기능적 프로그래밍에 대한 훌륭한 입문 자료로 강력히 추천 할 수 있습니다.

때로는 Packt 사이트에서도 많은 것을 얻을 수 있습니다. Packt의 [Deep Dive into Functional JavaScript](https://www.packtpub.com/web-development/deep-dive-functional-javascript-video) 페이지입니다. 그러나 Packt의 프로모션은 내가 관리 할 수 ​​없으므로 $ 120- $ 140 가격표가 10 달러로 하락한시기를 알 수 없습니다.

마지막으로 JavaScript 미니 코스에 가입하면 더 많은 정보를 얻을 수 있습니다.


-   arrow functions,
-   destructuring,
-   functions with variable number of arguments,
-   the ES6 class syntax,
-   the spread operator and rest parameters,
-   destructuring.