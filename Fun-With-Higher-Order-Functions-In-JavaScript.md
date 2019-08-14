> 원문: https://derickbailey.com/2015/10/21/fun-with-higher-order-functions-in-javascript/

### 고차함수는 재밌..다?!

JavaScript는 "huge order functions"으로 함수형 프로그래밍이 가능해졌다. 이 언어, 단어 자체는 너무 막연한 생각이 들긴 하다..

![](https://derickbailey.com/wp-content/uploads/2015/10/unwrapping.jpeg)

<br>

선물을 열어보고 안에서 또 다른 선물을 찾는 것 같다, 이 좋은 아이디어로 일하는 방법을 터득한다면 재미있고 흥미롭게 일을 할 수 있을 것이다.

<br>

## Common Examples

`underscore`, `lodash` 라이브러리에 있는 "`debounce`" 또는 "`throttle`"과 같은 많은 일반적인 기능을 포함하여 고차함수는 정말 유용하게  많이 쓰인다

```js
window.onResize = _.throttle(function(){
  // do stuff when the window resizes, here
  // but only do it once every 500 milliseconds
  // and no more than that
}, 500);
```
<br>

이 예제는 throttle 함수를 사용하여 윈도우 브라우저 리사이즈 이벤트를 지속적으로 사용하는 것을 방지하는 역할을 한다.
JavaScript에 내장된 다른 예도 볼 수 있다. 혹시, 함수에 "`bind`" 기능을 사용한 적이 있나요?

<br>

```js
var fn = someFunction.bind(someObject);

fn(someParam, 2, "...");
```

<br>

이것은 고차함수를 만드는 방법이다. 일반적으로 JavaScript 코드에는 다양한 예가 있다. this를 쓰기도 쉽다.

<br>

## Build Your Own Bind Function

브라우저에서 ES5 기반 JavaScript가 보편화되기 전에는 "`bind`" 함수를 직접 작성해야 하는 경우가 많았다. 물론, 한 빌드를 사용하여 `underscore`, `lodash`를 할 수 있지만, 모두가 그렇게 하기를 원하지는 않는다.

기본 아이디어는 함수 및 컨텍스트 객체를 전달하여 해당 함수 내에서 "`this`"를 나타내는 것이다.

<br>

```js
function bind(fn, ctx){
  return function(){
    var args = Array.prototype.slice.apply(arguments);
    return fn.apply(ctx, args);
  }
}
```

"bind" 함수 자체는 다른 함수를 반환하는 것 이상의 역할을 하지 않는다. 일부 고차함수는 내부 함수를 반환하기 전에 일부 계산을 수행하고 다른 코드를 설정한다. 중요한 것은 바인딩 함수가 다른 함수를 반환한다는 것이다.

이 경우 정말 중요한 것이 바로 내부 함수이다. 함수를 반환하게되면 변수에 할당된다. 이 변수는 이제 다음과 같은 함수를 가리킨다.

1. 현재 인수 개체를 올바른 배열로 분할한다.
2. 원래 함수를 호출한다 ("`fn`" 매개 변수로 전달됨).
3. "`ctx`" 변수를 원래 기능에 대한 컨텍스트("`this`")로 적용한다.

<br>

예를 들어 다음과 같다.

```js
function foo(){
  console.log(this.bar);
}

var fn = bind(foo, {bar: "baz"});
fn();
```

<br>

이 간단한 예제에서는 "`this.bar`"를 기록하는 "`foo`" function를 생성한다. 바인딩 함수는 컨텍스트로 지정된 객체 문자 그대로 함수를 생성하는 데 사용된다. 결과 기능을 호출하면 예상된 "`this is a test`" 콘솔 메시지가 생성된다.

<br>

## 객체 메소드를 동적으로 구성하는 방법

JavaScript 개발자는 메소드에서 일반적인 동작을 원할 때 작성해야 하는 코드 양을 줄이는 등 더 높은 순서 기능을 통해 많은 작업을 수행할 수 있다.

예를 들어, SPA에서 누군가가 메뉴에서 링크나 버튼을 클릭할 때 화면에 특정 보기를 렌더링하는 코드가 있을 수 있다. 각 메뉴 항목은 다른 보기를 생성하지만 모든 보기를 동일한 기본 레이아웃에 표시해야 한다.

앱이 이미 실행되고 있으면 새 보기를 레이아웃의 적절한 부분으로 렌더링한다. 그러나 사용자가 브라우저에서 새로 고침 단추를 누르면 보기를 넣기 전에 레이아웃이 제대로 되어 있는지 확인해야 한다.

이를 관리하기 위해 약속을 반환하는 "ShowLayout" 기능을 사용할 수 있다. 특정 보기를 표시하는 각 방법 내에서 이 방법을 호출하고 약속이 해결될 때까지 기다릴 수 있다.

<br>

```js
var MyApp = {

  showLayout(){
    // only show the layout once
    if (this._layoutPromise){ return this._layoutPromise; }

    // no layout shown yet, so set it up now
    this._layoutPromise = new Promise(function(resolve){

      var myLayout = new MyLayout();
      myLayout.render(function(contents){
        $("#container").html(contents);

        resolve(myLayout);
      });

    });

    // return the promise after we set it up
    return this._layoutPromise;
  },

  home: function(){
    // show the layout, then the homepage
    this.showLayout().then(function(layout){
      layout.showContent(new HomeContent());
    });
  },

  about: function(){
    // show the layout, load some data, then show
    // the about page
    this.showLayout().then(function(layout){
      loadData("about", function(data){
        var about = new AboutContent(data);
        layout.showContent(about);
      });
    });
  },

  newThing: function(){
    // show the layout, create the add thing view
    // and then show it
    this.showLayout().then(function(layout){
      var thing = new Thing();
      var addThingForm = new AddThingForm(thing);
      layout.showContent(addThingForm);
    });
  }

};
```

<br>

showLayout 메서드를 호출하는 방법을 변경해야 하는 경우 이러한 모든 메서드에서 변경해야 한다.

고차함수를 사용하면 이 코드를 단순화할 수 있다. `showLayout()` 코드를 모든 기능에 배치하는 대신 고차함수를 사용하여 런타임에 해당 동작을 구성할 수 있다.

<br>

```js
// create a higher order function
// to use the layout and then run
// the next chunk of code
function useLayout(fn){

  // return a function that knows how to use the layout
  return function(){
    this.showLayout().then((layout) => {
      // make sure the original function still
      // has "this" set appropriately
      fn.call(this, layout);
    });
  }

}

// compose the app out of 
// higher order functions
var MyApp = {

  showLayout(){
    // ... same implementation as before
  },

  // show the layout, then the homepage
  home: useLayout(function(layout){
    layout.showContent(new HomeContent());
  }),

  // show the layout, load some data, show about
  about: useLayout(function(layout){
    loadData("about", (data) => {
      var about = new AboutContent(data);
      layout.showContent(about);
    });
  }),

  // show the layout, create add form, show it
  newThing: useLayout(function(layout){
    var thing = new Thing();
    var addThingForm = new AddThingForm(thing);
    layout.showContent(addThingForm);
  })

};
```

<br>


여기서 `showLayout`는 "`useLayout`" 함수인 단일 위치에 캡슐화된다. 이 기능은 `MyApp` 개체에 대해 function을 return하고 해당 개체에 연결될 것으로 가정한다.

`useLayout` 함수는 `MyApp`객체 외부에서 재사용할 수 있는 범위가 적지만, 목적은 `ShowLayout` 호출을 캡슐화하여 필요에 따라 빠르고 쉽게 변경할 수 있도록 하기 위한 것이다. 코드 중복의 양을 줄이는 것은 좋은 일이며, 각 방법에 대한 코드 양을 줄일 수도 있다. 읽기 쉽고 수정하기 쉽다.

그러나 런타임에 MyApp의 메소드에 추가 매개 변수를 전달해야 할 경우 어떻게 되는 것인가?

<br>

## Passing Args From The Function Call

앱에 ID나 라우터에서 전달된 다른 데이터가 필요한 경우 `useLayout` 함수가 이를 고려해야 한다. 레이아웃을 대상 기능과 함께 전달해야 하지만 ID 또는 다른 매개 변수들도 전달해야 한다.

이렇게 하려면 `useLayout`에서 반환되는 기능을 수정하고 ES6의 `...rest` 연산자를 사용하여 인수를 추적하도록 한다. 그런 다음 `fn.apply`를 호출하기 직전에 레이아웃 객체를 `Args` Array에 추가한다.

<br>

```js

// create a higher order function
// to use the layout and then run
// the next chunk of code
function useLayout(fn){

  // return a function that knows how to use the layout
  return function(...args){
    this.showLayout().then((layout) => {
      // prepend the layout to the args
      args = [layout, ...args];
      fn.apply(this, args);
    });
  }

}
```

이제 MyApp의 메서드가 매개 변수를 허용해도 호출에 지정된 레이아웃과 매개 변수가 계속 표시된다.

```js
// compose the app out of 
// higher order functions
var MyApp = {

  // ... other methods

  // load a thing by it's id, then show it
  editThing: useLayout(function(layout, id){
    loadThing(id, (thing) => {
      var editThingForm = new EditThingForm(thing);
      layout.showContent(editThingForm);
    });
  })

};

// call the edit function with an id
MyApp.editThing(123);
```

<br>

레이아웃이 첫 번째 인자로 들어가게 된다.

id는 `editThing`을 호출할 때 사용한다. 즉, ID만 지정해야 나머지 부분은 useLayout을 구현하여 처리할 수 있다.