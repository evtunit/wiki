
> 원문: [DOM Tree](https://github.com/Lee-hyuna/33-js-concepts-kr/wiki/dom-tree)

# DOM Tree

HTML 문서의 기본은 태그다.

Document Object Model(DOM)에 따르면 모든 HTML 태그는 객체다.

한 태그에 둘려쌓여진 중첩된 태그를 "children"이라고 한다. 또한 태그 안에 있는 글자(text)도 객체이다.

이 모든 객체들(태그)은 Javascript를 사용하여 접근이 가능하다.

## DOM 예제

이 문서의 DOM을 살펴보도록 하자.

```html
<!DOCTYPE HTML>
<html>
<head>
  <title>About elks</title>
</head>
<body>
  The truth about elks.
</body>
</html>
```

DOM은 태그의 트리 구조로서 HTML을 나타낸다. 다음과 같이 보인다.

[[/resource/yongkwan/13/01.png]]

태그는 앨리먼트 노드(element node) 또는 앨리먼츠(element) 라고 한다. 상위 태그로 둘러쌓인 중첩된 태그는 상위 태그의 자식들이 된다. 그 결과 앨리먼트 트리를 얻게 된다:  `<html>`은 최 상위에 존재하며, 그 아래  `<head>`와 `<body>`은 `<html>` 태그의 자식들이다.

앨리먼트 노드 안의 텍스트는 `#text`로 표시된 *텍스트 노드*를 생성한다. 텍스트 노드에는 문자열만 포함된다. 텍스트 노드는 자식을 가질수 없고 항상 트리의 잎(leaf) 노드이다.

예를 들어, `<title>` 태그에는 `"About elks"`라는 텍스트가 있다.

텍스트 노드의 특수 문자는 다음과 같다.

- 줄바꿈:  `↵`  (Javascript에선 `\n`)
- 공백:  `␣`

공백과 줄바꿈은 완전하게 유효한 문자이며 텍스트 노드를 생성하고 DOM의 일부가 된다. 그래서 위의 예에서 `<head>` 태그에는 `<title>` 이전에 일부 공백이 포함되어 있고, 그 공백은 `#text` 노드가 된다. ( 해당 노드에는 줄바꿈과 몇개의 공백만 들어있다.)

단, 두가지의 예외 항목이 존재한다.

1. `<head>` 이전의 공백과 줄바꿈은 무시된다.
2. 만약 `</body>` 뒤에 무언가를 넣으면, HTML 스펙이 모든 내용들은 `<body>` 안에 있어야한다고 요구하기 때문에 결국엔 자동적으로 `body`안으로 이동하게 된다. 그래서  `</body>` 뒤엔 공백이 없을수도 있다.

이 외에는 document에 공백이나 어떤 문자라도 있으면 DOM의 텍스트 노드가 된다. 이런 텍스트 노드가 생기면 지우고 싶어도 지울 수 없다.

아래는 여백이 없고 오직 텍스트 노드만 있는 예이다.

```html
<!DOCTYPE HTML>
<html><head><title>About elks</title></head><body>The truth about elks.</body></html>
```

[[/resource/yongkwan/13/02.png]]

> **줄의 맨 마지막 공백 및 중간에 빈 텍스트는 일반적으로 개발자 도구엔 표시되지 않는다.**
> 
> DOM을 확인할 수 있는 브라우저 개발자 도구는 일반적으로 텍스트의 시작 / 끝 부분에 공백을 표시하지 않고 태그 사이에 빈 텍스트 노드 (줄 바꿈)를 표시하지 않는다.
>
> 이는 대부분의 경우 HTML을 꾸미기 위해 주로 사용되기 때문에, HTML이 어떻게 표시되는지에 영향을 미치지 않기 때문이다.
>
> 앞으로 볼 DOM 그래프에서는 간결하게 보이기 위해 이러한 텍스트 노드를 생략 할 수 있다.

## 자동 보정

브라우저가 이상하게 만들어진 HTML을 발견하면 DOM을 작성할 때 자동으로 수정 한다.

예를 들어, 최상단 태그는 항상 `<html>` 이다. 하지만 `<html>`이 문서에 존재하지 않더라도, DOM에 존재할 것이고, 브라우저는 그것을 생성 할 것이다.  `<body>` 또한 마찬가지이다.

 HTML 파일이 하나의 단어 인 경우, 예를 들어 `"Hello"`라면 브라우저는 `<html>`로 감싸고 `<body>`로 감싸고, `<head>`를 추가한다 . 따라서 DOM은 다음과 같아 질 것이다.

[[/resource/yongkwan/13/03.png]]

DOM을 생성하는 동안 브라우저는 문서의 오류를 자동으로 처리하고 태그를 닫는 등의 작업을 수행한다. 닫히지 않은 태그가 있는 문서가 있다고 가정해보자:

```html
<p>Hello
<li>Mom
<li>and
<li>Dad
```

브라우저가 태그를 읽고 누락된 부분을 복구하여 정상적인 DOM이 될 것이다.

[[/resource/yongkwan/13/04.png]]

> **테이블은 항상 `<tbody>`가 존재한다.**
> 
> 테이블은 흥미롭게도 조금 "특별한 경우"이다. DOM 사양에 따라 반드시`<tbody>`가 있어야하지만 HTML 텍스트는 (공식적으로) 생략이 가능하다. 그런 다음 브라우저는 DOM에 `<tbody>`를 자동으로 만든다.
>
> 아래와 같은 HTML을 예로 들어보자.
>
> ```html
> <table id="table"><tr><td>1</td></tr></table>
> ```
>
> DOM 구조는 다음과 같을 것이다.:
> 
> [[/resource/yongkwan/13/05.png]] 
> 
> `<tbody>`가 불쑥 나타났다. 테이블 작업을 할 때 이 점을 명심해라.

## 다른 노드 유형

페이지에 더 많은 태그와 코멘트를 추가해보자.

```html
<!DOCTYPE HTML>
<html>
<body>
  The truth about elks.
  <ol>
    <li>An elk is a smart</li>
    <!-- comment -->
    <li>...and cunning animal!</li>
  </ol>
</body>
</html>
```

[[/resource/yongkwan/13/06.png]]

여기서 새로운 트리 노드 유형, `#comment`로 표시된 주석 노드를 볼 수 있다.

왜 DOM에 주석 노드가 추가되는가라고 생각할 수 있다. 코멘트 노드는 시각적 표현에 영향을 주지 않는다. 하지만 규칙이 있다. HTML에 어떤 것이 있다면, 그것은 반드시 DOM 트리에도 있어야 한다.

**HTML안의 모든 것들은, 심지어 주석도, DOM의 일부가 된다.**

HTML의 맨 처음에있는 `<!DOCTYPE...>` 지시자 조차도 DOM 노드다. DOM 트리에서 `<!DOCTYPE...>` 은 `<html>` 바로 앞에 존재한다. 우리는 그 노드를 건들지 않을 것이고, 그런 이유로 다이어그램에 그리지도 않을 것이지만 그 자리에 존재하고 있다.

전체 문서를 나타내는 `document` 객체는 공식적으로 DOM 노드이기도 하다.

[12개의 노드 유형](https://dom.spec.whatwg.org/#node)이 있다. 실제로 이 문서에서 그중 4개를 썼다.

1. `document` - DOM의 "시작 지점"
2. 엘리먼트 노드 - HTML 태그, 트리 구성 블록
3. 텍스트 노드 - 텍스트 포함
4. 주석 - 때로는 정보를 표시 할 수 있지만, 표시되지는 않는다. JS는 DOM을 통해 접근할 수 있다.

## 실제로 살펴보기 

실시간으로 DOM 구조를 보려면 [Live DOM Viewer](http://software.hixie.ch/utilities/js/live-dom-viewer/)를 사용해보자. document에 입력하기 만하면 DOM이 즉시 표시된다.

## 브라우저 개발자 도구로 보기 

DOM을 탐색하는 또 다른 방법은 브라우저 개발자 도구를 사용하는 것이다. 실제로, 개발자 도구는 개발할 때 사용하는 것이다.

웹 페이지 [elks.html](https://javascript.info/article/dom-nodes/elks.html)을 열고 브라우저 개발 도구를 킨다음 요소 탭으로 전환한다.

다음과 같이 보일 것이다.

[[/resource/yongkwan/13/07.png]]

이제 DOM을 보고, 엘리먼트를 클릭하고, 세부 정보를 보는 등의 작업을 수행할 수 있다.

개발자 도구의 DOM 구조가 단순화 되었다는 것을 명심하라. 텍스트 노드는 단순히 텍스트로 표시됩니다. 그리고 "black"(공백 전용) 텍스트 노드가 전혀 없다.

왼쪽 위 모서리의 버튼을 클릭하면, 웹 페이지에서 노드를 선택(또는 Elements 탭에서 선택)하고 살펴 볼수 있다. 이것은 거대한 HTML 페이지(그리고 그에 상응하는 거대한 DOM)를 가지고 있고 그 안에 있는 특정 엘리먼트의 위치를 보고 싶을 때 유용하게 사용된다.

또 다른 방법은 웹 페이지를 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 "Inspect"를 선택하는 것이다.

[[/resource/yongkwan/13/08.png]]

개발자 도구의 오른쪽 부분에는 다음과 같은 subtab이 있다.

* **Styles** - 기본 제공 규칙(gray)을 포함하여 규칙 별로 현재 엘리먼트 규칙에 적용되는 CSS를 볼 수 있다. 아래 박스의 치수/마진/패딩을 포함하여 거의 모든 것을 그 자리에서 편집할 수 있다.
* **Computed** - 속성별로 요소에 적용된 CSS보기 : 각 속성에 대해 CSS 상속을 포함한 규칙을 볼 수 있다.
* **Event Listener** - DOM 요소에 연결된 이벤트 리스너를 볼 수 있다.
* 기타등등

이것들을 공부하는 가장 좋은 방법은 클릭해 보는 것이다. 대부분의 값은 내부 편집이 가능하다.

## 콘솔과의 상호 작용

DOM을 탐색 할 때 Javascript를 적용하려고 할 수도 있다. 마찬가지로 노드를 가져 와서 수정하기 위해 코드를 실행하여 결과를 확인한다. 다음은 Elements tab과 console 사이를 이동할 때 유용한 몇 가지 팁이다.

* Elements tab에서 첫 번째 `<li>`를 선택해 보자.
* `Esc` 키를 누르면, Elements tab 바로 아래에 console이 열린다. 

이제 콘솔에서 마지막으로 선택한 엘리먼트는 `$0`로, 이전에 선택한 요소는 `$1`로 접근 할 수 있다.

커맨드를 실행할 수 있다. 예를 들어, `$0.sthtmlyle.background = 'red'`는 다음과 같이 선택된 목록 항목을 빨간색으로 만든다.

[[/resource/yongkwan/13/09.png]]

반대로 , DOM 노드를 참조하는 변수가 콘솔에 있는 경우, `inspect(node)`를 사용하여 Elements tab에서 볼 수 있다.

아니면 그냥 콘솔에 출력해서 "있는 곳"을 탐색할 수도 있다. 아래  `document.body`  처럼:

[[/resource/yongkwan/13/10.png]]

이런 것들은 디버깅을 위한 것이다. 다음 장에서 Javascript를 사용하여 DOM에 접근하고 수정할 것이다.

브라우저 개발자 도구는 개발 과정에서 큰 도움이 된다. DOM을 탐색하고, 시도하고, 잘못된 점을 파악할 수 있다.

## 요약

HTML/XML 문서는 브라우저 내부에 DOM 트리로 표시된다.

-   태그는 엘리먼트 노드가되어 구조를 형성한다.
-   텍스트는 텍스트 노드가 된다.
-   기타등등, HTML에 있는 모든 것들은, 심지어 주석에 까지도 DOM에 존재한다.

개발자 도구를 사용하여 DOM을 검사하고 수동으로 수정할 수 있다.

여기에서는 기초부터 시작하여 가장 많이 사용되는 중요한 작업에 대해 했다. Chrome 개발자 도구에 대한 자세한 내용은 [https://developers.google.com/web/tools/chrome-devtools](https://developers.google.com/web/tools/chrome-devtools)를 참조하라. 도구를 배우는 가장 좋은 방법은 여기 저기 눌러보고 메뉴를 읽는 것이다.(대부분의 옵션). 나중에, 전반적으로 이것들을 알게되면, 문서를 읽고 나머지는 선택해서 배워라.

DOM 노드는 그 사이를 이동하고 페이지를 수정하는 등의 작업을 수행 할 수있는 속성과 메서드를 가지고 있다. 우리는 다음 장에서 배워볼 것이다.