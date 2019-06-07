# Bitwise Operators in Javascript

번역 : [https://medium.com/bother7-blog/bitwise-operators-in-javascript-65c4c69be0d3](https://medium.com/bother7-blog/bitwise-operators-in-javascript-65c4c69be0d3)

지금까지 우리가 작업 한 두 가지 프로그래밍 언어는 Ruby와 Javascript입니다. 이 둘 사이에는 많은 차이점이 있지만 논점을 다루는 방법은 공통점이 있습니다. AND는 &&로 표현되고, OR는 || 등으로 표현됩니다. 우리는 &&와 || 같은 이중 기호 연산자를 사용하는 데 익숙합니다. 왜 우리는 단일 기호를 사용할 수 없습니까? 단일 심볼은 비트 연산자에 예약되어 있기 때문입니다.

<iframe src="https://giphy.com/embed/l1AsPGi3X2rLKeuxW" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/ufc-mma-l1AsPGi3X2rLKeuxW">via GIPHY</a></p>

비트 연산자 란 무엇입니까? 그것들은 비트 수준에서 변수와 상호 작용하는 방법입니다. 비트는 일반적으로 부동 소수점 및 정수로 변환되므로 정보를 쉽게 소화 할 수 있습니다. 우리가 속도와 효율성을 중요시한다면 비트를 직접 처리하고 그 변환을 floats / int로 건너 뛰는 것이 유용 할 것입니다. 비트는 자바 스크립트의 변수보다 빠르지 만 번역 수준을 건너 뛰는 것보다 복잡합니다.

비트 단위의 단순한 숫자

> 1 (integer) = 0001 (binary)

> 2 = 0010

> 3 = 0011

> 4 = 0100

> 5 = 0101

> 6 = 0110

> 7 = 0111

> 8 = 1000

비트 연산자 (AND, OR, XOR)는 일반 논리 연산자와 비슷하게 동작합니다. 단, 비트 수준에서는 평가하지만 논리를 정상적으로 해석하는 방식이 아닙니다. 다음은 비트 연산자와 일반 논리 연산자의 차이점에 대한 예입니다.

![https://cdn-images-1.medium.com/max/1600/1*v_EFpqIO1FQ_FxbpImiC1g.png](https://cdn-images-1.medium.com/max/1600/1*v_EFpqIO1FQ_FxbpImiC1g.png)

정상적인 논리 연산자는 양쪽 모두가 참일 때 가장 오른쪽 값을 반환합니다.

![https://cdn-images-1.medium.com/max/1600/1*R1solcujsnK83PBpQ44fMA.png](https://cdn-images-1.medium.com/max/1600/1*R1solcujsnK83PBpQ44fMA.png)

비트 연산자는 일반 논리 연산자와 다른 수준에서 3과 6을 평가합니다.

3 & 6의 결과는 어떻게 2와 같을 수 있습니까? 우리는 3 & 6을 본다. 그러나 사실은 아래와 같다. 
![https://cdn-images-1.medium.com/max/1600/1*QAWLF8Z3_S6T0A5P4vpGvg.png](https://cdn-images-1.medium.com/max/1600/1*QAWLF8Z3_S6T0A5P4vpGvg.png)

3 & 6

이 두 비트의 공통 분모가 두 번째 숫자이기 때문에 결과는 2입니다.

![https://cdn-images-1.medium.com/max/1600/1*qZKhSPEV8JsWMS-eTknnKg.png](https://cdn-images-1.medium.com/max/1600/1*qZKhSPEV8JsWMS-eTknnKg.png)

공통 숫자는 오른쪽에서 두 번째 숫자이고이 비트는 숫자 2를 나타냅니다.

비트 연산자가 우리가 익숙한 것과 다른 수준에서 작동한다는 것이 확실합니다. 비트 연산자를 사용하면 어떤 이점이 있습니까? 이러한 작업을위한 실제 응용 프로그램을 찾는 것이 유용 할 것입니다. 비트 수준에서의 평가는 일반 논리 연산자보다 빠르기 때문에 큰 샘플에 대한 평가 또는 반복은 비트 연산에서보다 효율적입니다. 웹 개발에보다 적합한 또 다른 예는 마스킹입니다. 우리는 할 수있어!

<iframe src="https://giphy.com/embed/MpjZmG4eTvso8" width="480" height="448" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/panda-tree-clumsy-MpjZmG4eTvso8">via GIPHY</a></p>

마스킹은 아주 간단한 문자열을 보내고 다른 숫자를 할당하여 다른 플래그를 나타내는 방법입니다. 이것은 일련의 예 또는 아니오 질문을 신속하게 요청하는 방법입니다. 우리가 웹 사이트를 가지고 있고 사용자에게 4 개의 간단한 플래그가 있고 싶다고 가정 해 봅시다.

> Flag A = “Is the user authenticated?” = 1

> Flag B = “Is the user in the correct region?” = 2

> Flag C = “Can we get ice cream after?” = 4

> Flag D = “Is the user a robot?” = 8

이 플래그는 4 자리 2 진 문자열로 전달 될 수 있습니다.

0000 = DCBA

해당 숫자에 1을 입력하여 원하는 플래그를 올릴 수 있습니다.

> 1000 (binary) = Flag D = 8 (integer)

> 0100 (binary) = Flag C = 4 (integer)

> 0010 (binary)= Flag B = 2 (integer)

> 0001 (binary)= Flag A = 1 (integer)

이것은 우리가 통과하는 모든 것이 정수 일 때 동시에 여러 플래그를 발생시키는 방법입니다.

> 1010 (binary) = Flag D and Flag B = 10 (integer)

> 0111 (binary) = Flag C, B and A = 7 (integer)

이 기능이 작동하는지 확인합시다.

![https://cdn-images-1.medium.com/max/1600/1*QhPKjsxw22Hf5a3-cR96fQ.png](https://cdn-images-1.medium.com/max/1600/1*QhPKjsxw22Hf5a3-cR96fQ.png)

![https://cdn-images-1.medium.com/max/1600/1*w6IRRQ8AxCLiQOLZYtNccA.png](https://cdn-images-1.medium.com/max/1600/1*w6IRRQ8AxCLiQOLZYtNccA.png)

![https://cdn-images-1.medium.com/max/1600/1*cTfbvJIxJOGyvx2ysmi7Tg.png](https://cdn-images-1.medium.com/max/1600/1*cTfbvJIxJOGyvx2ysmi7Tg.png)

멋지다, 효과가 있었다. 완전히 예상했다. 하하 아니, 아니지만, 정수를 전달하고 여러 개의 플래그를 올릴 수 있다는 것을 알면 좋습니다.