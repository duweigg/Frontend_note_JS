## forEach()
---
Foreach takes a callback function and run that callback function on each element of array one by one.   

For every element on the array we are calling a callback which gets element & its index provided by foreach.    

Basically forEach works as a traditional for loop looping over the array and providing you array elements to do operations on them.

insequence? or out of sequence

## filter()
---
filter executes the callback and check its return value. If the value is true element remains in the resulting array but if the return value is false the element will be removed for the resulting array.

Also take notice filter does not update the existing array it will return a new filtered array every time.

## map()
---
The provided callback to map modifies the array elements and save them into the new array upon completion that array get returned as the mapped array.

## reduce()
---
 reduce method of the array object is used to reduce the array to one single value.

 example
 ```js
var sample = [1, 2, 3] // here we meet again
// es5
var sum = sample.reduce(function(sum, elem){
    return sum + elem;
})
// es6
var sum = sample.reduce((sum, elem) => sum + elem)
console.log(sum)
 ```

 reduce takes a callback ( like every function we talked about ). Inside this callback we get two arguments sum & elem. The sum is the last returned value of the reduce function. For example initially the sum value will be 0 then when the callback runs on the first element it will add the elem to the sum and return that value. On second iteration the sum value will be first elem + 0, on third iteration it will be 0 + first elem + second elem.