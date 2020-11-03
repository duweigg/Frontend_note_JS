# Lexical Scoping
First off, JavaScript has lexical scoping with function scope. In other words, even though JavaScript looks like it should have block scope because it uses curly braces { }, a new scope is created only when you create a new function.
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
In this example, the variable x is available everywhere inside of ```outerFunction()```. Also, the variable y is available everywhere within the ```nestedFunction()```, but neither are available outside of the function where they were defined. The reason for this can be explained by lexical scoping. **The scope of variables is defined by their position in source code. In order to resolve variables, JavaScript starts at the innermost scope and searches outwards until it finds the variable it was looking for**. Lexical scoping is nice, because we can easily figure out what the value of a variable will be by looking at the code; whereas in dynamic scoping, the meaning of a variable can change at runtime, making it more difficult.