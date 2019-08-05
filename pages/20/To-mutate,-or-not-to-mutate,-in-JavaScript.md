# 자바스크립트에서  가변이거나 불변이거나

원문 : https://slemgrim.com/mutate-or-not-to-mutate/

mutable: 가변

immutable: 불변

primitive: 원시 타입

[https://ko.wikipedia.org/wiki/%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4](https://ko.wikipedia.org/wiki/불변객체)

많은 사람들이 불변성에 대한 가장 좋은 해결책에 대해 논의를 하고 있습니다. 어떤 사람들은 불변성은 엔터프라이즈 시스템이나 함수형 프로그랴밍에서의 가장 큰 부분이 아니라고 말합니다. 만약 구글에서 찾는다면(구글링을 한다면), 불변성이 무엇인지 설명하는 수 많은 의견과 기사들을 볼 수 있을 것입니다. 이 글은 자바스크립트에서 올바르게 사용되고 있는 사례를 집중적으로 다룰 것입니다.

## 기본 사항

수 많은 다른 언어에서 처럼  원시 타입들은 자바스크립트에서 불변입니다. 이 의미는 원시타입은 언제나 변경이 될 수 있고 이 원시타입이 새로운 인스턴스를 만들 수 있습니다.

```javascript
// Strings
let str = "Hello world!";
let res = str.slice(1,5); //ello

// Numbers
let number = 10;
number += 15;
```

불변은 많은 이점을 제공합니다.

- ~~동시성~~
- 인스턴스를 공유하기 위해 저장
- 사이드 이펙트가 없음
- 일시적인 커플링이 없음
- 가독성 향상
- 메모리 소비 감소
- 캐시하기 쉬움
- 테스트하기 쉬움

ELM 그리고 하스켈과 같은 어떠한 값을 변경이 불가능한 순수한 함수 언어에 많이 있습니다. 자바스크립트가 그 중 하나가 아닙니다. 자바스크립트에서 객체는 기본적으로 가변입니다. 가변을 사용하자마자 불변이 가진 장점들을 모두 사용할 수 없습니다. 하지만 가변적인 것을 불변처럼 사용하는 방법에는 여러가지가 있습니다. 

## 수동적인 방식

비록 자바스크립트는 불변 객체를 지원하지 않지만 대부분 가변을 피하는 방법으로 코드를 작성할 수 있습니다. 

### 함수에서 객체를 변경하지마세요.

주어진 오브젝트의 속성을 변경하는 대신에 변경된 복사본을 반환하는 함수를 작성합니다.

```
//bad
function save(object){
    object.saved = true;
    return object;
}

//better
function save(object){
    let newObject = object.clone();
    newObject.saved = true;
    return newObject;
}
```

### 생성자 이후에 객체를 변경하지 마세요.

객체는 참조 입니다. 속성을 변경하지 않으면 명확하지 않은 상태의 상황을 피할 수 있습니다. 또한 완성된 코드는 이해하기 쉽고 테스트하기가 더 쉽습니다.

```javascript
let request = {
    method: "GET",
    uri: "http://slemgrim.com"
}

// don't do this
request.method = "POST"
```

### 비용이 많이 들더라도 setter은 피하세요.

setter은 두 가지 사항에 중복이 됩니다. 만약 setter을 사용한다면 객체를 변경 합니다. 그래서 setter을 사용하지 않는 것이 좋습니다.

변이를 피하기 위해서 지루한 작업들이 존재합니다. 자바스크립트가 불변한 객체가 빌트인을 기본적으로 하지 않는 것은 작업자를 힘들게 하는 것입니다. 도움을 받아야 합니다.

## Immutable.js

페이스북의 [Immutable.js](https://facebook.github.io/immutable-js/)는 상태 불변을 유지하는데 도움을 주는 라이브러리 입니다. 비슷한 방법으로([Mori](https://github.com/swannodette/mori), [seamless-immutable]() ) 와 같은 각각의 라이브러리를 가지고 있지만 해당 글에서는 Immutable.js을 사용합니다.

자세한 내용은[documentation](https://facebook.github.io/immutable-js/)에 존재하기 때문에 좀 더 자세한 설명을 하지 않을 것입니다. 정말 간단하지만 마무리는 :

> Immutable.js는 `List, Stack, Map, OrderedMap, Set, OrderedSet and Record`와 같은 데이터의 불변을 제공합니다.

또는 코드의 폼에서:

```javascript
import Map from 'immutable';
var request = Map({method: 'GET', uri: 'http://slemgrim.com'});
var post = request.set('method', 'POST');
request.get('method'); // GET
post.get('method'); // POST
```

비록 객체 속성에 대한 직접적인 접근을 할 수 없지만, 가변객체에 부딪히는 대신에 본연의 목적에 집중할 수 있습니다.

## ESLint 가변

[eslint-plugin-immutable](https://github.com/jhusain/eslint-plugin-immutable) 플러그인은 자바스크립트에서 모든 가변을 사용할 수 없습니다. 리드미에 리액트, 리덕스를 언급하지만 이 두개의 플러그인을 제외하고 안전하게 사용할 수 있습니다. 

### 세가지 규칙을 플러그인에 추가합니다:

- no-let: let대신에 const를 사용하세요.
- no-this: ES6 class 사용을 금지합니다.
- no-mutation: 멤버 표현식의 결과에 값을 할당하는 것을 금지합니다.

****

## 언제 이것을 사용하나요?

### 동시성

이것은 불변성이 타당한 가장 중요한 이유 입니다. 스레드로부터 안전하기 위해서는 변경되지 앟는 항목을 잠그지 않아도 됩니다. JS에서는 실제 동시성이 없습니다. 대신 Event Loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)가 있습니다. 그래서 이것을 잊어버려도 좋습니다.

### 사이드 이펙트를 피하세요.

객체의 속성을 변경하는 것은 가변적인 것입니다.  가변은 사이드 이펙트에 의해 정의 됩니다. 사이드 이펙트를 피해야 한다고 배웠습니다. 사이드 이펙트가 없다면 함수의 구현을 들여다볼 필요가 없습니다. 코드는 추론하기 쉬우며 예측 가능하고 더 테스트 가능합니다.

### 함수형 프로그래밍

값 또는 레퍼런스를 다루고 있는 지에 대한 생각은 멈춰야 합니다. 불변의 객체는 항상 값입니다 .이것은 함수형 프로그래밍의 핵심 원리중 하나입니다. 함수에 값ㅇ르 전달하면 프로미스를 영원히 유지할 수 있습니다.

## 언제 사용하지 않을까요?

### 자주 변경되는 속성

실제로 자주 변경되는 객체가 있는 경우 모든 변경에 대해 새 인스턴스를 만드는 것이 가장 좋습니다. 새 인스턴스를 만드는 것이 가장 좋습니다. 이것은 게임과 시뮬레이션에서 특히 두번 반복되는 경우에 해당됩니다. 당신이 게임에서 불변성을 사용할 수 없다는 말은 아니지만, 변경 가능한 객체보다 성능 문제에 빠지기 쉽습니다.

### 거대한 데이터 구조

정말 거대한 데이터 트리가 불변 컨테이너로 저장되어 있다면, 메모리 소비량을 확인하는 것이 좋습니다. 물론 가비지 수집은 모든 오래된 객체를 죽일 것이지만 큰 객체를 복사하는 것은 여전히 비용이 듭니다.

## 결론

일반적으로 불변의 타입을 고수하는 것은 꽤 합리적인 언어 디자인의 선택입니다. 심지어 모든 것이 불변 일 때 멋지게 모델링하지 않는 몇 가지 문제가 있습니다. 대부분의 상황에서 불변 형의 장점은 단점보다 훨씬 중요하며 대안보다 뛰어나고 더 나은 코드와 더 빠른 실행 파일을 제공합니다.