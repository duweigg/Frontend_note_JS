# Arrow Function

## Arrow Function Syntax
---
Arrow functions have a single overarching structure, and then an number of ways they can be simplified in special cases.

The core structure looks like this:
```js
(argument1, argument2, ... argumentN) => {
  // function body
}
```
A list of arguments within parenthesis, followed by a 'fat arrow' (=>), followed by a function body.

This is very similar to traditional functions, we just leave off the function keyword and add a fat arrow after the arguments.

However, there are a number of ways to 'sugar' this up that make arrow functions dramatically more concise for simple functions.

First, if the function body is a single expression, you can leave off the brackets and put it inline. The results of the expression will be returned by the function. For example:
```js
const add = (a, b) => a + b;
```
Second, if there is only a single argument, you can even leave off the parenthesis around the argument. For example:
```js
const getFirst = array => array[0];
```
As you can see, this can lead to some very concise syntax, which we'll highlight more benefits of later.

## Advanced Syntax
---
There are a few pieces of advanced syntax that are useful to know.

First, if you're attempting to use the inline, single-expression syntax but the value you're returning is an object literal. You might think this would look like:
```js
(name, description) => {name: name, description: description};
```
The problem is that this syntax is ambiguous - it looks as though you're trying to create a traditional function body.

To indicate that instead you want a single expression that happens to be an object, you wrap the object with parentheses:
```js
(name, description) => ({name: name, description: description});
```

## Enclosing Scope Context
---
Unlike every other form of function, **arrow functions do not have their own execution context**.

Practically, this means that **both ```this``` and arguments are inherited from their parent function**.

For example, compare the following code with and without arrow functions:
```js
const test = {
  name: 'test object',
  createAnonFunction: function() {
    return function() {
      console.log(this.name);
      console.log(arguments);
    };
  },

  createArrowFunction: function() {
    return () => {
      console.log(this.name);
      console.log(arguments);
    };
  }
};
```
If we run these in a console with the same arguments however, we get very different results.
```js
> const anon = test.createAnonFunction('hello', 'world');
> const arrow = test.createArrowFunction('hello', 'world');

> anon();
undefined
{}

> arrow();
test object
{ '0': 'hello', '1': 'world' }
```
The anonymous function has its own function context, so when you call it there is no reference available to the ```this.name``` of the test object, nor to the arguments called in creating it.

The arrow function, on the otherhand, has the exact same function context as the function that created it, giving it access to both the argumetns and the test object.

## Where Arrow Functions Improve Your Code
---
One of the primary usecases for traditional lambda functions, and now for arrow functions in JavaScript, is for functions that get applied over and over again to items in a list.

For example, if you have an array of values that you want to transform using a map, an arrow function is ideal:
```js
const words = ['hello', 'WORLD', 'Whatever'];
const downcasedWords = words.map(word => word.toLowerCase());
```
An extremely common example of this is to pull out a particular value of an object:
```js
const names = objects.map(object => object.name);
```
Similarly, when replacing old-style ```for``` loops with modern iterator-style loops using ```forEach```, the fact that arrow functions keep ```this``` from the parent makes them extremely intuitive.
```js
this.examples.forEach(example => {
  this.runExample(example);
});
```
* Promises and Promise Chains
    Another place arrow functions make for cleaner and more intuitive code is in managing asynchronous code.

    Promises make it far easier to manage async code (and even if you're excited to use async/await, you should still understand promises which is what async/await is built on top of!)

    However, while using promises still requires defining functions that run after your asynchronous code or call completes.

    This is an ideal location for an arrow function, especially if your resulting function is stateful, referencing something in your object. Example:
    ```js
    this.doSomethingAsync().then((result) => {
    this.storeResult(result);
    });
    ```
* Object Transformations
    Another common and extremely powerful use for arrow functions is to encapsulate object transformations.

    For example, in Vue.js there is a common pattern for including pieces of a Vuex store directly into a Vue component using mapState.

    This involves defining a set of "mappers" that will transform from the original complete state object to pull out exactly what is necessary for the component in question.

    These sorts of simple transformations are an ideal and beautiful place to utilize arrow functions. Example:
    ```js
    export default {
    computed: {
        ...mapState({
        results: state => state.results,
        users: state => state.users,
        });
    }
    }
    ```

## Where You Should Not Use Arrow Functions
---
There are a number of situations in which arrow functions are not a good idea. Places where they will not only help, but cause you trouble.

The first is in methods on an object. This is an example where function context and ```this``` are exactly what you want.

There was a trend for a little while to use a combination of the Class Properties syntax and arrow functions as a way to create "auto-binding" methods, e.g. methods that could be used by event handlers but that stayed bound to the class.

This looked something like:
```js
class Counter {
  counter = 0;

  handleClick = () => {
    this.counter++;
  }
}
```
In this way, even if handleClick were called with by an event handler rather than in the context of an instance of ```Counter```, it would still have access to the instance's data.

The downsides of this approach are multiple, documented well in [this post](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1).

While using this approach does give you an ergonomic-looking shortcut to having a bound function, that function behaves in a number of ways that are not intuitive, inhibiting testing and creating problems if you attempt to subclass/use this object as a prototype.

Instead, use a regular function and if necessary bind it the instance in the constructor:
```js
class Counter {
  counter = 0;

  handleClick() {
    this.counter++;
  }

  constructor() {
    this.handleClick = this.handleClick.bind(this);
  }
}
```
* Deep Callchains   
    Another place where arrow functions can get you in trouble is when they are going to be used in many different combinations, particularly in deep chains of function calls.

    The core reason is the same as with anonymous functions - they give really bad stacktraces.

    This isn't too bad if your function only goes one level down, say inside of an iterator, but if you're defining all of your functions as arrow functions and calling back and forth between them, you'll be pretty stuck when you hit a bug and just get error messages like:
    ```js
    {anonymous}()
    {anonymous}()
    {anonymous}()
    {anonymous}()
    {anonymous}()
    ```
* Functions With Dynamic Context   
    The last situation where arrow functions can get you in trouble is in places where ```this``` is bound dynamically.

    If you use arrow functions in these locations, that dynamic binding will not work, and you (or someone else working with your code later) may get very confused as to why things aren't working as expected.

    Some key examples of this:

    * Event handlers are called with ```this``` set to the event's ```currentTargetattribute```.
    * If you're still using jQuery, most jQuery methods set ```this``` to the dom element that has been selected.
    * If you're using Vue.js, methods and computed functions typically set ```this``` to be the Vue component.   

    Certainly you can use arrow functions deliberately to override this behavior, but especially in the cases of jQuery and Vue this will often interfere with normal functioning and leave you baffled why code that looks the same as other code nearby is not working.