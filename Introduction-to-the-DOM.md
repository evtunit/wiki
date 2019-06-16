# Introduction to the DOM

번역 : [https://www.digitalocean.com/community/tutorials/introduction-to-the-dom](https://www.digitalocean.com/community/tutorials/introduction-to-the-dom)

### Introduction

일반적으로 ** DOM **이라고하는 ** 문서 객체 모델 **은 웹 사이트를 대화식으로 만드는 필수적인 부분입니다. 프로그래밍 언어가 웹 사이트의 내용, 구조 및 스타일을 조작 할 수있게 해주는 인터페이스입니다. JavaScript는 인터넷 브라우저에서 DOM에 연결하는 클라이언트 측 스크립팅 언어입니다.

웹 사이트가 이미지의 슬라이드 쇼간에 회전하거나, 사용자가 불완전한 양식을 제출하려고 할 때 오류를 표시하거나, 탐색 메뉴를 전환하는 등의 작업을 수행하는 거의 언제든지 JavaScript가 DOM에 액세스하고 조작 한 결과입니다. 이 기사에서는 DOM이 무엇인지,`document` 객체로 작업하는 방법, HTML 소스 코드와 DOM의 차이점에 대해 배우게됩니다.

** 참고 : ** DOM은 언어에 구속력이 없거나 특정 프로그래밍 언어와 독립적으로 작성되었지만이 리소스 전체에서 JavaScript의 HTML DOM 구현에 초점을 맞추고 참조합니다.

## Prerequisites

DOM을 효과적으로 이해하고 이것이 웹 작업과 어떻게 관련되어 있는지를 이해하려면 [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML)에 대한 기존 지식이 필요합니다. 및 [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)를 참조하십시오. 근본적인 [JavaScript 구문 및 코드 구조](https://www.digitalocean.com/community/tutorials/understanding-syntax-and-code-structure-in-javascript) 에 익숙한 것도 유익합니다.


## What is the DOM?

가장 기본적인 수준에서 웹 사이트는 HTML 문서로 구성됩니다. 웹 사이트를 보는 데 사용하는 브라우저는 HTML과 CSS를 해석하고 사용자가 보는 페이지에 스타일, 내용 및 구조를 렌더링하는 프로그램입니다.

브라우저는 HTML과 CSS의 스타일과 구조를 구문 분석하는 것 외에도 문서 객체 모델이라는 문서의 표현을 만듭니다. 이 모델을 사용하면 JavaScript가 웹 사이트 문서의 텍스트 내용과 요소에 객체로 액세스 할 수 있습니다.

JavaScript는 대화 형 언어이므로 수행하여 새로운 개념을 이해하는 것이 더 쉽습니다. 아주 기본적인 웹 사이트를 만들어 보겠습니다. index.html 파일을 만들고 새 프로젝트 디렉토리에 저장하십시오.

```html
<!DOCTYPE html>
<html lang="en">

  <head>
    <title>Learning the DOM</title>
  </head>

  <body>
    <h1>Document Object Model</h1>
  </body>

</html>
```

이 코드는 새로운 웹 사이트 뼈대의 익숙한 HTML 소스입니다. doctype과`head`와`body`가 내부에있는`html` 태그가 웹 사이트 문서의 가장 중요한 요소입니다.

우리는 Chrome 브라우저를 사용하고 있지만 다른 최신 브라우저에서 비슷한 결과를 얻을 수 있습니다. Chrome에서 'index.html'을 엽니 다. 우리는 "Document Object Model"이라는 제목의 일반 웹 사이트를 보게됩니다. 페이지의 아무 곳이나 마우스 오른쪽 버튼으로 클릭하고 "Inspect"를 선택하십시오. 그러면 개발자 도구가 열립니다.

_Elements_ 탭 아래에 DOM이 표시됩니다.

![https://assets.digitalocean.com/articles/eng_javascript/dom/the-dom.png](https://assets.digitalocean.com/articles/eng_javascript/dom/the-dom.png)


이 경우 확장되었을 때, 방금 작성한 HTML 소스 코드 (doctype 및 추가 된 몇 가지 다른 HTML 태그)와 완전히 동일하게 보입니다. 각 요소 위에 마우스를 올리면 렌더링 된 웹 사이트의 각 요소가 강조 표시됩니다. HTML 요소의 왼쪽에있는 작은 화살표를 사용하여 중첩 된 요소의보기를 전환 할 수 있습니다.

## The Document Object


`document` 객체는 웹 사이트에 액세스하고 수정할 수있는 많은 ** 속성 ** 및 ** 메서드 **가있는 내장 객체입니다. DOM 작업 방법을 이해하려면 JavaScript에서 객체가 작동하는 방식을 이해해야합니다. 객체 개념에 익숙하지 않은 경우 [JavaScript의 객체 이해] (https://www.digitalocean.com/community/tutorials/understanding-objects-in-javascript)를 검토하십시오.

** index.html **의 개발자 도구에서 _Console_ 탭으로 이동하십시오. 콘솔에`document`를 입력하고 ENTER를 누릅니다. 출력되는 내용은 _Elements_ 탭의 내용과 동일합니다.

```sh
> document;
```

```html
Output
#document
<!DOCTYPE html>
<html lang="en">

  <head>
    <title>Learning the DOM</title>
  </head>

  <body>
    <h1>Document Object Model</h1>
  </body>

</html>
```

`document`를 타이핑하고 그렇지 않으면 콘솔에서 직접 작업하는 것은 일반적으로 디버깅 밖에서 할 일이 아니지만 아래에서 알 수 있듯이`document` 객체가 무엇인지와 그것을 수정하는 방법을 정확히 응고시키는 데 도움이됩니다.

## What is the Difference Between the DOM and HTML Source Code?

현재이 예제에서 HTML 소스 코드와 DOM은 완전히 똑같은 것으로 보입니다. 브라우저에서 생성 한 DOM이 HTML 소스 코드와 다른 두 가지 인스턴스가 있습니다.

- DOM은 클라이언트 측 JavaScript에 의해 수정됩니다.
- 브라우저가 자동으로 소스 코드의 오류를 수정합니다.

클라이언트 측 JavaScript로 DOM을 수정하는 방법을 보여줍니다. 콘솔에 다음을 입력하십시오.

```sh
> document.body;
```

콘솔은 다음 출력으로 응답합니다.

```html
output
<body>
  <h1>Document Object Model</h1>
</body>
```

`document`는 객체이고,`body`는 우리가 dot notation으로 접근 한 객체의 속성입니다. `document.body`를 콘솔에 제출하면 body 요소와 그 안의 모든 내용을 출력합니다.

콘솔에서, 우리는이 웹 사이트에서`body` 객체의 몇몇 라이브 속성을 변경할 수 있습니다. 우리는`style` 속성을 편집하여 배경색을`fuchsia`로 변경합니다. 콘솔에 다음을 입력하십시오.


```sh
> document.body.style.gackgroundColor = 'fuchsia';
```

위 코드를 입력하고 제출하면 배경색이 변경 될 때 사이트에 대한 실시간 업데이트가 표시됩니다.

![https://assets.digitalocean.com/articles/eng_javascript/dom/modifying-the-dom.png](https://assets.digitalocean.com/articles/eng_javascript/dom/modifying-the-dom.png)

_Elements_ 탭으로 전환하거나`document.body`를 다시 콘솔에 입력하면 DOM이 변경되었음을 알 수 있습니다.

```html
<body style="background-color: fuchsia;">
  <h1>Document Object Model</h1>
</body>
```

**주의 : **`background-color` CSS 속성을 변경하기 위해서는 자바 스크립트에서`backgroundColor`라고 입력해야합니다. 하이픈으로 연결된 모든 CSS 속성은 JavaScript의 camelCase로 작성됩니다.

`body`의 배경색에 `fuchsia`를 대입하여 입력 한 JavaScript 코드는 이제 DOM의 일부입니다.

그러나 페이지를 마우스 오른쪽 단추로 클릭하고 "페이지 소스보기"를 선택하십시오. 웹 사이트의 소스에는 JavaScript를 통해 추가 한 새 스타일 속성이 포함되어 있지 않습니다. 웹 사이트의 소스는 변경되지 않으며 클라이언트 측 JavaScript의 영향을받지 않습니다. 페이지를 새로 고침하면 콘솔에 추가 한 새 코드가 사라집니다.

DOM이 HTML 소스 코드와 다른 출력을 갖는 다른 인스턴스는 소스 코드에 오류가있는 경우입니다. 한 가지 일반적인 예는`table` 태그입니다.`tbody` 태그가`table` 내부에 필요하지만 개발자는 종종 HTML에 태그를 포함하지 않습니다. 브라우저는 자동으로 오류를 수정하고 `tbody`를 추가하여 DOM을 수정합니다. DOM은 닫히지 않은 태그도 수정합니다.

## Conclusion


이 튜토리얼에서는 DOM을 정의하고`document` 객체에 액세스하여 JavaScript 및 콘솔을 사용하여`document` 객체의 속성을 업데이트하고 HTML 소스 코드와 DOM의 차이점을 살펴 보았습니다.

DOM에 대한 자세한 내용은 Mozilla 개발자 네트워크의 [DOM (Document Object Model)](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) 페이지를 참조하십시오.

다음 튜토리얼에서는 중요한 HTML 용어를 검토하고, DOM 트리에 대해 배우고, 노드가 무엇인지 알아보고, 가장 일반적인 유형의 노드에 대해 배우고, JavaScript로 대화 형 스크립트를 작성하기 시작합니다.