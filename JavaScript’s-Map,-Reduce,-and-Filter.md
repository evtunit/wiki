엔지니어로서 우리는 거의 매일 숫자, 문자열, 부울 및 객체를 포함하는 배열을 만들고 조작합니다.  
우리는 숫자를 처리하고, 객체를 수집하고, 문자열을 분리하고, 검색하고, 정렬하는 등의 작업을 수행합니다.  
그래서 순회하는 것이 바람직한 방법은 무엇입니까?  
수년 동안 인덱스를 기준으로 요소를 순회하는것을 보장하는 for 루프를 신뢰해왔습니다.

2011년에 JavaScript는 요소를 변환하거나 누적 값을 찾거나 조건을 기반으로 하위 집합을 만들 때 강력한 대안으로  
map, reduce 및 filter를 도입했습니다.  
이러한 방법을 통해 개발자는 복잡성을 줄이고 부작용없이 작업하며 코드를보다 쉽게 ​​읽을 수 있습니다.  

이 글에서는 배열의 map, reduce 및 filter 메소드의 유용성에 대해 다룰 것입니다.  
각 메소드의 유스케이스, 코드 샘플, 동작 및 매개 변수가 표시됩니다.  

![](https://svbtleusercontent.com/ozxnzdoibupnua_small.jpg)

## Drawbacks of Looping (반복의 단점)
코드베이스에서 1,000 줄의 자바 스크립트를 통해 버그 수정 및 작업을하고 있다고 가정 해 보겠습니다.  
for 루프를 발견했습니다. 정확히 무엇을하는지 확신 할 수는 없지만 자세히 살펴 봅니다.  
````
for(var i = 0; i < array.length; i++) {
    if(array.indexOf(array[i]) === i) {
        models.push(array[i]);
    }
}
````
우리는 그것이 배열을 가로 지르고 중복되지 않는 요소를 새로운 배열에 삽입하는 것을 볼 수 있습니다.  
그러나 이것을 알아 내기 위해 우리는 이 다섯 가지를 수집해야했습니다.  

| code piece | meaning |
|------------|---------|
| var i = 0  | 배열의 왼쪽에서 시작한다.  |
| i < array.length  | 배열의 오른쪽에서 끝난다.  |
| i++  | 1 씩 증가하다  |
| array.indexOf(array[i]) === i  | 값이 배열의 첫 번째 인스턴스이면 인덱스와 일치합니다. 이것은 그것이 중복인지를 확인하고 있음을 의미합니다.  |
| models.push(…) | 모델은 목록이어야합니다. 그러나 어떤 데이터가 들어 있나? 데이터 유형은 무엇입니까? 파일에서 "모델"을 검색해야합니다. 반복.|

우리는 무슨 일이 일어나고 있는지를 판단하기 위해 5가지 정보를 확인해야했습니다. 그리고 이것은 하나의 for 루프입니다!

## 기능적 접근법
이 같은 효과는 JavaScript의 기본 제공 filter() 메서드를 사용하여 작성할 수 있습니다.  
````
var uniqueProducts = array.filter(function(elem, i, array) {
        return array.indexOf(elem) === i;
    }
);
````
심플하고 우아하다.  
필터는 코드 동작을 전달하므로 나는 필터가 정확히 무엇을 하는지 알 수 있습니다.  
위의 루핑 방식과 비교 :
* #1, #2, #3 검사는 filter ()가 자동으로 수행하기 때문에 불필요합니다.
* #4는 동일하지만 추가 if (...) 블록이 없습니다.  
* 큰 장애물은 # 5이었다. 어떤 모델이 있는지 찾아 내기 위해서는 코드베이스를 검색해야했습니다. 
  이미 데이터가 있습니까? 특정 데이터 유형을 대상으로 했습니까? map, reduce 및 filter는 부작용이라고하는 콜백 외부의 코드에 의존하지 않으므로이 문제를 해결합니다.  

요약하면 map, reduce 및 filter는 부작용없이 코드를 덜 복잡하게 만듭니다. 

각각에 대해 보겠습니다.

## map()
다음과 같은 경우에 사용합니다. 배열의 모든 요소를 ​​다른 값 집합으로 변환 / 매핑하려고 합니다.  
예 : 화씨 온도를 섭씨로 변환
````
var fahrenheit = [0, 32, 45, 50, 75, 80, 99, 120];

var celcius = fahrenheit.map(function(elem) {
    return Math.round((elem - 32) * 5 / 9);
}); 

// ES6
// fahrenheit.map(elem => Math.round((elem - 32) * 5 / 9));

celcius //  [-18, 0, 7, 10, 24, 27, 37, 49]
````
기능 : 배열을 왼쪽에서 오른쪽으로 가로 지르며 매개 변수가 있는 각 요소에 대해 콜백 함수를 호출합니다.  
각 콜백에 대해 반환 된 값은 새 배열의 요소가됩니다. 모든 요소가 통과 된 후 map()은 모든 변환 된 요소가 있는 새 배열을 반환합니다.  

매개변수들 : 
````
array.map(function(elem, index, array) {
      ...
}, thisArg);
````
| param | meaning |
|------------|---------|
| elem  | 요소 값  |
| index  | 각 순회의 인덱스는 왼쪽에서 오른쪽으로 이동  |
| array | 메소드를 호출 한 원래의 배열  |
| thisArg  | (선택 사항) 콜백에서 this로 참조 될 객체  |

## filter()
다음과 같은 경우에 사용합니다. 조건에 따라 원치 않는 요소를 제거하려고합니다.  
예 : 배열에서 중복 요소를 제거합니다.  
````
var uniqueArray = array.filter(function(elem, index, array) {
        return array.indexOf(elem) === index;
    }
);

// ES6
// array.filter((elem, index, arr) => arr.indexOf(elem) === index);
````
기능 : map()처럼 배열을 왼쪽에서 오른쪽으로 가로 질러 각 요소에 콜백 함수를 호출합니다.  
반환 된 값은 요소를 보관할지 또는 폐기할지 여부를 식별하는 부울이어야 합니다.  
모든 요소가 순회 된 후 filter()는 true를 반환 한 모든 요소를 ​​가진 새로운 배열을 반환합니다.  
map()과 같은 매개 변수를 가집니다.  

매개변수들 : 
````
array.filter(function(elem, index, array) {
      ...
}, thisArg);
````
| param | meaning |
|------------|---------|
| elem  | 요소 값  |
| index  | 각 순회의 인덱스는 왼쪽에서 오른쪽으로 이동  |
| array | 메소드를 호출 한 원래의 배열  |
| thisArg  | (선택 사항) 콜백에서 this로 참조 될 객체  |

## reduce()
다음과 같은 경우에 사용하십시오. 배열의 요소를 기반으로 누적 또는 연결된 값을 찾고 싶습니다.
예 : 2014년에 궤도 로켓 발사.  
````
var rockets = [
    { country:'Russia', launches:32 },
    { country:'US', launches:23 },
    { country:'China', launches:16 },
    { country:'Europe(ESA)', launches:7 },
    { country:'India', launches:4 },
    { country:'Japan', launches:3 }
];

var sum = rockets.reduce(function(prevVal, elem) {
    return prevVal + elem.launches;
}, 0);

// ES6
// rockets.reduce((prevVal, elem) => prevVal + elem.launches, 0); 

sum // 85
````
기능 : map()처럼 배열을 왼쪽에서 오른쪽으로 가로 질러 각 요소에 콜백 함수를 호출합니다.  
반환 된 값은 콜백에서 콜백으로 전달 된 누적 값입니다. 모든 요소가 탐색 된 후 reduce()는 누적 값을 반환합니다.  

매개변수들 : 
````
array.reduce(function(prevVal, elem, index, array) {
      ...
}, initialValue);
````
| param | meaning |
|------------|---------|
| prevValue  | 각 콜백을 통해 반환 된 누적 값  |
| elem | 요소 값 |
| index  | 각 순회의 인덱스는 왼쪽에서 오른쪽으로 이동  |
| array | 메소드를 호출 한 원래의 배열  |
| initialValue  | (선택 사항) 첫 번째 (가장 왼쪽) 콜백에서 첫 번째 인수로 사용되는 객체입니다.  |
 

## 추가기능
* Each는 Array의 프로토 타입 객체에있는 메소드입니다.  
* 모든 콜백에서 배열 매개 변수의 요소를 변경하면 나머지 모든 콜백에서 지속되지만 반환 된 배열에는 영향을 미치지 않습니다.  
* 콜백 함수는 인덱스에 대해 정의되지 않은 모든 값을 포함하여 호출되지만 삭제되거나 절대 값이 할당되지는 않습니다.  

대규모 배열 (예 : 1,000 개가 넘는 요소)을 사용하거나 조건이 충족 될 경우 순회를 중단해야 할 때 루프가 여전히 명확한 위치를 차지한다는 점은  
주목할 가치가 있습니다.  

이 글에서는 map(), reduce() 및 filter()가 코드 동작을보다 쉽게 ​​전달하고 부작용을 추적 할 필요성을 줄이는 방법에 대해 설명했습니다.  
또한 각 방법의 사용 사례, 코드 샘플, 동작 및 매개 변수에 대해서도 설명했습니다. 이것이 당신에게 유용했기를 바랍니다.  
