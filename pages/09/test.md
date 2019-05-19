Due to the amazing quantity of libraries, tools and all kinds of things that make your development easier, a lot of programmers start building applications without having the deep understanding of how something works under the hood. JavaScript is the poster boy of this exact behavior. While it is one of the most complicated languages and most widely spread, many developers are attracted to using higher level tools and abstracting away the “bad parts” of the language.

While you will still be able to do build amazing applications doing that, going into the JavaScript maelstrom can be quite beneficial to you. Understanding the “weird parts” is what separates the average grunt coder from the senior developer and while the JS ecosystem is ever-changing, the fundamentals are those on top of which all other tools are built. Understanding those gives you a broader perception and changes the way you look at the development process.

## What’s the Event Loop?

You’ve probably heard that JavaScript is a single-threaded language. You may have even heard the terms Call Stack and Event Queue. Most people know that the Event Loop is what allows JavaScript to use callbacks and promises, but there’s a lot more to it. Without going into too much details we’ll have a high level view of how JavaScript code is actually executed.

## The Call Stack

JavaScript has a single call stack in which it keeps track of what function we’re currently executing and what function is to be executed after that. But first — what’s a stack? A stack is an array-like data structure but with some limitations — you can only add items to the back and only remove the last item. Another example is a pile of plates — you put them on top of each other and at any time you can only remove the top one.

When you’re about to execute a function it is added on the call stack. Then if that function calls another function — the other function will be on top of the first one in the call stack. When you get an error in the console you get a long message that shows you the path of execution — this is what the stack looked in that exact moment. But what if we make a request or put a timeout on something? In theory that should freeze the entire browser until it is executed so the call stack can continue? In practice however, you know that this doesn’t happen — because of the Event Table and Event Queue.

## The Event Table & Event Queue

Every time you call a setTimeout function or you do some async operation — it is added to the Event Table. This is a data structure which knows that a certain function should be triggered after a certain event. Once that event occurs (timeout, click, mouse move) it sends a notice. Bear in mind that the Event Table does not execute functions and does not add them to the call stack on it’s own. It’s sole purpose is to keep track of events and send them to the Event Queue.

The Event Queue is a data structure similar to the stack — again you add items to the back but can only remove them from the front. It kind of stores the correct order in which the functions should be executed. It receives the function calls from the Event Table, but it needs to somehow send them to the Call Stack? This is where the Event Loop comes in.

## The Event Loop

We’ve finally reached the infamous Event Loop. This is a constantly running process that checks if the call stack is empty. Imagine it like a clock and every time it ticks it looks at the Call Stack and if it is empty it looks into the Event Queue. If there is something in the event queue that is waiting it is moved to the call stack. If not, then nothing happens.

Here are a couple of interesting cases. In what order do you think the following code will run?

setTimeout(() => console.log('first'), 0)
console.log('second')
Some people think that because set timeout is called with 0 (zero) it should run immediately. In fact in this specific example you will see “second” printed out before “first”. JavaScript sees the setTimeout and says “Well, I should add this to my Event Table and continue executing”. It will then go through the Event Table, Event Queue and wait for the Event Loop to tick in order to run.

## Exploits

Another interesting example of the Event Loop’s behavior is recursion. Have you ever seen the stack overflow error message? You sometimes get that when you make an infinite recursion but sometimes you actually have a big number of recursive calls that you want to make. There’s a simple yet hacky workaround which will allow you to retain your code structure and still make an absurd amount of calls — wrap your recursion calls in setTimeout.

Instead calling recursion() (imagine this is the name of your method) directly, you can call setTimeout(() => recursion(), 0). This will avoid the stack overflow because the calls will go through the event table and queue instead of directly piling up on the stack. Try to restrain from using that approach, but it’s a good example of JavaScript’s behavior.

## Conclusion

There is a lot more going on and this is just the basic explanation of the loop and everything else around it. As much as I wanted to keep this as simple as possible there is no way to explain what the Event Loop does without going into the whole process. Something else to have in mind is that this explanation is in the context of the V8 JavaScript Engine. It’s the engine behind Chrome and is also used in Node.

If you’re interested in more JS related content you can subscribe to my newsletter from here or you can take a look at the other articles from the same series: