### 좋은 자바 스크립트 개발자는 DOM을 탐색하는 방법을 알아야합니다. 이는 다른 요소에서 요소를 선택하는 행위입니다.

그렇다면 DOM을 탐색하는 법을 배워야하는 이유는 무엇입니까? 
document.querySelector는 우리가 필요로 하는 대부분의 것에 충분하지 않은가요?  

이 기사에서는 traversing이 document.querySelector보다 나은 이유와 프로처럼 등산하는 방법을 보여 드리겠습니다.  
앉아서 긴장을 풀고 기사를 즐기십시오!  

계속 진행하기 전에 이 기사는 Learn JavaScript의 샘플 강의입니다.  
이전에 시도해 보았지만 실패한 경우에도 JavaScript를 한 번에 배우는 데 도움이되는 과정입니다.  
이 링크를 통해 JavaScript 배우기에 대해 자세히 알아보십시오.  

## 우리가 traverse 하는 이유

이웃집에 가고 싶다고 합시다. 가장 빠르고 효율적인 방법은 무엇입니까?  
1. 당신의 집에서 그들의 집으로 이동하시오 (그들의 주소를 이미 알고 있기 때문에)
2. Google지도에서 주소를 검색 한 다음 Google에서 제공하는 안내에 따라 이동하십시오.  

집에서 집으로 직접 이동하는 경우 DOM을 가로 지르는 것과 동일한 작업을 수행합니다. 인접 요소에서 하나의 요소를 선택하는 것입니다.  

Google에서 해당 주소를 조회하는 경우 document.querySelector와 동일한 작업을 수행하여 요소를 찾습니다.  

어떤 방법이 더 효율적인지 추측 할 수 있습니까?  

````
<div class="neighborhood">
  <div class="your-house">😎</div>
  <div class="neighbor-house">🎉</div>
</div>
````

아마 답을 알고있을 것입니다.  
전체 요소를 검색하는 것보다 요소에서 다른 요소로 이동하는 것이 더 쉽습니다.  
그것이 우리가 DOM을 traverse하는 이유입니다.  

세 가지 방향으로 이동할 수 있습니다.
1. 아래로
2. 옆으로
3. 위로
(참고 : 더 신뢰할만한 두 번째 이유가 있지만 다른 날의 고급 주제입니다.)  

## 아래로 traversing

아래쪽으로 traverse 하는 두 가지 방법이 있습니다.
1. querySelector or querySelectorAll
2. children

### querySelector or querySelectorAll

특정 요소에서 아래로 이동하려면 element.querySelector 또는 element.querySelectorAll을 사용할 수 있습니다.  
element.querySelector를 집으로 비유한다면 우리는 당신의 집에서 특정한 방을 찾습니다.  
우주 공간(문서)에서 동일한 방을 검색하는 것보다 빠릅니다.  

````
<div class="component">
  <h2 class="component__title">Component title</h2>
</div>

const component = document.querySelector('.component')
const title = component.querySelector('.component__title')

console.log(title) // <h2 class="component__title"> ... </h2>
````

### children

하위 항목은 직접 하위 항목(다른 요소에 즉시 중첩되는 요소)을 선택할 수 있는 속성이다.  
하위 요소가 변경되면 업데이트되는 HTML 컬렉션을 반환한다.  

````
<ul class="list">
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
  <li><a href="#">Link 3</a></li>
  <li><a href="#">Link 4</a></li>
  <li><a href="#">Link 5</a></li>
</ul>
const list = document.querySelector('.list')
const listItems = list.children

