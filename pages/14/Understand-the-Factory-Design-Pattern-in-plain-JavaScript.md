# 순수 자바스크립트에서 팩토리 디자인 패턴의 이해

- 원문 : https://medium.com/front-end-weekly/understand-the-factory-design-pattern-in-plain-javascript-20b348c832bd

## 용어 사전 정의 (영->한)

---

- Javascript : 자바스크립트
- plain : 단조로운, 뽐내지 않는와 같은 뜻을 지니고 있어 순수로 번역
- Factory Design Pattern : 팩토리디자인패턴
- interpreter : [인터프리터](http://www.terms.co.kr/interpreter.htm)
- [cumbersome](https://ko.wiktionary.org/wiki/cumbersome) : 성가신, 부담스러운
- object : 객체
- In a nutshell : 간단히 말해
- instance : 인스턴스
- function : 함수
- keyword : 키워드
- Mixin : 믹스인

---

프로토 타입 상속을 구현 하기 위해 일반적으로 아래와 같은 방법으로 작성합니다.

```javascript
const Animal = function(name) {
  this.name = name;
}
Animal.prototype.walk = function() {
  console.log(this.name + " walks");
}
```

그러고나서 우리는 아래의 코드를 작성하여 사용합니다.

```javascript
poo = new Animal("poo")
tommy = new Animal("tommy")
poo.walk()
tommy.walk()
```

위와 같은 방법은 몇가지 문제점을 가지고 있습니다. 한가지는 개발하는 동안에 new 키워드를 사용하는 것을 잊게 되고 인터프리터는 해석하지 않아서 오류가 발생합니다. this 메서드로 상속을 구현하는 것 부담스러운 일입니다. 그리고 this와 함께 이 문제를 해결하려 하지 않습니다.

한 가지 다른 방법은 객체를 사용하는 것입니다.

```javascript
const Animal = {
  name: "not_given_yet", //remove this if you like
  walk() {
    console.log(this.name + " walks");
  }
}
const poo = Object.assign({}, Animal, {name: "poo"})
poo.walk()
or
const tommy = Object.create(Animal) //this sets up prototype chain
tommy.name = "tommy"
tommy.walk()
```

하지만 팩토리 디자인 패턴이라 불리우는 다른 패턴을 선호합니다.

간단히 말해, 함수 내에서 인스턴스 생성을 구현하고, 사용자를 Object.create 또는 new 키워드를 사용할 필요가 없는 경우 팩토리를 사용합니다.

좀 더 상세히 설명하도록 하겠습니다.

## 팩토리 디자인 패턴

```javascript
const Animal = function(name){
    const animal = {};
    animal.name = name;
    animal.walk = function(){
        console.log(this.name + " walks");
    }
    return animal;
};
```

위의 코드를 작성하고나면, 아래의 코드에서 처럼 사용합니다.

```javascript
const poo = Animal("poo");
const tommy = Animal("tommy");
poo.walk() // poo walks
tommy.walk() // tommy walks
```

보시다시피 Animal 함수가 호출이 되고, 두 번 모두 새로운 animal 인스턴스를 반환합니다.

그러나 animal은 함수적으로는 제한이 있습니다. poo, tommy 인스턴스는 당장 walk 함수를 사용할 수 있습니다. 이 animal의 인스턴스들에 함수를 추가하려면 무엇을 해야 할까요?

믹스인을 사용하자

함수를 더 추가하기 위해 믹스인을 사용합니다. 믹스인은 자체적으로 상태가 존재하지 않지만, 아래의 코드에서 객체에 메서드를 추가하는 방법은 좋은 방법 입니다.

```javascript
const canKill = {
  kill() {
    console.log("I can kill")
  }
}
```

이제 kill 함수가 추가 됨으로써, animal은 kill 함수를 사용할 수 있습니다.

```javascript
k1 = Object.assign(Animal("k1"), canKill)
```

그러나 우리는 여러번 canKill이라는 함수를 필요로 한다면, 아래의 코드에서 처럼 이를 위한 팩토리를 생성할 수 있습니다.

```javascript
const KillingAnimal = function(name) {
  const animal = Animal(name)
  const killingAnimal = Object.assign(animal, canKill)
  return killingAnimal;
}
```

아래와 같은 방법으로 사용합니다.

```javascript
k2 = KillingAnimal("k2")
monty = KillingAnimal("monty")
k2.kill()
k2.walk()
```

예를 들어 여러개의 믹스인을 필요할 수 있습니다.

```javascript
const canFly = {
  fly(h) {
    console.log("I can fly " + h "meters high")
  }
}
const canDance = {
  dance() {
    console.log("Look Maa !! I'm dancing")
  }
}
brian = Object.assign(Animal("brian"), canKill, canFly, canDance)
brian.walk()
brian.fly(200)
brian.kill()
brian.dance()
```

두가지 팩토리를 합칠 수 있습니다.

Animal 팩토리를 제외하고, Robot 팩토리가 있다고 가정해봅시다.

```javascript
const Robot = function(name) {
  const robot = {}
  robot.name = name;
  robot.speak = function(message) {
    console.log(robot.name + " speaks " + message)
  }
  return robot;
}
```

많은 로봇을 만들 수 있습니다. 그러나 Animal Robot을 필요로 합니다. animal, robot을 얻기 위해 두가지 팩토리를 합치는 것은 문제가 없습니다.

```javascript
roboDog = Object.assign(Animal("roboDog"), Robot("roboDog"))
```

만약 robotDog가 canKill을 필요로 한다면 아래와 같이 코드를 작성합니다.

```javascript
killingRoboDog = Object.assign({}, roboDog, canKill)
killingRoboDog.walk()
killingRoboDog.kill()
killingRoboDog.speak("roboDog will kill you")
```

Animal Robot이 필요로 한다면, 이전에 로봇 팩토리를 만들 수 있습니다.

충분히 이해하길 원하고 있고, 문의 사항, 의견이 있으면 있으면 댓글로 달아주세요.

팩토리에 대해서 더 자세히 알고 싶다면,[Eric Elliott의 글](https://medium.com/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1)을 읽으면 됩니다.