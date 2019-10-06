
# Algorithms in plain English: time complexity and Big-O notation

번역 : [https://www.freecodecamp.org/news/time-is-complex-but-priceless-f0abd015063c/](https://www.freecodecamp.org/news/time-is-complex-but-priceless-f0abd015063c/)

모든 훌륭한 개발자는 시간에 중요시 합니다. 그들은 사용자에게 더 많은 것을 제공하기를 원하므로 사용자의 즐거움에 관해서 무엇이든 할 수 있습니다. 시간 복잡성을 최소화하여 이를 수행합니다.

당신이 시간복잡도를 이해하기 전에 어디서 대게 적용이 되는지 이해해야 한다. : 알고리즘 디자인 에서 적용이 된다.

### So what’s an algorithm, anyway?

그래서 알고리즘이 무엇이냐 간단하게 알고리즘은 절차를 포함하고 있는 단계이다. 이것은 한 목표를 달성 또는 결과물을 만들어내기 위한 단계 입니다. 
다음 할머니의 케잌을 만드는 레시피를 살펴 봅시다. 잠깐, 여기서 몇개의 알고리즘이 사용되었는지 확인해 보자.

```javascript
function BakeCake(flavor, icing){
"
 1. Heat Oven to 350 F
 2. Mix flour, baking powder, salt
 3. Beat butter and sugar until fluffy
 4. Add eggs.
 5. Mix in flour, baking powder, salt
 6. Add milk and " + flavor + "
 7. Mix further
 8. Put in pan
 9. Bake for 30 minutes
10." + if(icing === true) return 'add icing' + "
10. Stuff your face
"
}

BakeCake('vanilla', true) => deliciousness
```

알고리즘은 시간복잡도를 시험하기에 유용합니다. 왜냐하면 알고리즘들은 모든 모양과 크기를 나타낼수 있기 때문이다.

같은 방식으로 파이를 조각낼수 있는 방법이 100가지가 있다고 했을때, 당신은 다른 여러가지 알고리즘에서 한가지로 이 문제를 해결 할 수 있습니다.  몇몇 해결책들은 효율적이고 시간이 덜 들면서 다른것보다 더 적은 공간을 요구합니다. 

그래서 주된 질문은 어떻게 우리가 해결책이 가장 효율적인지 분석을 하는가에 대한 것이다.

수학이 해결해줄것이다! 프로그래밍의 시간 복잡도 분석은 주어진 입력 수 (n)를 가진 알고리즘이 작업을 완료하는 데 걸리는 시간을 분석하는 매우 단순화 된 수학적 방법입니다. 이는 대게 Big-O 표기법을 사용해서 정의한다. 

### What’s Big O notation, you ask?

Big-O 표기법은 알고리즘의 전체 단계를 대수용어로 바꾸는 방법입니다. 그 후에 문제의 전체 복잡성에 큰 영향을 미치지 않는 하위 상수 및 계수를 제외합니다.

수학자들은 아마도 내 "전반적인 영향" 가정에 대해 약간의 비난을 할 것이지만 개발자가 시간을 절약하기 위해 다음과 같은 방식으로 일을 단순화하는 것이 더 쉽습니다.

```
Regular       Big-O

2             O(1)   --> It's just a constant number

2n + 10       O(n)   --> n has the largest effect

5n^2          O(n^2) --> n^2 has the largest effect
```

간단히 말해서,이 모든 예는 다음과 같습니다. 우리는 표현이 반환 할 가치에 가장 큰 영향을 미칠 수있는 표현의 요소만을 봅니다. (상수가 클수록 n이 작아 지지만, 지금은 걱정하지 않아도됩니다.)

다음은 간단한 정의로 인한 일반적인 시간 복잡도입니다. 보다 자세한 정의는 Wikipedia를 확인하십시오.

- O (1) — 상수 시간 : 크기 n의 입력이 주어지면 알고리즘이 작업을 수행하는 데 단 한 단계 만 걸립니다.
- O (log n) — 로그 시간 : 크기 n의 입력이 주어지면 작업을 수행하는 데 걸리는 단계 수가 각 단계마다 일부 요인으로 줄어 듭니다.
- O (n) — 선형 시간 : 크기 n의 입력이 주어지면 필요한 단계 수는 직접 관련됩니다 (1-1).
- O (n²) — 2 차 시간 : 크기 n의 입력이 주어지면 작업을 수행하는 데 걸리는 단계 수는 n의 제곱입니다.
- O (C ^ n) — 지수 시간 : 크기 n의 입력이 주어지면 작업을 수행하는 데 걸리는 단계의 수는 n의 제곱 (상당히 큰 수)입니다.

이 지식을 바탕으로 이러한 각 복잡성에 수반되는 단계 수를 확인하십시오.

```javascript
let n = 16;

O (1) = 1 step "(awesome!)"

O (log n) = 4 steps  "(awesome!)" -- assumed base 2

O (n) = 16 steps "(pretty good!)"

O(n^2) = 256 steps "(uhh..we can work with this?)"

O(2^n) = 65,536 steps "(...)"
```

보시다시피 알고리즘의 복잡성에 따라 상황이 훨씬 복잡해집니다. 운 좋게도 컴퓨터는 여전히 매우 큰 복잡성을 비교적 빠르게 처리 할 수있을만큼 강력합니다.

그렇다면 Big-O 표기법으로 코드를 분석하는 방법은 무엇입니까?

다음은 이러한 지식을 야생에서 접하거나 직접 코딩 할 수있는 알고리즘에 적용하는 방법에 대한 빠르고 간단한 예입니다.

아래의 데이터 구조를 예로 들어 보겠습니다.

```javascript
var friends = {
 'Mark' : true,
 'Amy' : true,
 'Carl' : false,
 'Ray' :  true,
'Laura' : false,
}
var sortedAges = [22, 24, 27, 29, 31]
```

#### **O(1) — Constant Time**

키 (객체) 또는 인덱스 (배열)가 항상 한 단계 씩 걸리므로 일정한 시간이 걸리면 값을 찾습니다.

```javascript
//If I know the persons name, I only have to take one step to check:

function isFriend(name){ //similar to knowing the index in an Array 
  return friends[name]; 
};

isFriend('Mark') // returns True and only took one step

function add(num1,num2){ // I have two numbers, takes one step to return the value
 return num1 + num2
}
```

#### **O(log n) — Logarithmic Time**

배열의 어느 쪽에서 항목을 찾아야하는지 알면 나머지 절반을 잘라서 시간을 절약 할 수 있습니다.

```js
//You decrease the amount of work you have to do with each step

function thisOld(num, array){
  var midPoint = Math.floor( array.length /2 );
  if( array[midPoint] === num) return true;
  if( array[midPoint] < num ) --> only look at second half of the array
  if( array[midpoint] > num ) --> only look at first half of the array
  //recursively repeat until you arrive at your solution
  
}

thisOld(29, sortedAges) // returns true 

//Notes
 //There are a bunch of other checks that should go into this example for it to be truly functional, but not necessary for this explanation.
 //This solution works because our Array is sorted
 //Recursive solutions are often logarithmic
 //We'll get into recursion in another post!
```

#### **O(n) — Linear Time**

작업을 수행하려면 배열 또는 목록의 모든 항목을 살펴 봐야합니다. 단일 for 루프는 거의 항상 선형 시간입니다. 또한 indexOf와 같은 배열 메소드도 선형 시간입니다. 루핑 과정에서 추상화되었습니다.

```js
//The number of steps you take is directly correlated to the your input size

function addAges(array){
  var sum = 0;
  for (let i=0 ; i < array.length; i++){  //has to go through each value
    sum += array[i]
  }
 return sum;
}

addAges(sortedAges) //133
```

#### **O(n²) — Quadratic Time**

중첩 된 for 루프는 다른 선형 연산 (또는 n * n = n²) 내에서 선형 연산을 실행하기 때문에 2 차 시간입니다.

```js
//The number of steps you take is your input size squared

function addedAges(array){
  var addedAge = [];
    for (let i=0 ; i < array.length; i++){ //has to go through each value
      for(let j=i+1 ; j < array.length ; j++){ //and go through them again
        addedAge.push(array[i] + array[j]);
      }
    }
  return addedAge;
}

addedAges(sortedAges); //[ 46, 49, 51, 53, 51, 53, 55, 56, 58, 60 ]

//Notes
 //Nested for loops. If one for loop is linear time (n)
 //Then two nested for loops are (n * n) or (n^2) Quadratic!
```

#### **O(2^n) — Exponential Time**

지수 시간은 일반적으로 그 정도를 모르고 가능한 모든 조합 또는 순열을 시도해야하는 상황을위한 것입니다.

```js
//The number of steps it takes to accomplish a task is a constant to the n power

//Thought example
 //Trying to find every combination of letters for a password of length n
```

빠르게 실행되어야하는 코드를 작성할 때마다 시간 복잡도 분석을 수행해야합니다.

문제를 해결하기위한 다양한 경로가있는 경우 가장 먼저 작동하는 솔루션을 만드는 것이 현명합니다. 그러나 장기적으로는 가능한 한 빠르고 효율적으로 실행되는 솔루션이 필요합니다.

문제 해결 과정에 도움이되도록 다음과 같이 간단한 질문을합니다.

1. 이 문제가 해결되었는가? 예 => 
2. 이 작업에 시간을 갖는가? 예 => 3번으로, 아니오 => 나중에 다시 돌아와서 6 단계로 가십시오.
3. 모든 엣지 케이스에 커버했습니까? 예 => 
4. 내 복잡성이 가능한 한 낮습니까? 아니오 => 새로운 방법 또는 수정으로 재 작성하십시요 -> 1번 단계로 다시 가십시오. , 예 => 5번으로 가십시오.
5. 코드가 D.R.Y 합니까? 예 => 
6. 만족한가! 아니오 => 다시 D.R.Y로 짜고나서 만족해라.

문제를 해결하려고 할 때마다 시간 복잡성을 분석하십시오. 로그 실행에서 더 나은 개발자가 될 것입니다. 팀원과 사용자가 당신을 좋아할 것입니다.

다시 말하지만 알고리즘이든 프로그래밍 방식이든 프로그래머가 직면하게 될 대부분의 문제는 수백 가지의 방법으로 해결할 수는 없지만 수십 가지가 될 것입니다. 문제 해결 방법이 다를 수 있지만 여전히 문제를 해결합니다.

집합을 사용할지 또는 그래프를 사용하여 데이터를 저장할지 여부를 결정할 수 있습니다. 팀 프로젝트에 Angular, React 또는 Backbone을 사용할지 여부를 결정할 수 있습니다. 이러한 솔루션은 모두 동일한 문제를 다른 방식으로 해결합니다.

이를 감안할 때 이러한 문제에 대한“올바른”또는“최상의”답변이 있다고 말하기는 어렵습니다. 그러나 주어진 문제에 대해“더 나은”또는“더 나쁜”답변이 있다고 말할 수 있습니다.

이전 예제 중 하나를 사용하면 팀의 절반이 경험이있는 팀 프로젝트에 React를 사용하는 것이 더 좋으므로 시작하고 실행하는 데 시간이 덜 걸립니다.

더 나은 솔루션을 설명하는 능력은 일반적으로 시간 복잡성 분석과 유사합니다.

간단히 말해서 문제를 해결하려면 잘 해결하십시오. Big-O를 사용하여 방법을 파악하십시오.

최종 요약은 다음과 같습니다.

- O (1) — 상수 시간 : 알고리즘이 작업을 수행하는 데 단 한 단계 만 걸립니다.
- O (log n) — 로그 시간 : 작업을 수행하는 데 걸리는 단계 수는 각 단계마다 일부 요인에 의해 줄어 듭니다.
- O (n) — 선형 시간 : 필요한 단계 수는 직접 관련되어 있습니다 (1-1).
- O (n²) — 2 차 시간 : 작업을 수행하는 데 걸리는 단계 수는 n의 제곱입니다.
- O (C ^ n) — 지수 : 작업을 수행하는 데 걸리는 단계 수는 n 제곱 (상수)에 대한 상수입니다.