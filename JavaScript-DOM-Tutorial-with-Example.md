## 자바스크립트의 DOM이 뭘까?

JavaScript는 DOM(Document Object Model)을 사용하여 웹 페이지의 모든 요소에 액세스할 수 있습니다. 실제로 웹 브라우저는 페이지가 로드되면 웹 페이지의 DOM을 생성합니다. DOM 모델은 이와 같은 개체의 트리로 작성됩니다.

<br>

![](https://www.guru99.com/images/JavaScript/javascript8_1.png)

<br><br>


## DOM과 이벤트는 어떻게 사용할까?

DOM을 사용하여 JavaScript는 여러 task를 수행할 수 있습니다. 새 요소와 속성을 만들고 기존 요소와 속성을 변경하며 기존 요소와 속성을 제거할 수도 있습니다. JavaScript는 기존 이벤트에 반응하고 페이지에 새 이벤트를 생성할 수도 있습니다.

### getElementById, innerHTML Example

1. `getElementById` ID를 요소의 속성값으로 설정합니다.
2. `innerHTML` 요소의 내용을 액세스합니다.

<br>

```html
<html>
<head>
    <title>DOM!!!</title>
</head>
<body>
    <h1 id="one">Welcome</h1>
    <p>This is the welcome message.</p>
    <h2>Technology</h2>
    <p>This is the technology section.</p>
    <script type="text/javascript">
        var text = document.getElementById("one").innerHTML;
        alert("The first heading is " + text);
    </script>
</body>
</html>
```

<br>

### getElementsByTagName Example

`getElementsByTagName` 태그 이름을 사용하여 요소의 속성에 셋팅을 합니다. 이 방법은 동일한 태그 이름을 가진 모든 항목의 배열을 반환합니다.

```html
<html>
<head>
    <title>DOM!!!</title>
</head>
<body>
    <h1>Welcome</h1>
    <p>This is the welcome message.</p>
    <h2>Technology</h2>
    <p id="second">This is the technology section.</p>
    <script type="text/javascript">
        var paragraphs = document.getElementsByTagName("p");
        alert("Content in the second paragraph is " + paragraphs[1].innerHTML);
        document.getElementById("second").innerHTML = "The orginal message is changed.";
    </script>
</body>
</html>
```

<br>

### Event handler Example

1. `createElement` 새로운 요소를 생성합니다.
2. `removeChild` 요소를 제거합니다.
3. 다음과 같은 특정 요소에 Event handler를 추가할 수 있습니다.

```js
document.getElementById(id).onclick = function() {
    lines of code to be executed
}
```
OR
```js
document.getElementById(id).addEventListener("click", functionname)
```

<br>

```html
<html>
<head>
     <title>DOM!!!</title>
</head>
<body>
    <input type="button" id="btnClick" value="Click Me!!" />
    <script type="text/javascript">
        document.getElementById("btnClick").addEventListener("click", clicked);
        function clicked() {
   	    alert("You clicked me!!!");
        }	
  </script>
</body>
</html>
```