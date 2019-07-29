# 자바스크립트로 map, filter, reduce 배우기

## 함수 프로그래밍에서 벤처를 위한 완벽한 툴세트 !

이 기사는 Javascript의 초보자 또는 함수형 Javascript로 작업을 시작하고지도, filter 및 reduce 함수에 대해 들어 본 적이없는 사람들을 대상으로합니다. 이미 이것들을 가지고 많은 연습을한다면, 끝쪽에 있는 흥미로운 글로 이동해도 됩니다.

# 함수형 프로그래밍이란 무엇인가요?

함수형 프로그래밍은 함수의 출력 값이 함수에 전달 된 인수에만 의존하는 프로그래밍 패러다임입니다. 따라서 함수를 특정 시간만큼 호출하면 호출 시간에 관계없이 항상 동일한 결과가 생성됩니다. 이것은 많은 함수가 로컬 또는 글로벌 상태에서 작동하는 여러 가지 공통적이고 현대적인 코드와 대조되는데, 이는 서로 다른 실행에서 다른 결과를 반환 할 수 있습니다. **해당 상태의 변경은 부작용이며 이를 제거하면 코드의 동작을 더 쉽게 이해하고 예측할 수 있습니다.**

# 왜 map, filter, reduce인가요?

함수형 프로그래밍의 핵심 기반 중 하나는 목록 및 목록 연산을 사용한다는 것입니다. Javascript에서 우리는 **map **, **filter ** 및 **reduce **를 가지고 있습니다. 초기 리스트(배열)가 주어진 모든 함수는 동일한 원래의 리스트(배열)를 그대로 유지하면서 다른 것으로 변환합니다.

# [**Map**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map?source=post_page---------------------------)

때때로 우리는 각 객체의 속성을 수정 / 추가하려는 객체 배열을 가지고 있으며, 다른 경우에는 모든 객체를 소문자로 바꾸는 문자열 배열을 가질 수도 있습니다. 실제로 **map **이 구세주이며 무척 사용하기 쉬운 무수한 상황이있을 수 있습니다.

## 어떻게 이것을 사용하나요?

**map**을 호출하는 가장 기본적인 방법입니다.

