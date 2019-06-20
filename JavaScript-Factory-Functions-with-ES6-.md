![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)  

````
Note : 이것은 자바 스크립트 ES6+ 의 함수 프로그래밍 및 합성 소프트웨어 기술을 처음부터 배우는 "Composing Software"시리즈(현재 책)의 
       일부입니다. 계속 지켜봐라. 이보다 더 많은 것이 있습니다.!
````

팩토리 함수는 객체(아마도 새로운)를 반환하는 클래스나 생성자가 아닌 함수입니다.  
JavaScript에서는 모든 함수가 객체를 반환 할 수 있습니다. new 키워드가 없으면 팩토리 함수입니다.  

팩토리 함수는 JavaScript에서 항상 매력적이었습니다.  
클래스의 복잡성과 새 키워드로 뛰어들어 가지 않고도 객체 인스턴스를 쉽게 생성 할 수 있기 때문입니다.  

JavaScript는 매우 편리한 객체 리터럴 구문을 제공합니다. 다음과 같이 보입니다.  
````
const user = {
  userName: 'echo',
  avatar: 'echo.png'
};
```` 

JavaScript의 객체 리터럴 표기법에 기반한 JSON과 마찬가지로 :의 왼쪽은 속성 이름이고 오른쪽은 값입니다.  
점 표기법을 사용하여 props에 액세스 할 수 있습니다.  
````
console.log(user.userName); // "echo"
````

대괄호 표기법을 사용하여 계산 된 특성 이름에 액세스 할 수 있습니다.  
````
const key = 'avatar';
console.log( user[key] ); // "echo.png"
````  

원하는 속성 이름과 동일한 이름의 범위 변수가있는 경우 객체 리터럴 만들기에서 콜론과 값을 생략 할 수 있습니다.  
````
const userName = 'echo';
const avatar = 'echo.png';
const user = {
  userName,
  avatar
};
console.log(user);
// { "avatar": "echo.png",   "userName": "echo" }

````  

객체 리터럴은 간결한 메소드 구문을 지원합니다. .setUserName () 메소드를 추가 할 수 있습니다.  

````
const userName = 'echo';
const avatar = 'echo.png';
const user = {
  userName,
  avatar,
  setUserName (userName) {
    this.userName = userName;
    return this;
  }
};
console.log(user.setUserName('Foo').userName); // "Foo"
````  

간결한 메소드에서, 이것은 메소드가 호출되는 오브젝트를 참조합니다. 객체의 메서드를 호출하려면 객체 도트 표기법을 사용하여  
메서드에 액세스하고 괄호를 사용하여 메서드를 호출하면됩니다. 예를 들어 game.play ()는 .play ()를 게임 객체에 적용합니다.  
도트 표기법을 사용하여 메소드를 적용하려면 해당 메소드가 해당 오브젝트의 특성이어야합니다.  
함수 프로토 타입 메서드 인 .call (), .apply () 또는 .bind ()를 사용하여 다른 임의의 개체에 메서드를 적용 할 수도 있습니다.  


이 경우 user.setUserName ( 'Foo')는 .setUserName ()을 사용자에게 적용하므로  
this === user. .setUserName () 메서드에서이 바인딩을 통해 사용자 개체의 .userName 속성을 변경하고 메서드 체인에 대해  
동일한 객체 인스턴스를 반환합니다.  

## 하나에 대한 리터럴, 많은 Factories

많은 객체를 생성해야하는 경우 객체 리터럴과 팩토리 함수의 기능을 결합해야합니다.

팩토리 함수를 사용하여 원하는 만큼 많은 사용자 객체를 생성 할 수 있습니다.  
예를 들어 채팅 앱을 만드는 경우 현재 사용자를 나타내는 사용자 객체와 현재 로그인하고 채팅하는 다른 모든 사용자를 나타내는 많은 다른 사용자 객체를 가질 수 있으므로 이름을 표시 할 수 있습니다 아바타도.  

사용자 객체를 createUser () 팩토리로 변환합시다.  
````
const createUser = ({ userName, avatar }) => ({
  userName,
  avatar,
  setUserName (userName) {
    this.userName = userName;
    return this;
  }
});
console.log(createUser({ userName: 'echo', avatar: 'echo.png' }));
/*
{
  "avatar": "echo.png",
  "userName": "echo",
  "setUserName": [Function setUserName]
}
*/
````

