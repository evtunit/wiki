

# JS: 알고리즘 인터뷰

> 원문: [JS: Interview Algorithm](http://www.thatjsdude.com/interview/js1.html)

## 1. 소수 검사

**질문 :** 소수를 어떻게 확인 할 것인가?

**답 :** 소수는 1과 1로만 나눌 수 있다. 따라서 while 루프를 실행하고 1씩 증가시킨다. (코드 예제를 참고. 이해가 안되면 자바 스크립트 기본 사항을 배워야한다.)

```js
function isPrime(n){
	var divisor = 2;
	
	while (n > divisor){
		if(n % divisor == 0) {
			return  false;
		}  else 
			divisor++; 
	}  
	return  true;
} 

> isPrime(137); // true 
> isPrime(237); // false
```

**면접관 :** 더 나은 방법은 없나?

**답변:** 나눗수는 한 번에 1 씩 증가한다. 3 이후에 2를 증가시킬 수 있다. 숫자가 짝수로 나눌 수 있으면 2로 나눌 수 있다.

**추가 :** 제수를 숫자까지 늘릴 필요가없는 경우. 훨씬 일찍 멈출 수 있다.

## 2. 소인수 (인수 중에 소수인 수)

**질문 :** 숫자의 모든 소인수어떻게 찾을 수 있나?

**답 :** while 루프를 실행하자. 2로 나누기 시작하고 나눌 수없는 경우 ur가 나올 때까지 나눗수를 늘린다.

```js
function primeFactors(n){
	var factors = [],
		divisor = 2;
		
	while(n>2){
		if(n % divisor == 0){
			factors.push(divisor);
			n= n/ divisor;
		} else {
			divisor++;
		}
	}
	return factors;
} 

> primeFactors(69); // [3, 23]
```

**면접관 :** 런타임 복잡성은 무엇인가? 이것을 더 좋게 만들 수 있나?

**답변:** 이것은 O (n)입니다. 나눗수가 3에서 부터 2로 나눠지는 나눗수로 증가할 수 있다. 숫자가 짝수로 나눌 수 있으면 2로 나눌 수 있기 때문에 짝수로 나눌 필요가 없습니다. 게다가, 그 가치의 절반보다 큰 인자를 가지지 않을 것이다. 복잡한 개념을 원한다면 복잡한 개념으로 사용하라

## 3. 피보나치

**질문 :** n 번째 피보나치 숫자는 어떻게 얻는가?

**답 :** 배열을 만들고 반복부터 시작한다.

피보나치 수열은 신입에게 가장 인기있는 인터뷰 질문 중 하나이다. 따라서 알아야한다.

### 시도 1

```js

function fibonacci(n){
  var fibo = [0, 1];
  
  if (n <= 2) return 1;

  for (var i = 2; i <=n; i++ ){
   fibo[i] = fibo[i-1] + fibo[i-2];
  }

 return fibo[n];
} 

> fibonacci(12);
  = 144       
```

**면접관  :** 런 타임 복잡도는 어떻게 되나?

**답변:** O (n)

**면접관  :** 재귀 적으로 만들 수 있나?

### 시도 2

```js

function fibonacci(n){
  if(n<=1)
    return n;
  else
    return fibonacci(n-1) + fibonacci (n-2);  
}

> fibonacci(12);
  = 144
         
```

**면접관  :** 런타임 복잡도는 어떻게 되나?

**답변:** O (2 n ). [복잡성에 대한 세부 사항](https://stackoverflow.com/a/360773/1535443)

## 4. 최대 공약수

**질문 :** 두 숫자의 최대 공약수를 어떻게 찾을 수 있나?

```js

function greatestCommonDivisor(a, b){
  var divisor = 2, 
      greatestDivisor = 1;

  //if u pass a -ve number this will not work. fix it dude!!
  if (a < 2 || b < 2)
     return 1;
  
  while(a >= divisor && b >= divisor){
   if(a %divisor == 0 && b% divisor ==0){
      greatestDivisor = divisor;      
    }
   divisor++;
  }
  return greatestDivisor;
}

> greatestCommonDivisor(14, 21);
  =7 
> greatestCommonDivisor(69, 169);
  = 1

        
```

### 멋진 알고리즘

**미안하다.** 설명 할 수 없다. 나 자신도 80% 정도 이해하지 못하기 때문에... 알고리즘 분석 강사가 이것에 대해 말해서 그냥 쓴거다.

```js

function greatestCommonDivisor(a, b){
   if(b == 0)
     return a;
   else 
     return greatestCommonDivisor(b, a%b);
}
        
```

**참고 :** 뇌를 사용하여 이해해라.

## 5. 중복 제거

**질문 :** 배열에서 중복 요소를 어떻게 제거 할 것인가?

**답변 :** 반복 루프를 시작하고 객체 / 관련 배열을 유지합니다. 처음으로 요소를 찾으면 그 값을 true로 한다.(요소가 한 번 추가되었음을 려준다). 존재하는 객체에서 요소를 찾으면 반환 배열에 삽입하지 않는다.

```js

function removeDuplicate(arr){
  var exists ={},
      outArr = [], 
      elm;

  for(var i =0; i<arr.length; i++){
    elm = arr[i];
    if(!exists[elm]){
      outArr.push(elm);
      exists[elm] = true;
   }
  }
  return outArr;
}

> removeDuplicate([1,3,3,3,1,5,6,7,8,1]);
  = [1, 3, 5, 6, 7, 8]
        
```

## 6. 정렬 된 두 배열을 병합

**질문 :** 두 개의 정렬 된 배열을 어떻게 병합 할 것인가?

**답 :** 각 배열에 대한 포인터를 유지(코드를 읽고 이것에 주의하라)

```js

function mergeSortedArray(a, b){
  var merged = [], 
      aElm = a[0],
      bElm = b[0],
      i = 1,
      j = 1;
  
  if(a.length ==0)
    return b;
  if(b.length ==0)
    return a;
  /* 
  if aElm or bElm exists we will insert to merged array
  (will go inside while loop)
   to insert: aElm exists and bElm doesn't exists
             or both exists and aElm < bElm
    this is the critical part of the example            
  */
  while(aElm || bElm){
   if((aElm && !bElm) || aElm < bElm){
     merged.push(aElm);
     aElm = a[i++];
   }   
   else {
     merged.push(bElm);
     bElm = b[j++];
   }
  }
  return merged;
}

> mergeSortedArray([2,5,6,9], [1,2,3,29]);
 = [1, 2, 2, 3, 5, 6, 9, 29]
        
```

## 7. 임시 변수 없는 숫자 교체하기

**질문 :** 임시 변수를 사용하지 않고 두 숫자를 어떻게 바꿀것 인가?

**답 :** 생각하는 것 처럼 시간을 끌어라. 답은 알고 있지만 생각하는 것처럼 행동하고 답변에 시간을 내라.

```js

function swapNumb(a, b){
  console.log('before swap: ','a: ', a, 'b: ', b);
  b = b - a;
  a = a + b;
  b = a - b;
  console.log('after swap: ','a: ', a, 'b: ', b);  
}

> swapNumb(2, 3);
   = before swap:  a:  2 b:  3
   = after swap:  a:  3 b:  2 
        
```

**비트 조작** : 미안해. 설명 할 수 없다. Kinjal Dave는이 를 이해하기위한 [논리적 연결](https://en.wikipedia.org/wiki/Logical_conjunction) 을 제안 했다.

```js

function swapNumb(a, b){
  console.log("a: " + a + " and b: " + b);
  a = a ^ b;
  b = a ^ b;
  a = a ^ b;
  console.log("a: " + a + " and b: " + b);
}

> swapNumb(2, 3);
  = a: 2 and b: 3
  = a: 3 and b: 2
        
```

## 8. 문자열 뒤집기

**질문 :** 문자열을 어떻게 뒤집을 것인가?

**답 :** 문자열을 반복하고 문자를 새 문자열로 연결하면 된다.

### 시도 1

```js

function reverse(str){
  var rtnStr = '';
  for(var i = str.length-1; i>=0;i--){
    rtnStr += str[i];
  }
  return rtnStr;
}

> reverse('you are a nice dude');
  = "edud ecin a era uoy"
        
```

**면접관  :** 최신 브라우저에서는 저러한 방식이 잘 동작하지만 IE8과 같은 오래된 브라우저에서는 느리다. 다른 방법이 있는가?

**답 : ** 물론이다. 배열을 사용해 검사를 추가 할 수 있다. 문자열이 null이거나 문자열이 아닌 경우 실패한다. 타입 체크도 한다. 이 배열을 사용하는 것은 일부 서버 측 언어에서 문자열 버퍼를 사용하는 것과 같다.

### 시도 2

```js

function reverse(str){
  var rtnStr = [];
  if(!str || typeof str != 'string' || str.length < 2 ) return str;
  
  for(var i = str.length-1; i>=0;i--){
    rtnStr.push(str[i]);
  }
  return rtnStr.join('');
}
        
```

**면접관  :** 런타임 복잡도는 어떻게 되나?

**답변:** O (n)

**면접관  :** 더 개선이 가능한가?

**답변:** 색인의 절반을 반복 하면 조금 절약 할 수 있을 것이다. 

### 3 번 시도

```js

function reverse(str) {
  str = str.split('');
  var len = str.length,
      halfIndex = Math.floor(len / 2) - 1,
      revStr;
  for (var i = 0; i <= halfIndex; i++) {
    revStr = str[len - i - 1];
    str[len - i - 1] = str[i];
    str[i] = revStr;
  }
  return str.join('');
}
        
```

**면접관  :** 이 것은 효과가 있지만 재귀 적으론 가능한가?

**답변: **  물론이다.

### 4 번 시도

```js

function reverse (str) {
    if (str === "") {
        return "";
    } else {
        return reverse(str.substr(1)) + str.charAt(0);
    }
}
        
```

### 5 번 시도

**면접관  :** 좀 더 깔끔하게 할 수 있나?

**답변:** 예.

```js

function reverse(str){
  if(!str || str.length <2) return str;
  
  return str.split('').reverse().join('');
}
        
```

### 6 번 시도

**질문 :** 문자열 확장으로 리버스 함수를 만들 수 있나?

**답변 :** 이 함수를 String.prototype에 추가해야하고 str을 매개 변수로 사용한다.

```js

String.prototype.reverse = function (){
  if(!this || this.length <2) return this;
  
  return this.split('').reverse().join('');
}

> 'abc'.reverse();
  = 'cba'
        
```

## 9. 단어 뒤집기

**질문 :** 문장에서 단어를 어떻게  뒤집을 것인가?

**답 :** 공백이 여러 개 있나 확인한다.

```js

//have a tailing white space
//fix this later
//now i m sleepy
function reverseWords(str){
 var rev = [], 
     wordLen = 0;
 for(var i = str.length-1; i>=0; i--){
   if(str[i]==' ' || i==0){
     rev.push(str.substr(i,wordLen+1));
     wordLen = 0;
   }
   else
     wordLen++;
 }
 return rev.join(' ');
}
        
```

**내장 메소드를 이용한 빠른 솔루션 :**

```js

function reverseWords(str){
  return str.split(' ').reverse();
}
        
```

## 10. 위치 뒤집기

**질문 :** "I am the good boy""와 같은 문장이 있다면. "I ma eht doog yob"을 어떻게 만들 수 있나? 단어는 제자리에 있지만 뒤집은 상태이다.

**답 :** 이렇게 하려면 문자열 반전과 단어 반전을 모두 수행해야 한다.

```js

function reverseInPlace(str){
  return str.split(' ').reverse().join(' ').split('').reverse().join('');
}

> reverseInPlace('I am the good boy');
 = "I ma eht doog yob"
        
```

**면접관  :** 좋다. reverse() 메서드를 사용하지 않고 할 수 있나?

**답변:** 뭐라구요?

```js

//sum two methods.
//you can simply split words by ' '
//and for each words, call reverse function
//put reverse in a separate function


//if u cant do this, 
//have a glass of water, and sleep
        
```

## 11. 첫 번째 비 반복 문자

**질문 :** 문자열에서 첫 번째 비 반복 문자를 어떻게 찾을 수 있나?

**답변 :** 조건이 좀 더 필요하다.

**설명 :** 대소문자를 구분합니까?

**면접관 :** 면접관은 거절 할 수 있다.

**답변 :** 매우 긴 문자열인가? 아니면 몇 백줄인가?

**면접관  :** 왜 중요한가?

**답변 :**예를 들어, 만약 그것이 매우 긴 문자열이라면 백만자를 말하고, 26개의 영어 문자가 반복되고 있는지 확인하고 싶다. 전체 문자열을 순환하는 것 외에 모든 문자가 200자마다 중복되는지 여부를 확인할 수 있다. 이렇게 하면 계산 시간이 절약된다.

**면접관  :** 문장은 "the quick brown fox jumps then quickly blow air" 이거다.

```js

function firstNonRepeatChar(str){
  var len = str.length,
      char, 
      charCount = {};
  for(var i =0; i<len; i++){
    char = str[i];
    if(charCount[char]){
      charCount[char]++;
    }
    else
      charCount[char] = 1;
  }
  for (var j in charCount){
    if (charCount[j]==1)
       return j;
  }
}  

>firstNonRepeatChar('the quick brown fox jumps then quickly blow air');
 = "f"
        
```

**참고:** 이것은 하나의 문제가 있다. for 루프가 순서대로 실행된다는 보장은 없다.

## 12. 중복 문자를 제거

**질문 :** 문자열에서 중복 문자를 어떻게 제거하는가?

**답변:** 이것은 첫 번째 비 반복 문자와 매우 유사하다. 비슷한 질문을 하면된다. 대소문자를 구분하는가?

대소문자를 구분하면 더 쉬워진다. 구분하지 않는다면. string.toLowercase()를 사용하여 전체 문자열을 변환할 수 있다. 그러나 그것을 좋아하지 않을 수도 있습니다. 리턴 문자열이 같은 결과를 내지 않기 때문이다. 그래서

```js

function removeDuplicateChar(str){
  var len = str.length,
      char, 
      charCount = {}, 
      newStr = [];
  for(var i =0; i<len; i++){
    char = str[i];
    if(charCount[char]){
      charCount[char]++;
    }
    else
      charCount[char] = 1;
  }
  for (var j in charCount){
    if (charCount[j]==1)
       newStr.push(j);
  }
  return newStr.join('');
}

> removeDuplicateChar('Learn more javascript dude');
  = "Lnmojvsciptu"
        
```

**참고:** 이것은 하나의 문제가 있다. for 루프가 순서대로 실행된다는 보장은 없다.

## 13. 회문(palindrome) 확인

**질문 :** 단어가 회문인 것을 어떻게 확인 할 것인가?

**답 :** 단어를 뒤집어 이전 단어와 동일하면 회문이라 한다.

```js

function isPalindrome(str){
  var i, len = str.length;
  for(i =0; i<len/2; i++){
    if (str[i]!== str[len -1 -i])
       return false;
  }
  return true;
}

> isPalindrome('madam')
  = true
> isPalindrome('toyota')
  = false
        
```

또는 내장 메서드를 사용할 수 있다.

```js

function checkPalindrom(str) {
    return str == str.split('').reverse().join('');
}
        
```

**유사 :** 문자열 O (n)이 시간에 연속 회문 문자열이 들어 있는지 여부를 찾는다. O (1) 시간 안에 문제를 해결할 수 있을까?

## 14. 5에서 7 사이의 임의의 수

**질문 :** 1에서 5 사이의 난수를 생성하는 함수가 있다면 그 함수를 사용하여 어떻게 난수를 1에서 7까지 생성 할 수 있을까?

**답 :** 매우 간단하다. 기본적인 산술을 생각하면 얻을 수 있다.

```js

function rand5(){
   return 1 + Math.random() * 4;
}

function rand7(){
  return 5 + rand5() / 5 * 2;
}
        
```

## 15. 빠진 번호 찾기

**질문 :** 하나의 숫자를 제외하고 1에서 100까지의 정렬되지 않은 배열에서 빠진 숫자를 어떻게 찾을 수 있을까?

**설명 :** 배열에 1-100의 숫자 배열이 있다. 배열에서 하나의 숫자만 누락되었다. 배열은 정렬되지 않았다. 빠진 숫자를 찾아라.

**답 :** 생각을 많이 하는 것처럼 행동해야한다. 그런 다음  숫자 n = n * (n + 1) / 2의 합에 대해 이야기 한다.

```js

function missingNumber(arr){
  var n = arr.length+1, 
  sum = 0,
  expectedSum = n* (n+1)/2;
  
  for(var i = 0, len = arr.length; i < len; i++){
    sum += arr[i];
  }
  
  return expectedSum - sum;
}

> missingNumber([5, 2, 6, 1, 3]);
  = 4
        
```

## 16. 2의 합

**질문 :** 정렬되지 않은 배열에서 주어진 숫자에 해당하는 두 개의 숫자가 있는지 확인하라.

**답 :** 세상에서 가장 간단한 것. 이중루프를 이용한다. 

```js

function sumFinder(arr, sum){
  var len = arr.length;
  
  for(var i =0; i<len-1; i++){  
     for(var j = i+1;j<len; j++){
        if (arr[i] + arr[j] == sum)
            return true;
     }
  }
  
  return false;
}

> sumFinder([6,4,3,2,1,7], 9);
  = true
> sumFinder([6,4,3,2,1,7], 2);
  = false
        
```

**면접관  :** 이 함수의 시간 복잡도는 무엇인가

**답변:** O (n 2 )

**면접관  :** 더 잘 만들 수 있나?

**답변:** 합계와 요소의 차이를 저장할 객체를 가질 수 있다. 그런 다음 새 요소에 도달했을 때 차이가 객체라는 것을 알면 원하는 합계까지 합이 되는 쌍이 있다.

### 시도 2

```js

function sumFinder(arr, sum){
  var differ = {}, 
      len = arr.length,
      substract;
  
  for(var i =0; i<len; i++){
     substract = sum - arr[i];

     if(differ[substract])
       return true;       
     else
       differ[arr[i]] = true;
  }
  
  return false;
}

> sumFinder([6,4,3,2,1,7], 9);
  = true
> sumFinder([6,4,3,2,1,7], 2);
  = false
        
```

비슷한 문제 : 배열 에서 두 숫자의 [최대차이](http://programmingpraxis.com/2011/04/01/maximum-difference-in-an-array/) 를 찾아라

## 17. 가장 큰 합계

**질문 :** 두 요소 중 가장 큰 합계를 어떻게 찾을 수 있나?

**답 :** 매우 간단하다. 두 개의 가장 큰 숫자를 찾아서 그 합계를 반환하면 된다.

```js

function topSum(arr){
  
  var biggest = arr[0], 
      second = arr[1], 
      len = arr.length, 
      i = 2;

  if (len<2) return null;
  
  if (biggest<second){
    biggest = arr[1];
    second = arr[0];
  } 
  
  for(; i<len; i++){

   if(arr[i] > biggest){
      second = biggest;
      biggest = arr[i];
    }
   else if (arr[i]>second){
      second = arr[i];
   }
    
 }
 return biggest + second;
}
        
```

## 18. 0 세기

**질문 :** 1 에서 n까지의 총 0의 갯수는?

**답 :** n = 50 인 경우 0의 수는 11 (0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100)이다. 100에는 두 개의 0이 있습니다. 이것은 간단하지만 조금 까다로워 보인다.

**설명 :** 1에서 50까지의 숫자가 있으면 값은 5입니다. 50을 10으로 나눈 값입니다. 그러나 값이 100이면 값은 11입니다. 100/10 = 10 및 10/10이 된다. 그것이 (100, 200, 1000)과 같은 하나의 숫자로 더 많은 0을 얻는 방법이다.

```js

function countZero(n){
  var count = 0;
  while(n>0){
   count += Math.floor(n/10);
   n = n/10;
  }
  return count;
}

> countZero(2014);
  = 223
        
```

## 19. subString

**질문 :** 문자열의 부분 문자열을 어떻게 일치시킬 수 있나?

**답변 :** 문자열을 반복하는 동안 포인터 (문자열과 하위 문자열에 대한 포인터)를 사용한다. 그리고 초기 일치의 시작 색인을 보유 할 다른 변수가 있다.

```js

function subStringFinder(str, subStr){
  var idx = 0,
      i = 0,
      j = 0,
      len = str.length,
      subLen = subStr.length;

   for(; i<len; i++){
   
      if(str[i] == subStr[j])
         j++;
      else
         j = 0;
      
      //check starting point or a match   
      if(j == 0)
        idx = i;
      else if (j == subLen)
        return idx;
  }

  return -1;
}

> subStringFinder('abbcdabbbbbck', 'ab')
  = 0
> subStringFinder('abbcdabbbbbck', 'bck')
  = 9

//doesn't work for this one.
> subStringFinder('abbcdabbbbbck', 'bbbck')  
  = -1
            
```

**질문 :** 마지막 문제를 어떻게 해결할 수 있나?

**답 :** 직접 해봐라..

## 20. 순열

**질문 :** 문자열의 모든 순열을 어떻게 만들 것인가?

**답 :** 이것은 알고리즘에 대한 지식 수준에 따라 어려운 일일 수 있다.

```js

function permutations(str){
var arr = str.split(''),
    len = arr.length, 
    perms = [],
    rest,
    picked,
    restPerms,
    next;

    if (len == 0)
        return [str];

    for (var i=0; i<len; i++)
    {
        rest = Object.create(arr);
        picked = rest.splice(i, 1);

        restPerms = permutations(rest.join(''));

       for (var j=0, jLen = restPerms.length; j< jLen; j++)
       {
           next = picked.concat(restPerms[j]);
           perms.push(next.join(''));
       }
    }
   return perms;
}
        
```

**설명:**

-   **아이디어 :** 아이디어는 매우 간단하다. 문자열을 배열로 변환한다. 배열에서 하나의 문자를 선택한 다음 나머지 문자를 치환한다. 나머지 캐릭터의 순열을 얻은 후, 우리는 선택한 캐릭터와 각 캐릭터를 연결한다.
-   **1 단계** 요소를 선택할 때 변경하지 않도록 원본 배열을 먼저 복사
-   **2 단계** splice를 사용하여 복사 된 배열에서 요소를 제거한다. splice가 배열에서 항목을 제거하기 때문에 배열을 복사한다. 다음 반복에서 선택한 항목이 필요하다.
-   **3 단계** [1,2,3,4] .splice (2,1)은 [3]을 반환하고 나머지 배열 = [1,2,4]
-   **4 단계 :** 재귀 메서드를 사용하여 배열을 문자열로 전달하여 나머지 요소의 순열을 가져온다.
-   **5 단계** 마지막으로 각 + a permute (bc)와 같은 연결

