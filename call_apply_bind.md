# Call, Apply, Bind
## TL;DR
result = Call({this reference objct}, param1, param2, param3)
result = apply({this reference objct}, [param1, param2, param3])
result function = bind({this reference objct})

## Detail notes
* Call()   
    The first parameter in call() method sets the "this" value, which is the object, on which the function is invoked upon.
    The rest of the parameters are the arguments to the actual function.

    ```js
    //Demo with javascript .call()

    var obj = {name:"Niladri"};

    var greeting = function(a,b,c){
        return "welcome "+this.name+" to "+a+" "+b+" in "+c;
    };

    console.log(greeting.call(obj,"Newtown","KOLKATA","WB"));
    // returns output as welcome Niladri to Newtown KOLKATA in WB
    ```

* Apply()   
    Similarly to call() method the first parameter in apply() method sets the "this" value which is the object upon which the function is invoked. The only difference is that the second parameter of the apply() method accepts the arguments to the actual function as an array.
    ```js
    //Demo with javascript .apply()

    var obj = {name:"Niladri"};

    var greeting = function(a,b,c){
        return "welcome "+this.name+" to "+a+" "+b+" in "+c;
    };

    // array of arguments to the actual function
    var args = ["Newtown","KOLKATA","WB"];  
    console.log("Output using .apply() below ")
    console.log(greeting.apply(obj,args));

    /* The output will be 
    Output using .apply() below
    welcome Niladri to Newtown KOLKATA in WB */
    ```

* Bind()   
    The first parameter to the bind() method sets the value of "this" in the target function when the bound function is called. Please note that the value for first parameter is ignored if the bound function is constructed using the "new" operator.   
    The rest of the parameters following the first parameter in bind() method are passed as arguments which are prepended to the arguments provided to the bound function when invoking the target function.
    ```js
    //Use .bind() javascript

    var obj = {name:"Niladri"};

    var greeting = function(a,b,c){
        return "welcome "+this.name+" to "+a+" "+b+" in "+c;
    };

    //creates a bound function that has same body and parameters 
    var bound = greeting.bind(obj); 


    console.dir(bound); ///returns a function

    console.log("Output using .bind() below ");

    console.log(bound("Newtown","KOLKATA","WB")); //call the bound function

    /* the output will be 
    Output using .bind() below
    welcome Niladri to Newtown KOLKATA in WB */
    ```
    In the above code sample for bind() we are returning a bound function with the context which will be invoked later. We can see the bound function in the console as below .

    ![alt text](https://process.filestackapi.com/cache=expiry:max/HkLkiwSDTSqz9BFRxP59)