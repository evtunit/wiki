# The Factory Pattern in JS ES6

번역 : [https://medium.com/@SntsDev/the-factory-pattern-in-js-es6-78f0afad17e9](https://medium.com/@SntsDev/the-factory-pattern-in-js-es6-78f0afad17e9)

나는 ES6 (ES2015)에서 모든 새로운 것들을 최대한 활용하려고 노력하고 있습니다. 그리고 Factory가 필요한 곳에서 새로운 라이브러리를 작성한다. 그래서 나는 모든 새로운 것들을 어떻게 활용할 수 있었는지 궁금해했고, 모든 ES5가 ES6 와 함께 계속해서 유용하고 유효다나는것을 잊지 않았습니다.

나는 조사하고 구글링 했다 왜냐하면 그곳에는 매우 똑똑한 코더가 있기 때문에 이런 종류의 질문을하고 똑똑한 대답을하기 때문입니다. 결과를 얻는 것은 상당히 어려웠고, 두 영역을 결합한 것을 찾을 수 없었습니다. ES6 + Factory-Pattern. 어쩌면 누구도 그것에 대해 글을 남기지 않았거나 두 분야 모두 다른 기술이나 ES5에 대한 인기 게시물이 상위 결과에 나타나고 새 게시물이 아직 순위를 올리지 못하고 있기 때문에 두 분야 모두 자체적으로 많은 참고 자료가 있기 때문일 수 있습니다.

그래서 나는 혼자서 뭔가를 생각해 내야 만했다. 그러나 주제로 들어가기 전에, 우리는 "Factory 패턴"의 그 것이 무엇인지에 대한 공통된 이해가 필요합니다. 주제를 밝게하는 [Rob Dodson의 게시물] (https://robdodson.me/javascript-design-patterns-factory/)을 읽는 것이 좋습니다. 그는 3 가지 개념 팩토리 패턴에 대해 이야기합니다.

-   Simple Factory
-   Factory Method
-   Abstract Factory

" **simple factory**는 다른 객체의 생성을 캡슐화하는 객체입니다." ES6에서 그것은 "new"에 의해 instathiated되는 생성자가 될 수 있습니다.

```javascript
//Definition of class
class User { 
	constructor(typeOfUser){
        this._canEditEverything = false; 
        if (typeOfUser === "administrator") {               
            this._canEditEverything = true;
        }
    } 
	get canEditEverything() { return this._canEditEverything; }
	
}//Instatiation
let u1 = new User("normalGuy");
let u2 = new User("administrator");
```

당신이 볼 수 있듯이 단순한 공장은 매우 간단합니다. 하나의 클래스에만 적용됩니다. 많은 상황에 충분히 좋습니다.

"** Factory Method **는 하나의 메소드를 정의합니다. 예를 들어 _createThing_ 은 반환 할 항목을 결정하는 하위 클래스에 의해 재정의됩니다. Factories과 Products은 클라이언트가 그것들을 사용하기 위한 인터페이스를 따릅니다."

새로운 메소드를 만드는 대신 "new"를 계속 사용하면서 ES6에서 클래스를 확장하여 구현할 수 있습니다.

```javascript
//Class
class User {
    constructor(){
        this._canEditEverything = false;
    } 
    get canEditEverything() { return this._canEditEverything; }
}//Sub-class
class Administrator extends User {
    constructor() {
        super();
        this._canEditEverything = true;
    }
}//Instatiation
let u2 = new Administrator();
u2.canEditEverything; //true
```

"** Abstract Factory** 패턴은 구체적인 클래스를 지정하지 않고 관련 오브젝트 또는 종속 오브젝트의 _families_ 을 생성하기위한 인터페이스를 제공합니다."

여기서 중요한 점은 "_families_"이라는 단어입니다. 이 예제를 계속 사용하면 ES6에서 "인터페이스"(클래스) 및 "구체적인 클래스"(하위 클래스)로 구현할 수 있습니다. TypeScript에서 (c #에서와 같이) _interface_exists는 정의없이 (구현없이) 클래스가 구현합니다. 그러나 JS (ES6 포함)에서는 그렇지 않습니다. 그래서 우리는 그것을 확장하는 클래스와 하위 클래스를 사용합니다. 그래서 우리는 서브 클래스 "Administrator", "Editor", "Publisher"등을 가질 수 있습니다.

## Registering concrete objects at run-time

지금까지는 그렇게 좋았지 만, 나는 더 유용한 것을 필요로했다. 내가 필요한 것은:

- 실행 시간에 서브 클래스의 등록을 허용하는 Factory. 내 경우에는 코더가 새로운 유형의   사용자 및 앱을 만들어 사용자 인터페이스에서 사용할 수있게해야하므로 필요합니다.
- 이전에 등록 된 모든 새로운 유저 타입을 가진 사용자 제어의 모든 인스턴스에서 사용할 수있는 팩토리. 이것은 간단합니다. Singleton이되어야합니다.

그래서 클래스의 인스턴스를 등록하고 생성하기 위해 정적 메소드가있는 ES6 클래스를 만들었습니다. 다음과 같습니다.

```javascript
class Factory {
    constructor() {} 
    static register(clazzname, clazz) {
        if ((!Factory._registeredTypes.has(clazzname) &&
            clazz.prototype instanceof User)) { 			
	            Factory._registeredTypes.set(clazzname, clazz);	
            ... } 
        else { ... } 
     } 
	static create(clazzname, ...options) { 
		if (!Factory._registeredTypes.has(clazzname)) {
            console.error("!!!");
            return null;
        } 
	    let clazz = this._registeredTypes.get(clazzname);
	    let instance = new clazz(...options);
	    return instance; 
    }
 }
Factory._registeredTypes = new Map();
``` 


두 가지를보십시오. (1) "정적"방법을 사용하고 (2) "Map"를 하단의 속성에 바인딩합니다. ES6에서는 정적 메서드 (인스턴스화 할 필요 없이 사용할수 있으며 인스턴스에는 사용할 수 없고, 인스턴스 this 프로퍼티에 접근할 수 없음)을 가질 수 있습니다. 따라서 (2)에서 그것들을 만들어야합니다.

이 작업은 정상입니다. 그리고 여러분 중 일부는 그것을 자연적인 구현으로 생각할 것입니다. 그러나 나는 그렇지 않습니다. 나는 그것이 instatiated 수있는 것처럼 그것은 사실을 좋아하지 않아. 또는 정적 메서드를 잊어 버리고 라이브러리에서 인스턴스화하고, 논리를 추가하여 Singleton으로 만들 수 있습니다. 네, 그럴 수도 있습니다.

스타일의 문제 일 수도 있지만이 경우에는 클래스 나 함수에 대한 객체를 선호합니다. 그래서 마침내 나는 단순히 객체를 사용했다.

```javascript
const Factory = { 
	registeredTypes: new Map(), 
	register(clazzname, clazz) {
        if (!(Factory.registeredTypes.has(clazzname) &&
            clazz.prototype instanceof User)) { 
	            Factory._registeredTypes.set(clazzname, clazz); 
           } 
        else { ... }
    }, 
    create(clazzname, ...options) { 
	    if (!Factory.registeredTypes.has(clazzname)) {
            console.error("!!!");
            return null;
        }        
        let clazz = this.registeredTypes.get(clazzName);
        let instance = new clazz(...options);
        return instance; 
    }
}
```


즉, 새로운 ES6 라이브러리에서 이를 구현하는 방법을 알아 내려고 2 일이 지난 후에 나는 꽤 표준적인 솔루션으로 나왔습니다!

이전 사례를 기고자로 삼아 아이디어 나 구현을 공유하도록 권유합니다. 당신은 어떻게 할 수 있습니까?

읽고 의견을 주셔서 감사합니다!