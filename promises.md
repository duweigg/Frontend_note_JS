# Promise
**A promise is an object that may produce a single value some time in the future**, either a resolved value, or a reason that it’s not resolved A promise may be in one of 3 possible states: 
fulfilled, ```onFulfilled()``` will be called (e.g., resolve() was called)
rejected, ```onRejected()``` will be called (e.g., reject() was called)
pending. not yet fulfilled or rejected
Promise users can attach callbacks to handle the fulfilled value or the reason for rejection.

Promises are **eager**, meaning that a promise will start doing whatever task you give it as soon as the promise constructor is invoked. If you need **lazy**, check out ```observables``` or ```tasks```.

Once settled, a promise can not be resettled. Calling resolve() or reject() again will have no effect. The immutability of a settled promise is an important feature.

## example
---
Here is a function that returns a promise which will resolve after a specified time delay:
```js
const wait = time => new Promise((resolve) => setTimeout(resolve, time));

wait(3000).then(() => console.log('Hello!')); // 'Hello!'
```

## rules
---
Promise Rules
* A promise or “thenable” is an object that supplies a standard-compliant ```.then()``` method.
* A pending promise may transition into a fulfilled or rejected state.
* A fulfilled or rejected promise is settled, and must not transition into any other state.
* Once a promise is settled, it must have a value (which may be undefined). That value must not change.

```js
promise.then(
  onFulfilled?: Function,
  onRejected?: Function
) => Promise
```

then
* Both onFulfilled() and onRejected() are optional.
* If the arguments supplied are not functions, they must be ignored.
* ```onFulfilled()``` will be called after the promise is fulfilled, with the promise’s value as the first argument.
* ```onRejected()``` will be called after the promise is rejected, with the reason for rejection as the first argument. The reason may be any valid JavaScript value, but because rejections are essentially synonymous with exceptions, I recommend using Error objects.
* Neither ```onFulfilled()``` nor ```onRejected()`` may be called more than once.
* ```.then()``` may be called many times on the same promise. In other words, a promise can be used to aggregate callbacks.
* ```.then()``` must return a new promise, ````promise2```.
* If ```onFulfilled()``` or ```onRejected()``` return a value x, and x is a promise,  ```promise2``` will lock in with (assume the same state and value as) x. Otherwise, ```promise2``` will be fulfilled with the value of x.
* If either ```onFulfilled``` or ```onRejected``` throws an exception e, ```promise2``` must be rejected with e as the reason.
* If ```onFulfilled``` is not a function and promise1 is fulfilled, promise2 must be fulfilled with the same value as promise1.
* If ```onRejected``` is not a function and promise1 is rejected, promise2 must be rejected with the same reason as promise1.

## Error Handling
---
to catch both error from ```save()``` and ```handleSuccess()```, adding a ```.catch()``` at the end.
```js
save()
  .then(handleSuccess)
  .catch(handleError)
;
```

## Cancellation
---
* Reject the cancel promise by default — we don’t want to cancel or throw errors if no cancel promise gets passed in.
* Remember to perform cleanup when you reject for cancellations.
* Remember that the onCancel cleanup might itself throw an error, and that error will need handling, too. (Note that error handling is omitted in the wait example above — it’s easy to forget!)

simple timer example
```js
const wait = (
  time,
  cancel = Promise.reject()
) => new Promise((resolve, reject) => {
  const timer = setTimeout(resolve, time);
  const noop = () => {};

  cancel.then(() => {
    clearTimeout(timer);
    reject(new Error('Cancelled'));
  }, noop);
});

const shouldCancel = Promise.resolve(); // Yes, cancel
// const shouldCancel = Promise.reject(); // No cancel

wait(2000, shouldCancel).then(
  () => console.log('Hello!'),
  (e) => console.log(e) // [Error: Cancelled]
); 
```
more general example
```js
// HOF Wraps the native Promise API
// to add take a shouldCancel promise and add
// an onCancel() callback.
const speculation = (
  fn,
  cancel = Promise.reject() // Don't cancel by default
) => new Promise((resolve, reject) => {
  const noop = () => {};

  const onCancel = (
    handleCancel
  ) => cancel.then(
      handleCancel,
      // Ignore expected cancel rejections:
      noop
    )
    // handle onCancel errors
    .catch(e => reject(e))
  ;

  fn(resolve, reject, onCancel);
});
```
```js
import speculation from 'speculation';

const wait = (
  time,
  cancel = Promise.reject() // By default, don't cancel
) => speculation((resolve, reject, onCancel) => {
  const timer = setTimeout(resolve, time);

  // Use onCancel to clean up any lingering resources
  // and then call reject(). You can pass a custom reason.
  onCancel(() => {
    clearTimeout(timer);
    reject(new Error('Cancelled'));
  });
}, cancel); // remember to pass in cancel!

wait(200, wait(500)).then(
  () => console.log('Hello!'),
  (e) => console.log(e)
); // 'Hello!'

wait(200, wait(50)).then(
  () => console.log('Hello!'),
  (e) => console.log(e)
); // [Error: Cancelled]
```