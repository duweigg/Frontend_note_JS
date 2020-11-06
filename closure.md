# Closures
example code from lexical scoping
```js
var outerFunction  = function(){
 
   if(true){
      var x = 5;
      //console.log(y); //line 1, ReferenceError: y not defined
   }
 
   var nestedFunction = function() {
 
      if(true){
         var y = 7;
         console.log(x); //line 2, x will still be known prints 5
      }
 
      if(true){
         console.log(y); //line 3, prints 7
       }
   }
   return nestedFunction;
}
 
var myFunction = outerFunction();
myFunction();
```
The fact that we can access the variable x might still be confusing, because, normally, a local variable inside a function is gone after a function finishes executing. We called ```outerFunction()``` and assigned its result, ```nestedFunction()```, into ```myFunction()```. How does the variable x still exist if ```outerFunction()``` has already returned?

Merely accessing a variable outside of the immediate scope (no return statement is necessary) will create something called a **closure**. Mozilla Development Network(MDN) gives a great definition:

    “A closure is a special kind of object that combines two things: a function, and the environment in which that function was created. The environment consists of any local variables that were in-scope at the time that the closure was created.”

Since x is a member of the environment that created ```nestedFunction()```, ```nestedFunction()``` will have access to it. Straightforward enough? It’s about to get more interesting, because this doesn’t behave like a regular variable. Try this example, which has nested functions inside of an object with some properties:
```js
var cat = {
 
   name: "Gus",
   color: "gray",
   age: 15,
 
   printInfo: function() {
      console.log("Name:", this.name, "Color:", this.color, "Age:", this.age); //line 1, prints correctly
 
      nestedFunction = function() {
          console.log("Name:", this.name, "Color:", this.color, "Age:", this.age); //line 2, loses cat scope
      }
 
   nestedFunction();
   }
}
cat.printInfo(); //prints Name: window Color: undefined Age: undefined
```
Why are color and age undefined in line 2? You might be thinking, “the cat object properties are clearly defined above and are in an outer more global scope aren’t they?” More importantly, where did “window” come from?

JavaScript loses scope of this when used inside of a function that is contained inside of another function. When it is lost, by default, this will be bound to the global window object. In our example, it just so happens that the window object also has a “name” property with a value of “window”.

in order to solve this, we use call(), apply(), bind()