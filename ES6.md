# ES6 features
## Arrow funcrion
---
The most noticed feature of ES6 The Fat Arrows. Fat arrows allows you to bind the ```this``` context of your current scope with the executing function (lexical scoping, arrow function inherit its parent ```this```). Fat arrows also helps to write less code thanks to its minimal syntax.
```js
// ES5
function returnArgPlusOne (arg) {
  return arg + 1;
}
// ES6
const returnArgPlusOne = arg => arg + 1;
// ES5
var a = [1,2,3,4,5].map(function(elem, index) {
  return elem * 10;
});
let a = [1,2,3,4,5].map((elem, index) => elem * 10);
// OR
let a = [1,2,3,4,5].map((elem, index) => {
  return elem * 10;
});
```
if you just have one parameter you can define parameter without using parenthesis.
and if you don’t specify braces around your functional body the expression will be automatically returned.
```js
const returnArgPlusOne = arg => arg + 1;
```

## Spread Operator
---
They basically lets you expend and use your Object or Array. For Eg
```js
// ES5
var a = [5,2,3];
function plusThree (a, b, c) {
  return a + b + c;
}
plusThree(a[0],a[1],a[2]);
// ES6
let a = [5,2,3];
const plusThree = (a, b, c) => a + b + c
plusThree(...a); // Whoa That was totally cool !!!
// Or merge objects
let user = {
  name: "Manoj",
  age: "19",
  profession: "Javascript Developer"
}
let userModified = {...user, age: "20"} // takes all the properties from user then replace age with 20
```

## Object Deconstruction
---
Object Deconstruction simply lets you deconstruct an Object, extract its properties and use them as local variables. For e.g.
```js
let user = {
  name: "Manoj",
  age: "19",
  profession: "Javascript Developer"
}
let {name, age} = user;
console.log(name, age) // should output "Manoj" and "19"
```

## Default Parameters
---
```js
const defArg (arg = 2, defa = 6) {
  // your computation here
}
```
using togather with object destruction
```js
let user = {
  name: "Manoj",
  age: "19",
  profession: "Javascript Developer"
}
let {name, city = "Chandigarh" } = user;
console.log(name, city) // should output "Manoj" and "Chandigarh"
```

## import & export keywords
---
imports
```js
// ES5
var YourModule = require('./YourModule');
// ES6
import YourModule from './YourModule';
// ES5
var child = require('./YourModule').child;
// ES6
import {child} from './YourModule';
```
exports
```js
// ES5
var child = "toSomething";
module.exports = {
  child: child
}
// ES6
export const child = "toSomething";
// OR
const child = "toSomething";
export default {
  child
}
```