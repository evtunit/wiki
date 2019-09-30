자바스크립트는 매일 진화하고 있습니다. [React](https://reactjs.org/) , [Angular](https://angularjs.org/) , [Vue](https://vuejs.org/) , [NodeJS](https://nodejs.org/) , [Electron](https://electronjs.org/) , [React Native](https://facebook.github.io/react-native/) 와 같은 프레임워크 및 플랫폼이 빠르게 성장함에 따라 큰 규모의 애플리케이션에 자바스크립트를 사용하는 것이 일반적이 되었습니다. 자바스크립트가 jquery에 의존하던 시대는 지났습니다.  또한 이제는 특정 문제에 적합한 툴이 무엇인지 이해하고, 효율적이고 성능이 뛰어난 응용프로그램을 작성하는 것이 중요합니다.

이러한 이유에서 **데이터 구조** 는 매우 중요합니다. 자기주도 학습 프로그래머였기 때문에 졸업 할때까지 데이터 구조를 공부 한 적이 없습니다. 그러나 최근에 나는 자바스크립트를 기반으로 한 대규모 응용 프로그램을 작업하고 있으며, 올바른 상황에서 올바른 도구를 사용하는 것이 중요하다는 것을 깨달았습니다. ( 여기서 *도구* 는 데이터 구조만을 지칭하지 않습니다. 추가 블로그 게시물에서 다룰 내용).  코드 상에서 약간의 간단한 수정 만으로도 응용 프로그램의 성능이 크게 향상 될 수 있습니다.  
  
이 첫 번째 블로그 글에서는 기본 데이터 구조 인 **스택**으로 시작 합니다.

## 스택

![](https://miro.medium.com/max/830/0*bbTnCsiT0klMbi3q.png)

스택은 이해하기 쉽습니다. 위의 이미지를 보면 스택의 대부분의 속성을 이해할 수 있습니다. 위에 표시된 스택의 첫 번째 항목은 '빨간' 부분입니다.( 'out'이 쓰지연). 그러나 이 항목을 제거하려면 다른 항목들을 먼저 제거해야만 하기 때문에 가장 마지막에 제거될 것입니다. 간단히 말해서 스택의 첫 번째 항목은 마지막에 제거 될 항목입니다.

코드를 눈으로 본다면 더 명확 해집니다. ES6 구문을 사용하여 더 깔끔하고 이해하기 쉽게 만들었습니다.

스택의 모습은 다음과 같습니다.

```js
class Stack {  
	constructor(){  
	  
	}  
	  
	push(item){  
	//push item to the stack  
	}  
	  
	pop(){  
	//pull out the topmost item (last item) from stack  
	}  
	  
	peek(){  
	// see what's the last item in stack  
	}  
	  
	size(){  
	//no. of items in stack  
	}  
	  
	isEmpty(){  
	// return whether the stack is empty or not  
	}  
}
```

함께 코드를 작성해 봅시다. 내부적으로 배열을 사용하여 스택의 항목을 다음과 같이 저장할 수 있습니다.

```js
class Stack {  
  constructor(){  
    this._items = [];  
  }  
  ...
```

변수 이름 앞의 _ (밑줄)은 이 변수가 내부적으로 만 사용되어야하며  스택의 어떤 인스턴스도 이 변수에 접근해선 안된다는 것을 전달하기 위해 사용 되었습니다.

스택의 다른 누락된 부분을 구현해 봅시다.

```js
class Stack {  
	constructor(){  
		this._items = []  
	}  
	  
	push(item){  
		//push item to the stack  
		return this._items.push(item)  
	}  
	  
	pop(){  
		//pull out the topmost item (last item) from stack  
		return this._items.pop()  
	}  
	  
	peek(){  
		// see what's the last item in stack  
		return this._items[this._items.length-1]  
	}  
	  
	size(){  
		//no. of items in stack  
		return this._items.length  
	}  
	  
	isEmpty(){  
		// return whether the stack is empty or not  
		return this._items.length==0  
	}  
}
```

스택의 기본 구조를 완성했습니다. 그러나 여전히 구현하고 싶은 몇 가지 기능이 있습니다.

1.  초기화하면서 스택에 일부 항목을 전달 할 수 있어야합니다.
2.  여러 항목을 한꺼번에 추가하고, 제거할 수 있어야합니다.

```js
class Stack {  
	constructor(...items){  
	this._items = []  
	  
	if(items.length>0)  
		items.forEach(item => this._items.push(item))
	}  
	  
	push(...items){  
		//push item to the stack  
		items.forEach(item => this._items.push(item) )  
		return this._items;
	}  
	  
	pop(count=0){  
		//pull out the topmost item (last item) from stack  
		if(count===0)  
			return this._items.pop()  
		else  
			return this._items.splice( -count, count )  
	}  
	  
	peek(){  
		// see what's the last item in stack  
		return this._items[this._items.length-1]  
	}  
	  
	size(){  
		//no. of items in stack  
		return this._items.length  
	}  
	  
	isEmpty(){  
		// return whether the stack is empty or not  
		return this._items.length==0  
	}  
}
```

 훨씬 나아 보입니다. 그러나 여전히 몇 가지 문제점을 고쳐야합니다.

1.  외부에서 누구나 _items 변수에 액세스하여 변경할 수 있습니다. ES6에서 [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) 을 사용하여 이 문제를 해결할 수 있습니다.
2.  전역 네임 스페이스를 오염시키지 않으려면 Stack의 네임스페이스를 올바르게 지정해야합니다. 이를 위해 전체 구현을 클로저로 감쌉니다.
3.  스택에서 현재 항목을 볼 수있는 방법이 필요합니다. 이를 위해 *toArray()* 메소드를 추가합니다.

아래는 이러한 문제가 해결 된 최종 구현 입니다.

```js
let Stack = (()=>{  
 let map = new WeakMap();  
 let _items = [];
 
 class Stack {  
  constructor(...items){  
    // let's store our items array inside the weakmap  
    map.set(this, []);  
    // let's get the items array from map, and store it in _items for easy access elsewhere  
    _items = map.get(this);  
      
    //if constructor receives any items, it should be stacked up  
    this.push(..items)  
        
  }  
    
  push(...items){  
    //push item to the stack  
     items.forEach(item => _items.push(item) )  
     return _items;  
      
  }  
    
  pop(count=1){  
    //pull out the topmost item (last item) from stack  
       return _items.splice( -count, count )  
  }  
    
  peek(){  
    // see what's the last item in stack  
    return _items[_items.length-1]  
  }  
    
  size(){  
    //no. of items in stack  
    return _items.length  
  }  
    
  isEmpty(){  
    // return whether the stack is empty or not  
    return _items.length==0  
  }  
    
   toArray(){  
     return _items;  
   }}
   
   return Stack;
 })();
```

최종 구현은 또한 아래에서 확인 가능합니다.
[https://gist.github.com/anish000kumar/0fd37acc866a9577cf259980500b1bbe](https://gist.github.com/anish000kumar/0fd37acc866a9577cf259980500b1bbe)

## 스택 사용

다음은 우리가 만든 스택을 사용하는 작은 예제 입니다.

```js
let my_stack = new Stack(1,24,4);  
// [1, 24, 4]

my_stack.push(23);
//[1, 24, 4, 23]

my_stack.push(1,2,342);  
//[1, 24, 4, 23, 1, 2, 342]

my_stack.pop();  
//[1, 24, 4, 23, 1, 2]

my_stack.pop(3);
//[1, 24, 4]

my_stack.isEmpty();
// false

my_stack.size();  
// 3

my_stack.toArray();  
//[1, 24, 4]
```