![img](https://miro.medium.com/max/1400/1*q8TjnvO8M_DpNypAJvS3Ag.png)

Map을 사용하여 배열의 모든 숫자에 2를 곱합니다.

보시다시피, **map **은 콜백을 인수로받습니다. 그 콜백에는 반복의 **현재 값 **, 반복의 **인덱스 **, 맵이 호출 된 원래 배열 **이 주어집니다. 콜백 내에서 "this"**로 사용할 값인 map (콜백 이후)에 대한 선택적 두 번째 인수가 있습니다.

## 예시

두개의 배열 오브젝트 :

![img](https://miro.medium.com/max/1400/1*MFDNOwFfilP-91TGTJxmAg.png)

songs 배열

- [Simple transformation of array of objects into array of strings](https://gist.github.com/JoaoCnh/37ff889a755565ac3b971032b2e9a026?source=post_page---------------------------)



![img](https://miro.medium.com/max/1400/1*yY95Nm5KoSEdXTkqNaclnA.png)

map을 사용한 모든 노래 이름 가져 오기

- [Applying a transformation through an util function](https://gist.github.com/JoaoCnh/33066830188c74eeae8f05b1a4674c2f?source=post_page---------------------------)



![img](https://miro.medium.com/max/1400/1*AIS5viahTSNaVQ23chEG2Q.png)

모든 노래 이름을 소문자로 매핑하는 util 함수 사용

- [Transforming the given array and adding and removing properties from each object](https://gist.github.com/JoaoCnh/6bf50fd47709d8da3b64120a853ba0e0?source=post_page---------------------------)

![img](https://miro.medium.com/max/1400/1*NO3E5VhjTbBN7SwKxSu-vg.png)

Map을 사용하여 속성을 제거하고 배열의 객체에 새 속성을 추가합니다.

## 브라우저 호환성

![img](https://miro.medium.com/max/1400/1*Xqk-dxZLHcOSY3JL2jBMeQ.png)

Javascript map 브라우저 호환성

# [Filter](https://developer.mozilla.org/pt-PT/docs/Web/JavaScript/Reference/Global_Objects/Array/filter?source=post_page---------------------------)

필자는 프로그래머로서의 전체 경로에서 가장 일반적인 연습 중 하나 인 기존 배열에서 일부 항목을 필터링해야한다고 확신합니다. 이것은 당신 자신을하기에 꽤 성가 시던 것이었지만 이제는 땀을 흘릴 필요가 없습니다. **필터 **가 도움이됩니다!

## 어떻게 사용하나요?

**map **과 매우 유사합니다. **map **을 알고 있다면 **filter **를 알 수 있습니다!

![img](https://miro.medium.com/max/1400/1*wLVBiC4US7DwcfrMzn4c0A.png)

**필터 **는 **map **과 같은 인수를받으며 매우 유사하게 작동합니다. 유일한 차이점은 콜백이 **true ** 또는 **false **를 반환해야한다는 것입니다. **true **를 반환하면 배열은 해당 요소를 유지하고 **false **를 반환하면 요소가 필터링됩니다.

## 예제

이전과 같은 노래 배열을 감안할 때

- [Filter the even numbers](https://gist.github.com/JoaoCnh/1bf8ad1f7c627de5085ed950a0e252e5?source=post_page---------------------------)

![img](https://miro.medium.com/max/1400/1*4V_hUYsFLWJnmTdTf6e_Ng.png)

필터를 사용하여 짝수를 필터링하기

- [Do a simple string search](https://gist.github.com/JoaoCnh/695936692866f60d9cc2c16d74ba3696?source=post_page---------------------------)

![img](https://miro.medium.com/max/1400/1*9xZcxV7uEzOT05aci4isYA.png)

필터를 사용하여 "at"가있는 모든 단어 필터링

- [Filtering arrays of objects](https://gist.github.com/JoaoCnh/e8fdd2543c747907363eff4550ccbda1?source=post_page---------------------------)

![img](https://miro.medium.com/max/1400/1*bJK-DJCy1mOO-dc3uVc5TQ.png)

필터를 사용하여 노래 배열에서 모든 마스토돈 노래 가져 오기

## 브라우저 호환성

![img](https://miro.medium.com/max/1400/1*Vu1RE5p1Ha--p22gEL3YBA.png)

Javascript filter 브라우저 호환성

# [Reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce?source=post_page---------------------------)

마지막으로, **reduce **는 배열을 취하여 하나의 값으로 줄입니다. 예를 들어 숫자 배열을 사용하면 모든 값의 평균을 쉽게 찾을 수 있습니다.

## 어떻게 사용하나요?

**map ** 및 **filter **와 비슷하지만 콜백 인수가 다릅니다. 콜백은 이제 **accumulator ** (모든 반환 값을 누적하며 그 값은 이전에 반환 된 누적 값의 누적 값입니다), 현재 값, 현재 인덱스 및 마지막으로 전체 배열을받습니다.

![img](https://miro.medium.com/max/1400/1*qbJR3QhBe8rFaHceT9IiAQ.png)

Reduce를 사용하여 배열의 합을 계산 한 다음 평균을 찾습니다.

## 예제

이전과 같은 노래 배열을 감안할 때

- [Calculate the **sum** of an array of integers](https://gist.github.com/JoaoCnh/f10fb489b10575a9896bd61b1996a0f3?source=post_page---------------------------)

![img](https://miro.medium.com/max/1400/1*UFT--o2DGOTohTFYd307qw.png)

배열 합계 계산

- [Build an object from an array — { artist: song count}](https://gist.github.com/JoaoCnh/8cc6e89429f39fd7511cbc6ba04139fc?source=post_page---------------------------)

![img](https://miro.medium.com/max/1400/1*zlYgMU_RlPiA1-FGrnCuEQ.png)

reduce를 사용하여 배열에서 객체 만들기

- [Turn an array of arrays into a single one](https://gist.github.com/JoaoCnh/cc2267a72e6e5227caea4ede41459a53?source=post_page---------------------------)

![img](https://miro.medium.com/max/1400/1*FyPy3rXjQkWv-1CKqs69pQ.png)

reduce를 사용하여 배열 배열 병합

## 브라우저 호환성

![img](https://miro.medium.com/max/1400/1*ERkX4vloBivLRtGkOZIhug.png)

Javascript reduce 브라우저 호환성

# 위대한 힘 ✨

Triforce The

**지도 **, **필터 ** 및 **축소 **는 연결 가능하며, 함께 사용하면 무제한의 신비한 힘을가집니다! 😄 ✨

Spotify 및 LastFM의 노래가있는 배열 배열이있는 이러한 복잡한 노래 배열을 생각해보십시오. 각 노래의 재생 시간은 초 단위입니다.

![img](https://miro.medium.com/max/1400/1*ZbC04p1PqZjBQTbkeT9MkA.png)

이제는 쉼표로 구분 된 문자열을 3 분보다 긴 모든 노래와 함께 가져와야합니다.] (https://gist.github.com/JoaoCnh/0dde17e75ff4549114dfc6d492f99d88?source=post_page-- ------------------)

![img](https://miro.medium.com/max/1400/1*P6E3sYFttqL4JFJlFsMqkg.png)

# 왜 map, filter, reduce을 사용하나요?

- 인덱스를 통해 액세스하는 대신 현재 값으로 직접 작업합니다 (예 : array [i]).
- 원래 배열의 돌연변이를 피하십시오. 따라서 부작용을 최소화하십시오.
- **for ** 루프를 관리 할 필요가 없습니다.
- 더 이상 빈 배열을 만들고 그 안에 값을 넣지 마십시오.
- **콜백에서 **돌아 오는 구문을 기억하십시오
- 당신은 함수형 프로그래밍의 강력한 힘라고 부를 수 있습니다.