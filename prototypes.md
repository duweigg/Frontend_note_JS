# Prototype

## defination

prototype is a property of a funtion, that references an object.

## OIbject.create
---
```js
function Animal (name, energy) {
  let animal = Object.create(Animal.prototype)
  animal.name = name
  animal.energy = energy

  return animal
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

const leo = Animal('Leo', 7)
const snoop = Animal('Snoop', 10)

leo.eat(10)
snoop.play(5)
```

note that in function Animal, anmial is created useing ```Object.create```, so that it inheritance the Animal.prototype

### How it works?
---
* It takes in an argument that is an object.
* It creates an object that delegates to the argument object on failed lookups.
* It returns the new created object.
```js
Object.create = function (objToDelegateTo) {
  function Fn(){}
  Fn.prototype = objToDelegateTo
  return new Fn()
}
```
## new
---
the ```new``` key word helps us to write ```let this = Object.create(Animal.prototype)``` and ```return this```, so the code above becomes

```js
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}

const leo = new Animal('Leo', 7)
const snoop = new Animal('Snoop', 10)
```

note the ```new``` keyword used here, and ```this``` keyword. (using ```new```, inside function must use ```this```).

## class
---
ltr, with syntax suger, js also has a class, to help OOP.

```js
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep(length) {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
}

const leo = new Animal('Leo', 7)
const snoop = new Animal('Snoop', 10)
```

## more example
---

```js
const friendsWithSugar = []
```
is equivalent to 
```js
const friendsWithoutSugar = new Array()
```
and that is why an array has so many ready to use functions like map, sort

## static
---
Whenever you have a method that is specific to a class itself but doesn’t need to be shared across instances of that class, you can add it as a static property of the class.

```js
class Animal {
  constructor(name, energy) {
    this.name = name
    this.energy = energy
  }
  eat(amount) {
    console.log(`${this.name} is eating.`)
    this.energy += amount
  }
  sleep(length) {
    console.log(`${this.name} is sleeping.`)
    this.energy += length
  }
  static nextToEat(animals) {
    const sortedByLeastEnergy = animals.sort((a,b) => {
      return a.energy - b.energy
    })

    return sortedByLeastEnergy[0].name
  }
}

const leo = new Animal('Leo', 7)
const snoop = new Animal('Snoop', 10)

console.log(Animal.nextToEat([leo, snoop])) // Leo
```

## getPrototypeOf
---
```js
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

Animal.prototype.eat = function (amount) {
  console.log(`${this.name} is eating.`)
  this.energy += amount
}

Animal.prototype.sleep = function (length) {
  console.log(`${this.name} is sleeping.`)
  this.energy += length
}
const leo = new Animal('Leo', 7)
const prototype = Object.getPrototypeOf(leo)

console.log(prototype)
// {constructor: ƒ, eat: ƒ, sleep: ƒ, play: ƒ}

prototype === Animal.prototype // true
```
First, you’ll notice that proto is an object with 4 methods, ```constructor, eat, sleep, and play```. That makes sense. We used ```getPrototypeOf``` passing in the instance, ```leo``` getting back that instances’ prototype, which is where all of our methods are living. This tells us one more thing about prototype as well that we haven’t talked about yet. **By default, the prototype object will have a constructor property which points to the original function or the class that the instance was created from.** What this also means is that because JavaScript puts a ```constructor``` property on the prototype by default, any instances will be able to access their ```constructor``` via ```instance.constructor```.

The second important takeaway from above is that   
```Object.getPrototypeOf(leo) === Animal.prototype```.   
That makes sense as well. The Animal constructor function has a prototype property where we can share methods across all instances and getPrototypeOf allows us to see the prototype of the instance itself.

To tie in what we talked about earlier with ```Object.create```, the reason this works is because any instances of Animal are going to delegate to Animal.prototype on failed lookups. **So when you try to access ```leo.constructor```, leo doesn’t have a constructor property so it will delegate that lookup to Animal.prototype which indeed does have a constructor property.** 

## hasOwnProperty
---
to print out only instance own property, excluding prototype：
```js
const leo = new Animal('Leo', 7)

for(let key in leo) {
  if (leo.hasOwnProperty(key)) {
    console.log(`Key: ${key}. Value: ${leo[key]}`)
  }
}
```

## instanceof
---
```js
function Animal (name, energy) {
  this.name = name
  this.energy = energy
}

function User () {}

const leo = new Animal('Leo', 7)

leo instanceof Animal // true
leo instanceof User // false
```
The way that instanceof works is it checks for the presence of ```constructor.prototype``` in the object’s prototype chain. In the example above, leo instanceof Animal is true because ```Object.getPrototypeOf(leo) === Animal.prototype```. In addition, leo instanceof User is false because Object.getPrototypeOf(leo) !== User.prototype.


## Creating new agnostic constructor functions
---
in case you forget to use ```new``` keyword, when instantiate a function.
```js
function Animal (name, energy) {
  if (this instanceof Animal === false) {
    return new Animal(name, energy)
  }

  this.name = name
  this.energy = energy
}
```

## Arrow Functions
---
Arrow functions don’t have their own this keyword. As a result, arrow functions can’t be constructor functions and if you try to invoke an arrow function with the new keyword, it’ll throw an error.
```js
const Animal = () => {}

const leo = new Animal() // Error: Animal is not a constructor
```
Also, because we demonstrated above that the pseudo-classical pattern can’t be used with arrow functions, arrow functions also don’t have a prototype property.
```js
const Animal = () => {}
console.log(Animal.prototype) // undefined
```