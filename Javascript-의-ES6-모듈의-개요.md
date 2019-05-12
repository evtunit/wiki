# An overview of ES6 Modules in JavaScript

번역 : https://blog.cloud66.com/an-overview-of-es6-modules-in-javascript/

## 소개

최근까지도 자바 스크립트에서 모듈을 최대한 활용하고 싶다면 서드파티 라이브러리를 이용해야 한다. CommonJS(AMD), Browserify, Webpack 그리고 기타 여러 툴들은 최근까지도 넓게 사용되고 있었다. 
이런 툴들은 매우 파워풀하지만 실행에 많은 복잡함을 안고 있다.

간단하고 표준화 된 모듈 시스템은 자바 스크립트에서 아직까지 잘 알려지지 않았다.
핵심 언어에서 빠져 나온 가장 중요한 기능 중 하나라고 생각한다. 

좋은 소식은 ES6 Modules가 이 모든걸 대체할것이고 모던 브라우저에서 생각보다 빨리 지원이 될거라는 것이다.

## Browser Support

-   **Safari 10.1+**  — Support enabled by default
-   **Firefox 54+**  — Support enabled via  `about:config`
-   **Chrome 60+**  — Support enabled via  `about:flags`
-   **Edge 15**  — Support enabled via  `about:flags`
-   **Opera 47**  — Support enabled via  `about:flags`

이곳에서 더 알아 볼수 있다. [latest browser support information on ES6 modules](http://caniuse.com/#feat=es6-module).

보시다시피, 주요 시간으로는 준비가되지 않았지만 ES6 모듈은 모든 최신 브라우저에서 기본적으로 제공됩니다.

## ES6 Modules 사용법

```html
<!DOCTYPE html>
<html>
	<head>
		<title>ES6 module example</title>
		<script  src="app/utils.js"  type="module"></script>
		<script  src="app/fallback.js"  nomodule></script>
	</head>
	<body>
	</body>
</html>
```

`module`을 script 엘리먼트의 속성 타입으로 추가할 수 있다. `<script type="module">`. 그러면 브라우저는 인라인 또는 외부 스크립트 요소를 ES6 모듈로 처리합니다.

ES6 모듈을 지원하는 브라우저는 nomodule 속성이있는 스크립트를 무시합니다. 이는 레거시 브라우저에 폴백 스크립트를 제공 할 때 유용하다.

이제 ES6 모듈에 엄격 모드가 기본적으로 활성화되어 있음을 알아두자.

###### Single Export

 **utils.js**  file.

```javascript
function hello() {
	return "Hello"
}
function world() {
	return "World"
}
// Basic export
export { hello, world }
```

**main.js**  file.

```javascript
import {hello, world} from '/app/utils.js'
hello();
world();
```

name space를 포함한 버전

```javascript
import * as util from '/app/utils.js'
util.hello();
util.world();
```

###### Named Export
```javascript
export function hello() {
	return "Hello"
}
export function world() {
	return "World"
}
```

Named exports 또한 object/function 을 만들자 마자 export 할 수 있다. 

###### Default Export

**utils.js**  file.

```javascript
var utils = {
	hello: function() {
		return "Hello"
	},
	world: function() {
		return "World"
	}
}

export default utils;
```

**main.js**  file.

```javascript
import utils from  '/app/utils.js';
utils.hello()
utils.world()
```

## Gotchas

ES6 모듈에서 실행되는 일반 JS와 JavaScript 간에는 몇 가지 차이점이 있습니다.

-   **CORS**  — Module scripts 를 CORS의 상태에서 요청할 경우 유효한 CORS 헤더 사용을  해야한다. e.g.  `Access-Control-Allow-Origin: *.`
    
-   **Use root relative paths to import modules(root의 상대경로를 이용해서 import 해라)**  —  `import {foo} from '/utils/bar.js';`  는 동작한다.  `import {foo} from 'utils/bar.js';`   는 동작하지 않는다.
    
-   **Strict mode by default**  — ES6 modules는  [strict mode](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Strict_mode)  로 동작한다. ( `'use strict'`  이게 따로 필요없다).
    
-   **The top level value of  `this`  is  `undefined`**  — top level에 this 값은 window 객체가 아니다..
    
-   **variables are local to the module**  — 특별히 내보내지 않는 이상 모듈의 변수들은 지역 변수들로 남는다.
    
-   **Deferred by default**  — 모듈들은 non blocking으로 동작하고 `defer`scripts 처럼 실행된다.
