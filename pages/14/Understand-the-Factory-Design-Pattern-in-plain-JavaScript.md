# Understand the Factory Design Pattern in plain JavaScript

- 원문 : https://medium.com/front-end-weekly/understand-the-factory-design-pattern-in-plain-javascript-20b348c832bd

Common way to do prototypical inheritance is like this

```javascript
const Animal = function(name) {
  this.name = name;
}
Animal.prototype.walk = function() {
  console.log(this.name + " walks");
}
```

and then we can do this

```javascript
poo = new Animal("poo")
tommy = new Animal("tommy")
poo.walk()
tommy.walk()
```

This has some problems, one is while developing we keep forgetting to use new keyword and the interpreter doesn’t complain so it silently fails. Doing inheritance with this method is also cubersome, and don’t get me started about the trouble with this.

One alternate way is to use objects

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

However I personally favor a different pattern called the Factory Design Pattern.

In a nutshell, if we implement instance creation inside the function itself and don’t require user to use Object.create or new keyword, then we have a factory.

Let’s look at this in greater detail.

## Factory design pattern

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

and then we can do this

```javascript
const poo = Animal("poo");
const tommy = Animal("tommy");
poo.walk() // poo walks
tommy.walk() // tommy walks
```

As you can see we just call the function Animal and both times it gives us a new animal instance.

But these animals our fairly limited in functionality. They can just walk right now. What should we do to add functionality in these animals.

Enter Mixins

To further add functionality we use mixins which is just a fancy way of describing an object which doesn’t have state itself but has some methods attached to it like this.

```javascript
const canKill = {
  kill() {
    console.log("I can kill")
  }
}
```

Now if we only need one animal which can also kill we can do this

```javascript
k1 = Object.assign(Animal("k1"), canKill)
```

However if we need multiple killing animals we can create a factory for this also.

```javascript
const KillingAnimal = function(name) {
  const animal = Animal(name)
  const killingAnimal = Object.assign(animal, canKill)
  return killingAnimal;
}
```

and use it like so

```javascript
k2 = KillingAnimal("k2")
monty = KillingAnimal("monty")
k2.kill()
k2.walk()
```

We can have multiple mixins, for example suppose we need

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

Now, let’s look at composing two factories together.

Let’s say apart from Animal factory we have a Robot factory

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

Now we can make many robots. But say we need an Animal Robot. No problem we can compose the two factories to get an animal robot.

```javascript
roboDog = Object.assign(Animal("roboDog"), Robot("roboDog"))
```

Now if we want a robot dog which can kill we can do

```javascript
killingRoboDog = Object.assign({}, roboDog, canKill)
killingRoboDog.walk()
killingRoboDog.kill()
killingRoboDog.speak("roboDog will kill you")
```

As before we can make a factory for producing Animal Robots also, if we need an army of Animal Robots.

Hope, I made this clear enough, do comment if you have any queries or if you have some suggestions.

To read in more detail about factories, check out the very genius Eric Elliott ‘s article https://medium.com/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1

If you liked this article follow me on Twitter and Medium or subscribe to my newsletter!