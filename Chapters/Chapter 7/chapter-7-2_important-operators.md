# Chapter 7.2: Important Operators for RxJs
Contents:
- `map` operator
- `filter` operator
- `tap` operator
- `switchMap` operator
- `combineLatest` operator
- Error handling in RxJS

## Common operators include map, filter, switchMap, and catchError
- By using RxJS operators we can write more concise, declarative, and maintainable code for handling complex asynchronous scenarios
- "Marble diagrams" are a visual way of seeing what and how RxJs operators handle data.
  - [RXMarbles] is a good resource on this
  - [Here](https://www.angulartraining.com/daily-newsletter/how-to-learn-more-about-rxjs-operators/) is an article on how to use RXMarbles

### [`map` Operator](https://rxjs.dev/api/operators/map)
- `map` passes/applies the received values to the function inside
- ```
  // received values
  // 1 , 2 , 3

  map(x => 10 * x)

  // emitted values
  // 10 , 20 , 30
  ```

### [`filter` Operator](https://rxjs.dev/api/index/function/filter)
- `filter` evaluates the received value and it will only emit values that satisfy the predicate function inside
  - The predicate function must evaluate or return `true` for the value to be emitted
- ```
  // received values
  // 0 , 1 , 2 , 3 , 4

  map(x => x % 2 === 1)

  // emitted values, only 1 and 3 have a result of 1 when modulo-ed by 2
  // 1 , 3
  ```

### [`tap` Operator](https://www.angulartraining.com/daily-newsletter/rxjs-tap-operator/)
- `tap` doesn't actually do anything to the values coming in to the observable
- It is instead useful for:
  - Debugging, by using `console.log()` inside
  - Watching for completion of inner observables when you’re combining several observables into one subscription
  - Registering side effects as the data changes
- More examples [here](https://blog.angulartraining.com/three-reasons-to-use-the-tap-operator-from-rxjs-6828fcf288ec)

### [`switchMap` Operator](https://www.angulartraining.com/daily-newsletter/rxjs-switchmap-operator/)
- **Avoid nested subscribes.**
- Use `switchMap()` instead. This can chain observables together and ensures they happen in sequence one after another.
- Unsubscribing from `observable1` also automatically unsubscribes from other observables in the chain.
- ```
  observable1.pipe(data1 => {
    switchMap(data1 => observable2)
  ).subscribe(data2 => {
    // do something with data2
  });
  ```
- [Sample project using `switchMap()`](https://stackblitz.com/edit/at-rxjs-demo7?file=src%2Fapp%2Fexchange-rate.service.ts)

### [`combineLatest` Operator](https://rxjs.dev/api/index/function/combineLatest)
- `combineLatest()` merges multiple observables and will emit an array containing the latest value from each observable whenever any of the input observables emit a new value.
- Always provides the most recent value from each source
- Any input Observable emitting causes a new combined output
- Returns values as an array in the same order as input Observables
- Common use cases:
  - Combining form field values for validation
  - Merging multiple API responses
  - Synchronizing state from different sources
  - Creating derived data from multiple streams
- `combineLatest()` is particularly useful when you need to react to changes from multiple sources and always have the most current data from each.
- ```
  const email$ = of('user@example.com');
  const password$ = of('password123');
  
  combineLatest([email$, password$]).subscribe(([email, password]) => {
    // Both email and password are available
    console.log('Login data ready:', { email, password });
  });
  ```

### [Error Handling in RxJs](https://www.angulartraining.com/daily-newsletter/error-handling-in-rxjs/)
- You can use the `catchError()` operator
- This returns a new observable when an error happens.
- Allows the subscriber to not know that something wrong happened as we are able to switch to a backup solution in the new observable (eg. cached data or a different API endpoint)
- Good option to recover or at least attempt to recover from an error in the browser
