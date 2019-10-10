> 원문 출처 : https://www.freecodecamp.org/news/javascript-objects-square-brackets-and-algorithms-e9a2916dc158/

### by Dmitri Grabov

자바스크립트의 가장 강력한 측면 중 하나는 개체의 속성을 동적으로 참조할 수 있다는 것이다. 이 글에서 우리는 이것이 어떻게 작동하는지 그리고 이것이 우리에게 어떤 이점을 줄 수 있는지를 살펴볼 것이다. 우리는 컴퓨터 과학에서 사용되는 데이터 구조 중 일부를 간단히 살펴볼 것이다. 또한 우리는 알고리즘의 성능을 설명하는 데 사용되는 Big O 표기법이라고 불리는 것을 살펴볼 것이다.

## Object intro

우선 자동차를 대표하는 간단한 물체를 만드는 것부터 시작합시다. 각 물체는 속성이라고 불리는 것을 가지고 있다. 속성은 물체에 속하는 변수다. 우리의 자동차 물체는 세 가지 특성을 가질 것이다: `make`, `model`, `color`

어떤 모습일지 보시죠.

```js
const car = {  make: 'Ford',  model: 'Fiesta',  color: 'Red'};
```

우리는 점 표기법을 사용하여 물체의 개별 속성을 언급할 수 있다. 예를 들어, 만약 우리가 우리 차의 색이 무엇인지 알고 싶다면, 우리는 `car.color`와 같은 점 표기법을 사용할 수 있다.

console.log를 사용하여 출력할 수도 있다.

```js
console.log(car.color); //outputs: Red
```

속성을 참조하는 또 다른 방법은 사각형 브라켓 표기법을 사용하는 것이다.

```js
console.log(car['color']); //outputs: Red
```

위의 예에서는 속성 이름을 대괄호 안의 문자열로 사용하여 해당 속성 이름에 해당하는 값을 얻는다.  
사각형 브라켓 표기법의 유용한 점은 변수를 사용하여 동적으로 속성을 얻을 수 있다는 것이다.

즉, 특정 속성 이름을 하드코딩하는 것이 아니라 변수에서 문자열로 지정할 수 있다.

```js
const propertyName = 'color';const console.log(car[propertyName]); //outputs: Red
```


## 사각 브래킷 표기법과 함께 동적 lookup 사용

이것을 이용할 수 있는 예를 보자.  
레스토랑을 운영하면서 메뉴에 있는 품목의 가격을 얻을 수 있기를 원한다고 하자.  
이것을 하는 한 가지 방법은 if/else 문장을 사용하는 것이다.

품목명을 수락하고 가격을 반환하는 기능을 쓰자.


```js
function getPrice(itemName){  if(itemName === 'burger') {    return 10;  } else if(itemName === 'fries') {    return 3;  } else if(itemName === 'coleslaw') {    return 4;  } else if(itemName === 'coke') {    return 2;  } else if(itemName === 'beer') {    return 5;  }}
```

위의 접근방식은 효과가 있지만, 이상적이지 않다.  
우리는 우리 코드에 메뉴를 하드코딩했다. 이제 우리의 메뉴가 바뀌면 우리는 코드를 다시 쓰고 다시 배포해야 할 것이다. 
게다가, 우리는 긴 메뉴를 가질 수 있고 이 코드를 모두 써야 하는 것은 번거로울 것이다.

더 나은 접근법은 우리의 데이터와 논리를 분리하는 것이다.  
그 자료에는 우리의 메뉴가 포함될 것이고 그 논리는 그 메뉴에서 가격을 올릴 것이다.

우리는 메뉴를 키로 알려진 속성 이름이 값에 해당하는 객체로 나타낼 수 있다.이 경우 열쇠는 품목명이고 값은 품목가격이다.


```js
const menu = {  burger: 10,  fries: 3,  coleslaw: 4,  coke: 2,  beer: 5};
```

사각형 브라켓 표기법을 사용하여 다음 두 가지 인수를 수용하는 함수를 만들 수 있다.

- a menu object
- a string with item name

해당 품목의 가격을 반환하십시오.


```js
const menu = {  burger: 10,  fries: 3,  coleslaw: 4,  coke: 2,  beer: 5};
```

```js
function getPrice(itemName, menu){  const itemPrice = menu[itemName];  return itemPrice;}
```

```js
const priceOfBurger = getPrice('burger', menu);console.log(priceOfBurger); // outputs: 10
```

이 접근방식의 단정한 점은 우리의 논리로부터 우리의 데이터를 분리했다는 것이다.  
이 예에서, 데이터는 우리의 코드에 존재하지만, 그것은 데이터베이스나 API에서 쉽게 나올 수 있다. 

