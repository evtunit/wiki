![](https://miro.medium.com/max/700/1*VZp0rSpP-oRiEzcqIoo69w.png)  

내 마지막 이야기는 TypeScript의 'class vs interface'에 관한 것이었고 많은 피드백이 와서 기쁩니다.  
그 피드백에서 저는 자바 스크립트로 수업을 할 때 잃어버린 일부 사람들을 보았습니다.  
그때 문장으로 오늘이 이야기를 쓸 의지를 촉발 시켰습니다.  

````
ECMAScript 2015 표준 이후 일반 자바 스크립트에는 적절한 클래스가 있습니다.
````
글쎄.. 아니다. 나는 너희에게 보여주겠다.

## 'class' 키워드 이전
Javascript에서 '클래스'를 구현 한 방법에 대한 예가 있습니다.  
첫 번째 함수는 일종의 생성자이므로 Cat 클래스가 있고 이 클래스에 함수가 있습니다.  
![](https://miro.medium.com/max/700/1*y4PkgnYJmLPur2MVzIJZ4A.jpeg)  

Quicknote : 새 키워드를 잊어 버린 경우 어떻게해야합니까? 글쎄, 아무런 오류도 발생시키지 않고, 당신의 창 이름을 바꾸었다.  

![](https://miro.medium.com/max/629/1*gdfxdrn_ErN5dfyYpQx77g.jpeg)
 
## 프로토 타입은 어디에 있습니까?  
콘솔에서 프로토 타입 체인을 명확하게 볼 수 있습니다.  
![](https://miro.medium.com/max/651/1*PYBw1HG7zT_vQAF6-JohxQ.jpeg)  
당신의 고양이는 이름을 가진 객체이고, 그것은 함수 meow와 Cat 생성자를 포함하고있는 __proto__ 객체를 가지고 있습니다.  
이 객체 / 프로토 타입은 Object 객체를 포함합니다(자바처럼 모든 자바스크립트 객체의 기본 프로토타입인 기본 객체).  
왜 프로토 타입 체인이라고 불리는 지 알 수 있습니다.  

이제 '클래식'클래스에서 작동하는 방식과 많이 다른 점 중 하나는 프로토 타입이 참조라는 점입니다.  
그 말은 만약 당신이 많은 고양이를 만들어서 그 고양이들 중 한 마리에게 그렇게 한다면?

````
delete cat.__proto__.meow
````
Cat 프로토 타입과 동일한 참조를 공유하기 때문에 모든 고양이에게 야옹 할 수있는 용량을 제거했습니다.  

이제 Dog 클래스를 만들자. 고양이와 같지만, 야옹하는 대신 짖는 소리이다.  
![](https://miro.medium.com/max/700/1*5YeNxOZEpc8qF7wgqyUBjg.jpeg)  

이제 당신은 개와 고양이를 얻었습니다. 정말 나쁜 일을 저 지르지 마십시오. 진지하게 절대로 시도하지 마십시오.
````
cat.__proto__ = dog.__proto__;
````
이제 우리 고양이 안에 뭐가 들어 있니?  

![](https://miro.medium.com/max/634/1*f-priWwshYd0cRowUl_tRQ.jpeg)  

고양이는 이제 짖을 수있어! 그는 여전히 고양이지만 그의 프로토 타입은 개 중 하나입니다.  
그리고 만약 내가 당신에게 그것을 말한다면.  
````
cat instanceof Dog === true
````
JS에서 객체의 프로토 타입에 모든 것이 어떻게 연결되는지 이해할 수 있습니다.  

보여? 그게 바로 Javascript의 미친 짓입니다.  
당신은 당신의 고양이가 원형을 바꾸는 것 만으로도 당신이 원하는 모든 것을 할 수 있어.  
문제는 많은 자유로 인해 많은 실수가 발생할 수 있다는 것입니다.  

## 이제 클래스를 구해보자
MDN에 따라 Javascript에서 클래스 정의에 대한 알림을 시작합시다.  

````
ECMAScript 2015에서 소개 된 JavaScript 클래스는 주로 JavaScript의 기존 프로토 타입 기반 상속에 대한 문법적 설탕입니다.  
클래스 구문은 JavaScript에 새로운 객체 지향 상속 모델을 도입하지 않습니다.
```` 

명확하지 않은 경우 JS에서 새 OO 모델을 소개하지 않는 정도를 알려 드리겠습니다.  


클래스 구문을 사용하여 고양이와 개를 예로 들어 봅시다.
![](https://miro.medium.com/max/700/1*9ropehPTGw99dAUNXYFnUQ.jpeg)

우리는 모든 OOP 언어처럼 작성된 모든 것을 가지고 있습니다 : 클래스, 생성자, 슈퍼, 확장 그리고 나는 심지어 재미있는 정적 함수를 추가했습니다.  
하지만 우리 슈퍼캣은 어떻게 생겼을까?  
![](https://miro.medium.com/max/700/1*ADWWzjPuNvoHQlmpMlnIrQ.jpeg)

우리가 확장을 사용한다는 사실은 프로토 타입 체인에서 깊이를 추가했다는 것을 의미합니다.  
이제 우리는 이름과 초강력을 가진 객체를 가지고 있습니다.  
SuperCat 생성자와 __proto__ 등이있는 SuperCat 함수 meow를 사용하여 __proto__ 객체를 가집니다.

즉, 이전과 마찬가지로 프로토 타입의 속성이나 메서드를 삭제할 수 있으며 사용자가 만든 모든 인스턴스에 대해 속성이나 메서드를 삭제할 수 있습니다.  
아무것도 바뀌지 않았습니다 ...음, 무언가가 바뀌 었습니다.  

![](https://miro.medium.com/max/700/1*PPguR4GvnI61LVgRSNeMow.jpeg)  

우리는 이전에했던 방식대로 프로토 타입을 변경해 볼 수 있으며 강력한 MEOW가 있는 짖는 SuperCat을 사용하거나 다른 미친 것들을 할 수 있습니다.    이제는 세계(프로토 타입)가 당신 것입니다.  

![](https://miro.medium.com/max/437/1*JKcskLCNKImaLWUcLGPSMw.jpeg)

이제 class 키워드 뒤에는 여전히 이전과 같은 프로토 타입 방식이라는 것을 알 수 있습니다. 코드에서 사용할 때 생각 해보시기 바랍니다.
필자는 Florian Orpelière (@florpeliere)에게 감사의 말씀을 전합니다.  
필자는이 기사를 쓰기 훨씬 전에 이 주제에 대한 연설을 한 적이 있습니다.
