자바스크립트는 다른 파일로 기능을 가져오고 내보낼 수 있는 표준 방법을 가지고 있지 않다.<br>
음, 있다면 글로벌변수 정도? *예를 들자면:*
```js
<script src="https://code.jquery.com/jquery-1.12.0.min.js"></script><script>
// `$` variable available here
</script>
```

<br>

이상과는 먼 몇 가지 이유가 있다:
* 동일한 변수 이름을 사용하여 다른 라이브러리와 충돌할 수 있다. 그래서 많은 라이브러리가 [noConflict()](https://api.jquery.com/jquery.noconflict/) 방식을 가지고 있는 것이다.
* cyclic references를 할 수 없다. A모듈이 B모듈에 의존하거나 그 반대인 경우, 어떻게 스크립트를 붙이나요?
* <script> 태그를 붙이는 순서는 중요하고 유지하기가 어렵다.

---
<br>

## CommonJS to the rescue
Node.js와 다른 서버측 JavaScript 솔루션이 나타나기 시작했을 때, 문제를 해결하기 위해 CommonJS를 만들었다.<br>
가져오기/내보내기 문제와 관련하여, 이 규격은 런타임에 의해 주입되는 `require()` 함수와 내보내기 기능을 위한 내보내기 변수를 정의한다.

> Note: CommonJS만 규격은 아니다, UMD와 같은 다른 것들은 프런트엔드와 백엔드 둘 다 사용될 수 있다.

시간이 흐를수록 특히 한 페이지짜리 어플리케이션을 만들기 위한 도구들이 폭발적으로 늘어났다.<br>
프론트엔드에 더 큰 코드 베이스가 있고 프론트엔드와 백엔드 간에 코드를 공유해야 할 필요성 때문에, 
좀 더 편한 방법으로 개발하기 위해 웹팩과 같은 많은 툴이 CJS 규격을 구현하고 이해하기 시작했다.

브라우저가 `require()`를 구현하거나 `exports`를 내장하고 있지 않기 때문에 트렌스컴파일러가 하는 일은 이 기능을 구현하는 것이다.

---
<br>

## How ES6 modules work and why Node.js hasn’t implemented it yet

JavaScript is evolving a lot, specially with ES6, and this problem had to be solved. That’s why ES modules were born. They look a lot like CJS syntactically.

Let’s compare them. This is how we import something in both systems:

```js
const { helloWorld } = require('./b.js') // CommonJS
import { helloWorld } from './b.js' // ES modules
```

This is how we export functionality:

```js
// CommonJS
exports.helloWorld = () => {
  console.log('hello world')
}
// ES modules
export function helloWorld () {
  console.log('hello world')
}
```

엄청 비슷하죠?

It’s been a long time since Node.js has implemented 99% of ECMAScript 2015 (aka ES6), but we will need to wait until the end of 2017 for support for ES6 modules. And it will be only available behind a runtime flag! Why is it taking so long to implement ES6 modules in Node.js if they are so similar to CJS?

Well, the devil is in the details. The syntax is pretty similar between both systems, but the semantics are pretty different. There are also subtle edge cases that require a special effort to be 100% compatible with the specification.

Even though ES modules are not implemented in Node.js, they are implemented already in some browsers. For example we can test them in Safari 10.1. Let’s see some examples and we will see why the semantics are so important. I’ve created these three files:

```js
// index.html
<script type="module" src="./a.js"></script>
// a.js
console.log('executing a.js')
import { helloWorld } from './b.js'
helloWorld()
// b.js
console.log('executing b.js')
export function helloWorld () {
  console.log('hello world')
}
```

What do we see in the console when this is run? This is the result:


```js
executing b.js
executing a.js
hello world
```

However, the same code using CJS and running it in Node.js:

```js
// a.js
console.log('executing a.js')
import { helloWorld } from './b.js'
helloWorld()
// b.js
console.log('executing b.js')
export function helloWorld () {
  console.log('hello world')
}
```

Will give us:

```js
executing a.js
executing b.js
hello world
```

So… it has executed the code in different order! This is because ES6 modules are first parsed (without being executed), then the runtime looks for imports, loads them and finally it executes the code. This is called async loading.

On the other hand, Node.js loads the dependencies (requires) on demand while executing the code. Which is very different. In many case this may not make any difference, but in other cases it is a completely different behavior.

Node.js and web browsers need to implement this new way of loading code keeping the previous one. How do they know when to use a system and when the other one? Browsers know this because you specify it at the <script> level, as we’ve seen in the example with the type property:

```js
<script type="module" src="./a.js"></script>
```

However, how does Node.js know? There’s been a lot of discussion about this and there’s been a lot of proposals (checking first the syntax and then deciding whether or not it should be treated as a module, defining it in the package.json file,…). Finally the approved proposal has been: the Michael Jackson Solution. Basically if you want a file to be loaded as an ES6 module you will use a different extension: .mjs instead of .js.

> The extension name (.mjs) is the reason why this is sometimes dubbed the Michael Jackson Solution.


At the beginning it seemed to me a very bad decision, but now I think it’s the best solution, because it’s easy and any tool (text editor, IDE, preprocessor) will know the easiest possible way if a file needs to be treated as an ES6 module or not. And it only adds the minimal overhead possible to the loading process.

If you want to know more about the implementation status of ES6 modules in Node.js you should read this update.

---
<br>

## A note about Babel

Babel implements ES6 modules, but… incorrectly. It doesn’t implement the full spec. So beware that if you are using Babel when switching to a native ES6 modules implementation, you may have side-effects.

---
<br>

## Why ES6 modules are good and how to get the best of both worlds
ES6 modules are great for two main reasons:

- They are a cross-platform standard. They will work in both Node.js and web browsers.
- Imports and exports are static. It has to be that way because of how the loading process works. Remember that we said the runtime first loads the file, parses it and then, before executing it, it loads the dependencies? This is only possible if imports and exports are static. You cannot do import 'engine-' + browserVersion This is good for a reason: tools can do static analysis of the code, figure out which code is actually being used and tree shake it. This is specially useful when using third-party libraries: you never use all the functionality they provide, so you can remove lots of bytes of code that the user won’t ever execute.

But, does this mean that I can no longer import functionality dynamically? To me this is very useful. Many times I do things like:

```js
const provider = process.env.EMAIL_PROVIDER
const emailClient = require(`./email-providers/${provider}`)
```

This way I get a different implementation with the same interface just with a configuration change, without having to load the code of all the implementations.

So, what happens with ES6 modules? Well, don’t worry, there’s a stage-3 proposal (which means it will likely be approved soon) that adds an import() function. This function accepts a path and returns the exported functionality as a promise.

So with ES6 modules and import() we will get the best of both worlds. 🚀