console.log(listItems)
````
![](https://zellwk.com/images/2018/traversal/children.png)  

HTML 콜렉션은 NodeList와 유사합니다 (그 querySelectorAll이 리턴합니다). 이 기사의 맥락에서 중요한 것은 미묘한 차이가 있습니다.  

중요한 것은 HTML 컬렉션은 배열과 같은 객체입니다.  
Array.prototype.forEach로 루프를 반복하려면 먼저 Array.from을 사용하여 배열로 변환해야합니다.  

### Selecting a specific child

노드 목록 (querySelectorAll의 결과)과 HTML 콜렉션 (하위 결과)에서 목록의 n 번째 항목을 선택할 수 있습니다.  
이렇게하려면 배열에서 특정 항목을 선택하는 것처럼 요소의 인덱스를 사용합니다.  

````
<ul class="list">
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
  <li><a href="#">Link 3</a></li>
  <li><a href="#">Link 4</a></li>
  <li><a href="#">Link 5</a></li>
</ul>
const listItems = document.querySelectorAll('li')

const firstItem = listItems[0]
const secondItem = listItems[1]
const thirdItem = listItems[2]
const fourthItem = listItems[3]
const fifthItem = listItems[4]

console.log(firstItem)
console.log(secondItem)
console.log(thirdItem)
console.log(fourthItem)
console.log(fifthItem)
````  
![](https://zellwk.com/images/2018/traversal/specific-child.png)  

위 코드를 HTML Collection으로 시도해보십시오. 똑같은 결과가 나옵니다.  

## 위로 traversing

위쪽으로 traverse하는 두 가지 방법이 있습니다.  
1. parentElement
2. closest

### parentElement

parentelement는 부모 요소를 선택할 수있는 속성입니다. 상위 요소는 현재 요소를 묶는 요소입니다.  

다음 HTML에서 .list는 모든 <li>의 부모 요소입니다. 각 <li>는 해당 <a>의 부모 요소입니다.  

````
<ul class="list">
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
  <li><a href="#">Link 3</a></li>
  <li><a href="#">Link 4</a></li>
  <li><a href="#">Link 5</a></li>
</ul>
const firstListItem = document.querySelector('li')
const list = firstListItem.parentElement

console.log(list)
// <ul class="list">...</ul>
````  

### closest

parentElement는 하나 이상의 레벨을 위쪽으로 선택하는 데 적합합니다.  
현재 요소 위에 여러 수준이 될 수있는 요소를 찾으려면 가장 가까운 방법을 사용합니다.  

가장 가까운 셀렉터와 일치하는 가장 가까운 상위 요소를 선택할 수 있습니다. 구문은 다음과 같습니다.  

````
const closestAncestor = Element.closest(selector)
````
의심하는 바와 같이 선택기는 QuerySelector 및 querySelectorAll에 전달되는 선택 항목과 동일하다.  

다음 HTML에서 Element.closest로 쉽게 <a>에서 .list를 선택할 수 있습니다.  

````
<ul class="list">
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
  <li><a href="#">Link 3</a></li>
  <li><a href="#">Link 4</a></li>
  <li><a href="#">Link 5</a></li>
</ul>
const firstLink = document.querySelector('a')
const list = firstLink.closest('.list')

console.log(list)
// <ul class="list"> ... </ul>
````  

참고 : nearest는 현재 요소에서 검색을 시작한 다음 문서에 도달 할 때까지 위쪽으로 진행합니다. 발견 된 첫 번째 요소를 반환합니다.  

````
const firstLink = document.querySelector('a')
const firstLinkThroughClosest = firstLink.closest('a')

console.log(firstLinkThroughClosest)
// <a href="#">Link 1</a>
````  

가장 가까운 것은 꽤 새롭다. IE Edge 14 이하에서는 작동하지 않습니다. 그것은 오페라 미니에서도 작동하지 않습니다.  
구형 브라우저를 지원해야하는 경우 polyfill을 사용할 수 있습니다.  

## 옆으로 traversing  

옆으로 traverse 하는 세 가지 방법이 있습니다.  

1. nextElementSibling
2. previousElementSibling
3. Combining parentElement, children, and index.  

### nextElementSibling
nextElementSibling을 사용하여 다음 요소를 선택할 수 있습니다.  

````
const nextElem = Node.nextElementSibling
<ul class="list">
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
  <li><a href="#">Link 3</a></li>
  <li><a href="#">Link 4</a></li>
  <li><a href="#">Link 5</a></li>
</ul>
const firstListItem = document.querySelector('li')
const secondListItem = firstListItem.nextElementSibling

console.log(secondListItem)
// <li><a href="#">Link 2</a></li>
````  

### previousElementSibling
마찬가지로 previousElementSibling을 사용하여 이전 요소를 선택할 수 있습니다.  

````
<ul class="list">
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
  <li><a href="#">Link 3</a></li>
  <li><a href="#">Link 4</a></li>
  <li><a href="#">Link 5</a></li>
</ul>
const secondListItem = document.querySelectorAll('li')[1]
const firstListItem = secondListItem.previousElementSibling

console.log(firstListItem)
// <li><a href="#">Link 1</a></li>
````  

### Combining parentElement, children, and index  

이 메서드를 사용하면 특정 형제를 선택할 수 있습니다. 예제로 어떻게 작동하는지 설명하는 것이 더 쉽습니다.  
이 HTML의 첫 번째 항목에서 네 번째 항목을 선택한다고 가정 해보십시오.  

````
<ul class="list">
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
  <li><a href="#">Link 3</a></li>
  <li><a href="#">Link 4</a></li>
  <li><a href="#">Link 5</a></li>
</ul>
````

이미 첫 번째 항목이 있다고 가정 해 보겠습니다.  

````
const firstItem = document.querySelector('li')
````  
네 번째 항목을 선택하려면 firstItem.parentElement를 사용하여 목록을 가져온 다음  
list.children을 사용하여 HTML Collection을 가져올 수 있습니다.  
HTML 컬렉션을 얻은 후에는 인덱스 3을 사용하여 네 번째 항목을 찾을 수 있습니다 (0부터 시작하는 인덱스를 기억하십시오!).

````
const firstItem = document.querySelector('li')
const list = firstItem.parentElement
const allItems = list.children
const fourthItem = allItems[3]

console.log(fourthItem)
// <li><a href="#">Link 4</a></li>
````  

모든 것을 한 번에 정리:  

````
const firstItem = document.querySelector('li')
const fourthItem = firstITem.parentElement.children[3]

console.log(fourthItem)
// <li><a href="#">Link 4</a></li>
````  

## 연습문제

이 단원에서 배우는 방법으로 DOM 탐색을 연습합니다. 아래 주어진 HTML을 사용하여 다음 작업을 수행하십시오.  
1. Select .characters with document.querySelector
2. Select .humans from .characters
3. Select all humans with querySelectorAll, starting from .humans
4. Select all hobbits with children
5. Select the Merry (the hobbit)
6. Select .enemies from Sauron
7. Select the .characters div from Nazgûl
8. Select Elrond from Glorfindel
9. Select Legolas from Glorfindel
10. Select Arwen from Glorfindel  

````
<div class="characters">
  <ul class="hobbits">
    <li>Frodo Baggins</li>
    <li>Samwise "Sam" Gamgee</li>
    <li>Meriadoc "Merry" Brandybuck</li>
    <li>Peregrin "Pippin" Took</li>
    <li>Bilbo Baggins</li>
  </ul>
  <ul class="humans">
    <li>Gandalf</li>
    <li>Saruman</li>
    <li>Aragorn</li>
    <li>Boromir</li>
    <li>Faramir</li>
  </ul>
  <ul class="elves">
    <li>Legolas</li>
    <li>Glorfindel</li>
    <li>Elrond</li>
    <li>Arwen Evenstar</li>
  </ul>
  <ul class="enemies">
    <li>Sauron</li>
    <li>Nazgûl</li>
  </ul>
</div>
````

## 마무리 

이 레슨에서는 DOM을 가로, 세로, 가로의 세 방향으로 traverse하는 방법을 배웠다. 다음은 배운 방법을 요약하는 간단한 글 머리 기호입니다.  
1. 아래로 traversing
   1. element.querySelector  
   2. element.querySelectorAll  
   3. element.children  
2. 위로 traversing
   1. element.parentElement  
   2. element.closest  
3. 옆으로 traversing
   1. element.nextElementSibling  
   2. element.previousElementSibling  
   3. Combine parentElement, children, and index  

이 강의가 왜 우리가 DOM을 통과하는지, 그리고 어떻게 할 수 있는지를 이해하는 데 도움이되기를 바랍니다.  
이 레슨을 통해 도움이 되었으면 단계별 방식으로 더 많은 JavaScript 레슨, 기본, 중급 및 고급 레슨을 공유하는 JavaScript 학습을 즐기십시오.  

읽어 주셔서 감사합니다. 이 기사가 도움이 되었습니까? 그랬다면 공유하는 것이 좋겠다고 생각합니다. 다른 사람을 도울 수 있습니다. 정말 고마워!


 