그것은 더 이상 품목명을 품목가격으로 바꾸는 우리의 룩업 논리와 밀접하게 연관되어 있지 않다.


## Datastructures and algorithms

컴퓨터 과학 용어의 지도는 각 키가 해당 값에 매핑되는 키/값 쌍의 집합인 데이터 구조다. 우리는 그것을 특정한 키에 해당하는 값을 보기 위해 사용할 수 있다. 이것이 앞의 예에서 우리가 하고 있는 일이다. 우리는 품목 이름인 키를 가지고 있고 우리는 우리의 메뉴 오브젝트를 사용하여 그 품목의 상응하는 가격을 찾아볼 수 있다. 우리는 지도 데이터 구조를 구현하기 위해 어떤 물체를 사용하고 있다.

왜 우리가 지도를 사용하기를 원하는지 예를 보자. 서점을 운영하며 책 목록을 가지고 있다고 하자. 각 책에는 13자리 숫자인 국제표준서적번호(ISBN)라는 독특한 들여쓰기가 있다. 우리는 책을 배열로 저장하고 ISBN을 이용하여 찾을 수 있기를 원한다.

이렇게 하는 한 가지 방법은 배열을 순환시켜 각 책의 ISBN 값을 확인하고 반환하는 것과 일치하는지 확인하는 것이다.

```js
const books = [{  isbn: '978-0099540946',  author: 'Mikhail Bulgakov',  title: 'Master and Margarita'}, {  isbn: '978-0596517748',  author: 'Douglas Crockford',  title: 'JavaScript: The Good Parts'}, {  isbn: '978-1593275846',  author: 'Marijn Haverbeke',  title: 'Eloquent JavaScript'}];
```

```js
function getBookByIsbn(isbn, books){  for(let i = 0; i < books.length; i++){    if(books[i].isbn === isbn) {      return books[i];    }  }}
```

```js
const myBook = getBookByIsbn('978-1593275846', books);
```

이 예에서는 책이 세 권밖에 없기 때문에 잘 된다. 하지만 우리가 아마존이라면 수백만 권 이상의 책을 반복하는 것은 매우 느리고 계산적으로 비쌀 수 있다.

컴퓨터 과학에서 Big O 표기법은 알고리즘의 성능을 설명하는 데 사용된다. 예를 들어, n이 우리 컬렉션의 책 수라면, 최악의 경우 시나리오(우리가 찾는 책은 목록의 마지막이다)에서 책을 찾는 데 반복을 사용하는 비용은 O(n)가 될 것이다. 그것은 우리가 수집한 책들의 수가 두 배로 증가한다면, 반복을 사용하여 책을 찾는 비용도 두 배가 될 것이라는 것을 의미한다.

다른 데이터 구조를 사용하여 알고리즘을 보다 효율적으로 만들 수 있는 방법을 살펴봅시다.

논의된 바와 같이, 지도가 키에 해당하는 값을 찾는 데 사용될 수 있다. 우리는 객체를 이용하여 배열이 아닌 지도로서 데이터를 구조할 수 있다.

키는 ISBN이고 값은 해당 도서 객체가 된다.

```js
const books = {  '978-0099540946':{    isbn: '978-0099540946',    author: 'Mikhail Bulgakov',    title: 'Master and Margarita'  },  '978-0596517748': {    isbn: '978-0596517748',    author: 'Douglas Crockford',    title: 'JavaScript: The Good Parts'  },  '978-1593275846': {    isbn: '978-1593275846',    author: 'Marijn Haverbeke',    title: 'Eloquent JavaScript'  }};
```

```js
function getBookByIsbn(isbn, books){  return books[isbn];}
```

```js
const myBook = getBookByIsbn('978-1593275846', books);
```

우리는 이제 반복을 사용하는 대신에 ISBN의 간단한 지도 조회를 이용하여 우리의 가치를 얻을 수 있다. 우리는 더 이상 각 개체의 ISBN 값을 확인할 필요가 없다. 우리는 키를 사용하여 지도에서 직접 값을 얻는다.

성능 면에서, 지도 조회는 반복에 비해 엄청난 향상을 제공할 것이다. 지도조회는 계산에 있어 비용이 일정하기 때문이다. 이는 Big O 표기법을 O(1)로 표기하여 쓸 수 있다. 300만 권의 책이 있어도 상관없고, 우리는 ISBN 키를 사용하여 지도를 조사함으로써 우리가 원하는 책을 빨리 얻을 수 있다.