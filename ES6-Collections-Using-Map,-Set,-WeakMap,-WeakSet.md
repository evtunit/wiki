# ES6 Collections: Using Map, Set, WeakMap, WeakSet

원문 : https://www.sitepoint.com/es6-collections-map-set-weakmap-weakset/

**이 글에서는 4 개의 새로운 ES6 컬렉션과 이들이 제공하는 장점을 살펴 봅니다. **

대부분의 주요 프로그래밍 언어에는 여러 유형의 데이터 컬렉션이 있습니다. 파이썬에는 리스트, 튜플 및 딕셔너리가 있습니다. Java에는 리스트, 셋, 맵, 큐가 있습니다. 루비에는 해시와 배열이 있습니다. 지금까지 자바스크립트에는 배열 만있었습니다. 객체와 배열은 자바스크립트의 핵심이었습니다. ES6에는 언어에 힘과 표현력을 추가하는 4 가지 새로운 데이터 구조 인 맵, 셋, 위크셋 및 위크맵이 도입되었습니다.

## 자바스크립트 해시맵 찾기

해시 맵, 딕셔너리 및 해시는 다양한 프로그래밍 언어에서 키 / 값 쌍을 저장하는 여러 가지 방법이며 이러한 데이터 구조는 빠른 검색을 위해 최적화됩니다.

