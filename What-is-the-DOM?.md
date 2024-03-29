## DOM 은 무엇인가?

최근에 한 독자가 나에게 DOM이 무엇인지 물어보았습니다. 그것에 대해 언급되고 암시되는 것을 들은 적이 있다고 말했지만, 정말로 이해하는지 확실하지 않습니다. 
우리가 해결할 수 있습니다.

<img src="https://css-tricks.com/wp-content/uploads/2013/12/is-html-the-dom.jpg" width="650" alt="">   

너가 쓰는 HTML이 DOM 입니까?   
아니요. 정말로.  
하지만 작성한 HTML은 브라우저에서 파싱되어 DOM으로 변환됩니다.  

<img src="https://css-tricks.com/wp-content/uploads/2013/12/view-source-no.jpg" width="650" alt="">   

View Source가 DOM 입니까?  
아니요. 정말로.  

View Source는 해당 페이지를 구성하는 HTML을 보여줍니다. 아마 당신이 쓴 그 HTML일 것입니다.  

예를 들어 템플릿 파일에서 백엔드 언어로 작업하고 컴파일 된 HTML 출력을 자주 보지 않으면 다른 코드처럼 보일 수 있습니다.
또는 HTML을 작성하고 코드를 live website에 게시 한 후에 발생하는 "빌드 프로세스"가 있습니다. HTML이 압축되거나 다른 방식으로 변경될수도 있습니다.  

View Source는 사실 약간 이상합니다. 그 코드를 보고 싶은 사람            은 개발자들뿐이고 모든 주요 브라우저들은 현재 개발자 도구를 내장하고 있습니다. 아마도 그 유용성을 능가하지 못했을 것입니다.
 
<img src="https://css-tricks.com/wp-content/uploads/2013/12/devtools-dom.jpg" width="650" alt="">   

DevTools의 코드가 DOM입니까?   
옙!

여러분이 사용하고 있는 DevTools에서 패널을 볼 때, HTML처럼 보이는 것들을 보여주는데, 그것이 DOM의 시각적 표현입니다!  
우리가 해냈어요!

<img src="https://css-tricks.com/wp-content/uploads/2013/12/looks-like-html.jpg" width="650" alt="">  

음. 하지만 내 HTML과 똑같아 보입니다.

네. 그렇습니다. 당신의 HTML 기억에서 직접 창조되었습니다. 대부분 간단한 경우 DOM의 시각적 표현은 단순한 HTML과 같습니다.  
하지만 종종 똑같지 않습니다.  
<br>

### DOM이 HTML과 다른 점은 무엇입니까?
한 가지 가능성이 있습니다. HTML에는 오류가 있으며 브라우저가 당신을 위해 수정했습니다.   
HTML에 `<table>` 요소가 있고 필요한 `<tbody>` 요소를 제외한다고 합시다. 브라우저는 해당 `<tbody>`를 삽입합니다.  
DOM에 있으므로 JavaScript로 찾을 수 있고 HTML에없는 경우에도 CSS로 스타일을 지정할 수 있습니다.
가장 가능성이 높은 경우는..  
<br>

### 자바스크립트는 DOM을 조작 할 수 있습니다.  

HTML에서 다음과 같이 빈 요소가 있다고 가정 해보십시오. 

```html
<div id="container"></div>
```

그런 다음 HTML에 JavaScript가 있습니다.

```html
<script>
  var container = document.getElementById("container");
  container.innerHTML = "New Content!";
</script>
```
자바 스크립트에 대해 잘 모르는 사람이라 할지라도, 그 코드의 일부를 추론할 수 있습니다.  
화면에 `New Content!`가 나타납니다! 
빈 `div` 안에 `new content`가 들어있기 때문입니다. 
DevTools를 사용하여 DOM의 시각적 표현을 확인하면 다음이 표시됩니다:

```html
<div id="container">New Content!</div>
```

원래 HTML이나 View Source에서 볼 수 있는 것과는 다릅니다. 
<br>

### Ajax and Templating
여기서 너무 깊게 들어가지는 않겠지만, Ajax를 이용하여 다른곳의 컨텐츠를 페이지에 올린다면, DOM은 원래의 HTML과는 다르게 작동 할 것이라는걸 상상할 수 있을 것입니다. 일종의 데이터 로딩 및 [클라이언트측 템플릿](https://css-tricks.com/video-screencasts/127-basics-of-javascript-templating/) 사용과 동일합니다.

Gmail로 이동하여 소스를 봅니다. 원래 페이지로드에서 나온 스크립트와 데이터의 모음입니다. 당신이 화면에서 보는 것과 비교하면 거의 알아볼 수 없는 것입니다.  
<br>

### JavaScript vs. the DOM 
JavaScript는 브라우저가 읽고 사용하는 언어입니다. 하지만 DOM은 그 일이 일어나는 곳입니다. 실제로 "JavaScript Thing"이라고 생각할 수있는 것은 "DOM API"입니다. 

예를 들어 요소에 `mouseenter` 이벤트를 감시하는 JavaScript를 작성할 수 있습니다. 그러나 "element"는 실제로 DOM node입니다. DOM node의 DOM 속성을 통해 해당 수신기를 연결합니다. 해당 이벤트가 발생하면 그 이벤트를 발생시키는 것은 DOM node입니다.  

<img src="https://css-tricks.com/wp-content/uploads/2013/12/dom-dom-dom-dom.jpg" width="800" alt=""> 

내가 잘못 말한 거면 사과할께요. 하지만 넌 내가 바라는 요점을 이해했어요. 

DOM은 여기서 생명의 핏줄입니다. 브라우저 모든 곳을 흐릅니다. 
자바스크립트는 단지 구문, 언어일 뿐입니다. DOM API가 전혀 없는 브라우저 외부에서 완전히 사용할 수 있습니다. (Node.js 참조)


### 이 글은 나에게 이상하지 않고, 충분히 이해가 될 만 했습니다.

DOM은 "Document Object Model"을 의미합니다. 나는 그 기사를 쓰고 싶지 않았고 (그리고 자격이 없습니다) 
여기 약간의 유용한 애들이 있습니다.

- W3C: [What is the Document Object Model?](https://www.w3.org/TR/DOM-Level-2-Core/introduction.html)
- MDN: [Introduction - Document Object Model](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)
- Wikipedia: [Document Object Model](https://en.wikipedia.org/wiki/Document_Object_Model)