## 반환객체

화살표 함수 (=>)는 암시 적 반환 특성을 가지고 있습니다. 함수 본문이 단일 표현식으로 구성되어 있으면 return 키워드를 생략 할 수 있습니다. () => 'foo'는 매개 변수를 사용하지 않고 " foo ".  

객체 리터럴을 반환 할 때주의하십시오. 기본적으로 JavaScript는 중괄호를 사용할 때 함수 본문을 만들려고한다고 가정합니다.  
(예 : {broken : true}). 객체 리터럴에 대해 암시 적 반환을 사용하려면 괄호 안에 객체 리터럴을 래핑하여 명확하게 해야합니다.  

````
const noop = () => { foo: 'bar' };
console.log(noop()); // undefined
const createFoo = () => ({ foo: 'bar' });
console.log(createFoo()); // { foo: "bar" }
````

첫 번째 예에서 foo :는 레이블로 해석되고 bar는 할당되거나 반환되지 않는 표현식으로 해석됩니다. 이 함수는 undefined를 반환합니다.  

createFoo () 예제에서 괄호는 중괄호를 함수 본문 블록이 아닌 평가할 표현식으로 해석하도록합니다.  

## 해체
함수 시그니처에 특별한주의를 기울이십시오.
````
const createUser = ({ userName, avatar }) => ({
````

이 줄에서 중괄호 ({,})는 객체 파괴를 나타냅니다. 이 함수는 하나의 인수 (객체)를 사용하지만 단일 인수 인 userName과 아바타에서  
두 개의 형식 매개 변수를 소멸시킵니다. 이러한 매개 변수는 함수 본문 범위에서 변수로 사용될 수 있습니다. 배열을 소멸시킬 수도 있습니다 :  
````
const swap = ([first, second]) => [second, first];
console.log( swap([1, 2]) ); // [2, 1]
````  

나머지 구문을 사용하여 (... varName) 배열 (또는 인수 목록)에서 나머지 값을 수집 한 다음 해당 배열 요소를  
다시 개별 요소에 분산시킬 수 있습니다.  

````
const rotate = ([first, ...rest]) => [...rest, first];
console.log( rotate([1, 2, 3]) ); // [2, 3, 1]
````

## 계산 된 속성 키
이전에 우리는 액세스 할 객체 속성을 동적으로 결정하기 위해 대괄호 산술 속성 접근 표기법을 사용했습니다 :  

````
const key = 'avatar';
console.log( user[key] ); // "echo.png"
````  

다음에 할당 할 키 값을 계산할 수도 있습니다.

````
const arrToObj = ([key, value]) => ({ [key]: value });
console.log( arrToObj([ 'foo', 'bar' ]) ); // { "foo": "bar" }
````  

이 예제에서 arrToObj는 키 / 값 쌍 (일명 튜플)로 구성된 배열을 가져 와서 객체로 변환합니다.  
키의 이름을 알지 못하기 때문에 객체의 키 / 값 쌍을 설정하려면 속성 이름을 계산해야합니다.  
이를 위해 계산 된 속성 접근자에서 대괄호 표기법을 빌려 객체 리터럴을 작성하는 컨텍스트에서 다시 사용합니다.  

````
{ [key]: value }
````

채우는 작업이 끝나면, 우리는 결국 다음과 같은 최종 대상을 얻게 된다.

````
{ "foo": "bar" }
````  

## 기본 매개 변수
JavaScript의 함수는 기본 매개 변수 값을 지원하며 다음과 같은 몇 가지 이점이 있습니다.  

1. 사용자는 적절한 기본값으로 매개 변수를 생략 할 수 있습니다.  
2. 기본값은 예상되는 입력의 예를 제공하기 때문에 이 함수는 더 자체적으로 문서화됩니다.  
3. IDE와 정적 분석 도구는 기본값을 사용하여 매개 변수에 필요한 유형을 추론 할 수 있습니다.  
   예를 들어, 기본값 1은 매개 변수가 Number 유형의 멤버를 사용할 수 있음을 의미합니다.  

기본 매개 변수를 사용하여 createUser 팩토리에 대한 예상 인터페이스를 문서화하고 사용자 정보가 제공되지 않은 경우  
'익명'세부 정보를 자동으로 채웁니다.  

````
const createUser = ({
  userName = 'Anonymous',
  avatar = 'anon.png'
} = {}) => ({
  userName,
  avatar
});
console.log(
  // { userName: "echo", avatar: 'anon.png' }
  createUser({ userName: 'echo' }),
  // { userName: "Anonymous", avatar: 'anon.png' }
  createUser()
);
```` 

함수 시그니처의 마지막 부분은 아마도 약간 우습게 보일 것입니다.  

````
} = {}) => ({
````
서명이 닫히기 직전의 마지막 = {} 비트는이 매개 변수에 아무것도 전달되지 않으면 빈 객체를 기본값으로 사용한다는 것을 의미합니다.  
빈 객체에서 값을 제거하려고하면 속성에 대한 기본값이 자동으로 사용됩니다. 기본 값은 다음과 같습니다.  
undefined를 미리 정의 된 값으로 바꾸십시오.  

= {} 기본값이 없으면 undefined에서 속성에 액세스 할 수 없기 때문에 인수가없는 createUser()가 오류를 발생시킵니다.  

### Type Inference
JavaScript는 이 글을 쓰는 동안 네이티브 타입 주석을 가지고 있지 않지만 JSDoc (더 나은 옵션의 출현으로 인해 감소),  
페이스 북의 플로우 및 마이크로 소프트의 타입 스크립트를 포함하여 몇 가지 경쟁 포맷이 그 격차를 해소하기 위해 수년간 급성장 해 왔습니다.  
나는 rtype을 문서화에 사용한다 - 기능적 프로그래밍을위한 TypeScript보다 훨씬 더 읽기 쉬운 표기법.  

이 글을 쓰는 시점에는 유형 주석에 대해 확실한 승자가 없습니다.  
JavaScript 사양에는 별다른 대안이 없으며 모든 기능에 명확한 단점이있는 것으로 보입니다.  

유형 유추는 사용 된 문맥을 기반으로 유추 유형을 추론하는 프로세스입니다. 자바 스크립트에서는 주석을 입력하는 좋은 대안입니다.   
 
표준 JavaScript 함수에서 추론을 위한 단서를 충분히 제공한다면 비용이나 위험이 없는 유형 주석의 이점 대부분을 얻을 수 있습니다.  

TypeScript 또는 Flow와 같은 도구를 사용하기로 결정한 경우에도 유형 유추로 가능한 한 많이 수행하고 유형 유추가 부족한 상황에서  
유형 주석을 저장해야합니다. 예를 들어 공유 인터페이스를 지정하는 자바 스크립트에는 기본 방법이 없습니다.  
이는 TypeScript 또는 rtype에서 쉽고 유용합니다.  

Tern.js는 많은 코드 편집기와 IDE 용 플러그인이있는 JavaScript 용 유추형 유추 도구입니다.  

Microsoft의 Visual Studio 코드는 TypeScript의 유형 유추 기능을 일반 JavaScript 코드로 가져 오기 때문에 Tern이 필요하지 않습니다.  

JavaScript에서 함수의 기본 매개 변수를 지정하면 Tern.js, TypeScript 및 Flow와 같은 유형 유추가 가능한 도구는 올바르게 작업중인 API를 사용할 수 있도록 IDE 힌트를 제공합니다.  

디폴트가 없다면, IDE는 예상되는 매개 변수 유형을 파악하기위한 충분한 힌트를 가지고 있지 않습니다.  
  
![](https://cdn-images-1.medium.com/max/1600/1*2sP_9k1e0dkgYqdEPs0G3g.png)  

기본값을 사용하면 IDE (예 : 사람)가 예제에서 유추 할 수 있습니다.  

![](https://cdn-images-1.medium.com/max/1600/1*tFUXCLA8ClAzsPgZXGGR9A.png)  

매개 변수를 고정 유형으로 제한하는 것이 의미가있는 것은 아닙니다(일반 함수와 고차 함수를 어렵게 만듭니다). 
하지만 이해가되면 기본 매개 변수가 가장 좋은 방법입니다. TypeScript 또는 Flow를 사용합니다.  

## Mixin 합성을 위한 팩토리 함수들
팩토리는 멋진 호출 API를 사용하여 객체를 크랭크 아웃하는데 적합합니다.  
일반적으로 필요한 것은 전부이지만, 한 번에 여러 유형의 객체에 비슷한 기능을 구현하는 경우가 있습니다.  
이러한 기능을 기능 믹스로 추상화하여 보다 쉽게 ​​재사용 할 수 있습니다.  

그것이 기능 믹스가 빛나는 곳입니다. 모든 객체 인스턴스에 .constructor 속성을 추가하려면 withConstructor 믹스 인을 빌드하십시오.  

with-constructor.js:
````
const withConstructor = constructor => o => ({
  // create the delegate [[Prototype]]
  __proto__: {
    // add the constructor prop to the new [[Prototype]]
    constructor
  },
  // mix all o's props into the new object
  ...o
});
````  

이제 가져 와서 다른 믹스와 함께 사용할 수 있습니다.  

````
import withConstructor from `./with-constructor';
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
// or `import pipe from 'lodash/fp/flow';`
// Set up some functional mixins
const withFlying = o => {
  let isFlying = false;
  return {
    ...o,
    fly () {
      isFlying = true;
      return this;
    },
    land () {
      isFlying = false;
      return this;
    },
    isFlying: () => isFlying
  }
};
const withBattery = ({ capacity }) => o => {
  let percentCharged = 100;
  return {
    ...o,
    draw (percent) {
      const remaining = percentCharged - percent;
      percentCharged = remaining > 0 ? remaining : 0;
      return this;
    },
    getCharge: () => percentCharged,
    getCapacity: () => capacity
  };
};
const createDrone = ({ capacity = '3000mAh' }) => pipe(
  withFlying,
  withBattery({ capacity }),
  withConstructor(createDrone)
)({});
const myDrone = createDrone({ capacity: '5500mAh' });
console.log(`
  can fly:  ${ myDrone.fly().isFlying() === true }
  can land: ${ myDrone.land().isFlying() === false }
  battery capacity: ${ myDrone.getCapacity() }
  battery status: ${ myDrone.draw(50).getCharge() }%
  battery drained: ${ myDrone.draw(75).getCharge() }% remaining
`);
console.log(`
  constructor linked: ${ myDrone.constructor === createDrone }
`);
````  

보시다시피 재사용 가능한 withConstructor() 믹스인은 다른 믹스인과 함께 파이프 라인으로 간단히 드롭 됩니다.  
withBattery()는 로봇, 전기 스케이트 보드 또는 휴대용 장치 충전기와 같은 다른 종류의 객체와 함께 사용할 수 있습니다.  
withFlying()은 비행중인 자동차, 로켓 또는 풍선을 모델링하는 데 사용될 수 있습니다.  

컴포지션은 코드의 특정 기술보다 사고 방식에 가깝습니다. 당신은 여러 가지 방법으로 그것을 성취 할 수 있습니다.  
함수 작성은 처음부터 다시 빌드하는 것이 가장 쉬운 방법이며, 팩토리 함수는 구현 세부 사항에 친숙한 API를 간단하게 래핑하는 간단한 방법입니다.  

## 결론
ES6은 객체 생성 및 팩토리 기능을 처리하기위한 편리한 구문을 제공합니다.  
대부분 당신이 필요로하는 것은 전부지만, JavaScript이기 때문에 자바와 같은 느낌을 주는 또 다른 접근 방식이 있습니다 : class 키워드.  

JavaScript에서 클래스는 공장보다 더 장황하고 제한적이며 리팩토링에 있어서는 약간의 지뢰밭이지만  
React와 Angular와 같은 주요 프론트엔드 프레임 워크에 의해 수용되어 왔으며 몇 가지 드문 용도가 있습니다. 복잡성을 가치있게 만드는 경우.  

````
"때로는 우아한 구현은 단지 함수 일 뿐이지, 메소드가 아니라 클래스가 아니라 프레임 워크가 아니라 함수 일뿐입니다."~ John Carmack
````  

가장 간단한 구현으로 시작하고 필요한 경우에만 보다 복잡한 구현으로 이동하십시오.
