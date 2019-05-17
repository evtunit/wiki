Javascript를 충분히 작성했다면 다음 사항을 확실히 알게 될 것입니다.


```js
setTimeout(function doSomething() {
  // Do stuff.
}, 0);
```

0밀리초를 기다리는 이유는 무엇입니까? 그냥 직접 `doSomething`을 call하는 게 어때요? 

정답은 이 `setTimeout`이 Javascript 엔진에 0ms 이후 작업을 실행하도록 지시하지 않는다는 것입니다. 

오히려, Javascript 엔진에 최소 0ms 후에 `doSomething`을 실행하라고 지시합니다. 

이것은 Javascript의 작동 방식을 이해하기 위한 핵심 구분입니다. `doSomething`은 거의 즉시 실행되거나 몇 초 후에 실행될 수 있습니다. 

예를 들어, 다음 코드를 고려합니다.


```js
let startTime = Date.now();
setTimeout(function() {
  console.log(Date.now() - startTime);
}, 500);

while (Date.now() - startTime < 1000) {} // Pause for 1 second.
```

## Javascript’s stack frames
When you call a function, a “stack frame” is created. A stack frame is a container that holds all the variables declared in a function. When a function finishes running, the corresponding stack frame is deleted. When you call a function inside a function, 2 stack frames are created. These are placed in a stack. The stack frame created by the first function is called the “initial frame.” The functions can recursively call other functions and millions of functions may be called after you initially called just one function. When all the functions finish running, the initial frame is removed from the stack. At this point, no functions (i.e. none of your code) is running.


## Javascript’s message queue
After all the functions finish running, how do you call new functions? Just add it to the message queue! Javascript has a queue of “messages”. Each message contains a single function. When the stack is empty (i.e. when all the functions finish running), the Javascript engine checks if the message queue is empty. If it isn’t empty, the engine removes the first message and runs the function inside. When the function runs, a new stack frame (the initial frame) is created and added to the stack. When the function finishes running, the initial frame is removed from the stack, and the Javascript engine checks if there’s another message in the message queue. This repeats until the message queue is empty.

## Adding to the message queue
We now know that Javascript has a queue of “messages”. Javascript runs the functions in each message until the message queue is empty. How did messages end up in the message queue in the first place? The most common ways to add to the message queue are events (DOM elements, XMLHttpRequest, server-sent events, etc), setTimeout, and setInterval. If I run setTimeout with a delay of 500ms, I’m telling the Javascript engine to add the callback function to the message queue in 500ms. There may also be functions in the stack that are already running. The callback function has to wait until the stack is empty before it can run. This is why the function runs after at least 500ms. In the example above where we paused for 1000ms, the setTimeout callback has to sit in the message queue for 1000ms – 500ms = 500ms before it runs. Events also add to the event queue instead of running immediately. Consider this code:

```js
let startTime = Date.now();
document.onclick = function() {
  console.log(Date.now() - startTime);
};
```

while (Date.now() - startTime < 1000) {} // Pause for 1 second.
If you click on the web page while the loop is looping, the click handler doesn’t run immediately. Instead, it’s added to the message queue. When the loop finishes, the Javascript engine then runs the click handler. console.log should output 1000 or something slightly higher. It could be slightly higher because ending the previous function and starting the click handler could take a few milliseconds.


## The event loop
The event loop refers to the loop that checks if there are more messages in the message queue. At the high level, the loop looks something like this:

```js
while (await messageQueue.nextMessage()) {
  let message = messageQueue.shift();
  message.run();
}
```

If the message queue has messages, this removes the next message from the message queue and runs the function associated with it. Otherwise, it waits for new messages to be added to the message queue. The event loop is the underlying model that allows asynchronicity in Javascript. The event loop is necessary in Javascript because Javascript is single-threaded. In multi-threaded languages, when an event is triggered, its event handlers can run immediately. If there’s already a function running, the event handler can run in a new thread. In Javascript, this isn’t possible (unless you use Web Workers, but let’s pretend they don’t exist for now). Since Javascript is single-threaded, it has to wait for the previous function to finish running before running the event handler. If multiple event handlers were triggered, they should run in the order that they were triggered. This is why Javascript needs the message queue and event loop. The event loop can be confusing at first. I recommend playing around with some code to figure out how it works. Unfortunately, it isn’t possible to view the message queue in any browser (as far as I know), so you’ll have to infer what’s happening with the event loop. Here’s an example that you can play around with to better understand the event loop:


```js
document.addEventListener('click', () => {
  setTimeout(console.log.bind(console, 'third'), 0);
  setTimeout(console.log.bind(console, 'fifth'), 100);
  console.log('first');
});

document.addEventListener('click', () => {
  setTimeout(console.log.bind(console, 'fourth'), 0);
  console.log('second');
});
```