ES5에서 키와 값을 갖는 임의의 속성 컬렉션 인 JavaScript 객체는 해시를 시뮬레이션 할 수 있지만 [객체를 해시로 사용하는 경우 몇 가지 단점](http://www.2ality.com/2012/01/objects -as-maps.html)을 가진다.

### 단점 #1: ES5에서 키는 문자열이어야 합니다.

JavaScript 객체 속성 키는 문자열이어야하므로 다양한 데이터 유형의 키 / 값 쌍 모음으로 제공되는 기능이 제한됩니다. 물론 다른 데이터 유형을 문자열로 coerce/stringify할 수 있지만 추가 작업이 추가됩니다.

### 단점 #2: 객체는 본질적으로 반복이 가능하지 않습니다.

객체는 컬렉션으로 사용하도록 설계되지 않았기 때문에 객체의 속성 수를 결정하는 효율적인 방법이 없습니다. (예를 들어 [Object.keys is slow](https://jsperf.com/object-keys-vs-for-in-with-closure/3))를 참조하십시오. 객체의 속성을 반복하면 프로토 타입 속성도 얻을 수 있습니다. iterable 속성을 모든 객체에 추가 할 수 있지만 모든 객체가 컬렉션으로 사용되는 것은 아닙니다. `for… in` 루프와 `hasOwnProperty()`메소드를 사용할 수 있지만 이는 해결 방법 일뿐입니다. 객체의 속성을 반복 할 때 속성을 삽입 한 순서대로 검색 할 필요는 없습니다.

### 단점 #3: 내장된 메소드와 충돌이 존재합니다.

객체에는 `constructor`, `toString` 및 `valueOf`와 같은 내장 메소드가 있습니다. 이러한 속성 중 하나가 속성으로 추가 된 경우 충돌이 발생할 수 있습니다. `Object.create(null)`을 사용하여 (object.prototype에서 상속되지 않은) 베어(??) 객체를 작성할 수 있지만 이를 해결하기 위한 방법 뿐입니다.

ES6에는 새로운 컬렉션 데이터 유형이 포함되어 있으므로 더 이상 객체를 사용하고 단점을 극복 할 필요가 없습니다.

## ES6에서의 맵 컬렉션 사용

`맵`은 우리가 살펴볼 첫 번째 데이터 구조 / 컬렉션입니다. 맵은 모든 유형의 키 및 값 모음입니다. 새 맵를 쉽게 만들고 값을 추가 / 삭제하고 키 / 값을 반복하여 크기를 효율적으로 결정할 수 있습니다. 중요한 방법은 다음과 같습니다.



### 맵 생성 및 일반적인 메소드의 사용

```javascript
const map = new Map();
map.set('hobby', 'cycling');

const foods = { dinner: 'Curry', lunch: 'Sandwich', breakfast: 'Eggs' }; 
const normalfoods = {};

map.set(normalfoods, foods);

for (const [key, value] of map) {
  console.log(`${key} = ${value}`); 
}

map.forEach((value, key) => {
  console.log(`${key} = ${value}`);
}, map);

map.clear();
console.log(map.size === 0);

```

[Run this example on JSBin](https://sitepointeditors.jsbin.com/buwecaz/edit?js,console)

## 셋 컬렉션 사용

셋은 중복이 포함되지 않은 정렬 된 값 목록입니다. 배열처럼 인덱싱되는 대신 키를 사용하여 집합에 접근합니다. 세트는 이미 [Java](https://docs.oracle.com/javase/7/docs/api/java/util/Set.html), [Ruby](http://ruby-doc.org/stdlib)에 있습니다. -2.3.0 / libdoc / set / rdoc / Set.html), [Python](https://docs.python.org/2/library/sets.html) 및 기타 여러 언어가 있습니다. ES6 세트와 다른 언어와의 한 가지 차이점은 순서가 ES6에서 중요하다는 것입니다 (다른 많은 언어에서는 그렇지 않음). 중요한 Set 메소드는 다음과 같습니다.

```javascript
const planetsOrderFromSun = new Set();
planetsOrderFromSun.add('Mercury');
planetsOrderFromSun.add('Venus').add('Earth').add('Mars');
console.log(planetsOrderFromSun.has('Earth'));

planetsOrderFromSun.delete('Mars');
console.log(planetsOrderFromSun.has('Mars'));

for (const planet of planetsOrderFromSun) {
  console.log(planet);
}
console.log(planetsOrderFromSun.size);

planetsOrderFromSun.add('Venus');
console.log(planetsOrderFromSun.size);

planetsOrderFromSun.clear();
console.log(planetsOrderFromSun.size);
```

[Run this example on JSBin](https://jsbin.com/pepobiz/1/edit?js,console)

## 위크 컬렉션, 메모리, 가비지 컬렉션

JavaScript 가비지 콜렉션은 더 이상 참조되지 않는 객체가 자동으로 삭제되고 자원이 재생되는 메모리 관리 양식입니다.

객체에 대한 맵 및 셋의 참조는 강력하게 유지되며 가비지 컬렉션을 허용하지 않습니다. DOM에서 이미 제거 된 DOM 요소와 같이 더 이상 필요하지 않은 큰 객체를 맵 / 셋이 참조하는 경우 비용이 많이들 수 있습니다.

이를 해결하기 위해 ES6에는 `위크맵`과 `위크셋`이라는 두 개의 새로운 위크 콜렉션이 도입되었습니다. 이 ES6 콜렉션은 더 이상 필요하지 않은 객체를 메모리에서 지울 수 있기 때문에 '위크' 입니다.

## 위크맵

위크맵은 우리가 다루고있는 새로운 ES6 컬렉션 중 세 번째입니다. 위크맵은 일반적인 맵과 유사하지만 가비지 콜렉션과 관련하여 방법이 적고 앞서 언급 한 차이점이 있습니다.

```javascript
const aboutAuthor = new WeakMap();
const currentAge = {};
const currentCity = {};

aboutAuthor.set(currentAge, 30);
aboutAuthor.set(currentCity, 'Denver');

console.log(aboutAuthor.has(currentCity));

aboutAuthor.delete(currentAge);

```

[Run this example on JSBin](https://jsbin.com/yejoxig/edit?js,console)

### 사용 사례

위크맵의 [몇 가지 널리 사용되는 사례가 있음](http://stackoverflow.com/questions/29413222/what-are-the-actual-uses-of-es6-weakmap). 객체의 private 데이터를 private로 유지하고 DOM 노드 / 객체를 추적하는 데 사용할 수 있습니다.

### Private 데이터 사용 사례

[JavaScript expert Nicholas C. Zakas](https://www.nczonline.net/blog/2014/01/21/private-instance-members-with-weakmaps-in-javascript/) 에서 사용 사례를 확인할 수 있습니다. :

```javascript
var Person = (function() {
  var privateData = new WeakMap();

  function Person(name) {
    privateData.set(this, { name: name });
  }

  Person.prototype.getName = function() {
    return privateData.get(this).name;
  };

  return Person;
}());
```

여기에서 '위크맵'을 사용하면 객체의 데이터를 private로 유지하는 프로세스가 간소화됩니다. `Person` 객체를 참조 할 수 있지만 특정 `Person` 인스턴스가 없으면 `privateDataWeakMap`에 대한 액세스가 허용되지 않습니다.

### DOM 노드에서 사용 사례

[Google Polymer 프로젝트](https://github.com/Polymer)는 PositionWalker라는 코드에서`WeakMaps '를 사용합니다.

> PositionWalker는 DOM 하위 트리 내의 위치를 현재 노드 및 해당 노드 내의 오프셋으로 추적합니다.

[위크맵](https://github.com/Polymer/designer/blob/ae5df5d85a008dfc5bedba4f3cfc1f2ac656b011/src/text/PositionWalker.js#L65)을 사용하여 DOM 노드 편집, 제거 및 변경 사항을 추적합니다.

```jsx
_makeClone() {
  this._containerClone = this.container.cloneNode(true);
  this._cloneToNodes = new WeakMap();
  this._nodesToClones = new WeakMap();

  ...

  let n = this.container;
  let c = this._containerClone;

  // find the currentNode's clone
  while (n !== null) {
    if (n === this.currentNode) {
    this._currentNodeClone = c;
    }
    this._cloneToNodes.set(c, n);
    this._nodesToClones.set(n, c);

    n = iterator.nextNode();
    c = cloneIterator.nextNode();
  }
}
```

## 위크셋

위크셋은 참조하는 객체가 더 이상 필요하지 않을 때 요소를 가비지 컬렉션 할 수있는 세트 컬렉션입니다. 위크셋은 반복을 허용하지 않습니다. [사용 사례는 다소 제한적입니다](https://esdiscuss.org/topic/actual-weakset-use-cases) (현재로서는). 대부분의 얼리 어답터들은 위크셋을 사용하면 객체를 변경하지 않고 태그를 지정할 수 있습니다. (http://stackoverflow.com/questions/30556078/ecmascript-6-what-is-weakset-for). [ES6-Features.org](http://es6-features.org/)에는 [위크셋에서 요소를 추가 및 삭제하는 예](http://es6-features.org/#WeakLinkDataStructures)가 있습니다. 객체가 표시되었는지 여부를 추적합니다.

```jsx
let isMarked     = new WeakSet()
let attachedData = new WeakMap()

export class Node {
    constructor (id)   { this.id = id                  }
    mark        ()     { isMarked.add(this)            }
    unmark      ()     { isMarked.delete(this)         }
    marked      ()     { return isMarked.has(this)     }
    set data    (data) { attachedData.set(this, data)  }
    get data    ()     { return attachedData.get(this) }
}

let foo = new Node("foo")

JSON.stringify(foo) === '{"id":"foo"}'
foo.mark()
foo.data = "bar"
foo.data === "bar"
JSON.stringify(foo) === '{"id":"foo"}'

isMarked.has(foo)     === true
attachedData.has(foo) === true
foo = null  /* remove only reference to foo */
attachedData.has(foo) === false
isMarked.has(foo)     === false
```

## 모든 것을 맵으로 ? 레코드 vs ES6 컬렉션

맵 및 셋은 키 / 값 쌍의 멋진 새 ES6의 콜렉션입니다. 즉, 자바스크립트 객체는 여러 상황에서 여전히 컬렉션으로 사용될 수 있습니다. 상황이 요구하지 않는 한 새로운 ES6 모음으로 변경 할 필요가 없습니다.

[MDN문서] (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)에서는 언제 객체 또는 키 컬렉션을 사용할지 알려줍니다.

- 일반적으로 런타임까지 키를 알 수 없으며 동적으로 찾아야합니까?
- 모든 값의 유형이 동일하고 호환 가능합니까?
- 문자열이 아닌 키가 필요합니까?
- 키-값 쌍이 종종 추가되거나 제거됩니까?
- 임의의 (쉽게 변경되는) 양의 키-값 쌍이 있습니까?
- 컬렉션이 반복됩니까?

## 새로운 ES6 컬렉션으로 더욱 유용한 자바 스크립트 생성

JavaScript 컬렉션은 이전에는 상당히 제한되었지만 ES6에서는 수정되었습니다. 이 새로운 ES6 컬렉션은 언어의 강력한 기능과 유연성을 제공 할뿐만 아니라이를 채택한 JavaScript 개발자의 작업을 단순화합니다.

------

해당 부분에 대한 번역은 본문의 글과 조금 상이하거나 불필요한 부분이라 생각이 되어 생략 하였습니다. (이 글의 근원지와 관련된 내용)