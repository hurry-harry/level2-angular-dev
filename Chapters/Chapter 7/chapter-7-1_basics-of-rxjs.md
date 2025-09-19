# Chapter 7.1: Basics of RxJs
Contents:
- What are Observables , and how do we use them?
- What are operators, and how to use them?
- Strategies to unsubscribe from Observables

## Basics of RxJS (Reactive Extensions for JavaScript)
- A library for handling asynchronous operations and event-based programming
- Uses Observables, streams of data that emit values over time
- Uses Operators that allow you to do the following to data:
  - Filter
  - Transform
  - Combine
  - Manipulate data streams

### [What are Observables](https://blog.angulartraining.com/rxjs-observables-in-5-minutes-144abf13cac8?sk=68cdf27b7ca4c6bc9da60470ac17b868)
- Similar to `Promises`
- `Observables` are objects that can be subscribed to for receiving data via the callback.
  - Use the `.subscribe()` method to listen for the callback, it returns a `Subscription` object.
  - Possible callbacks are:
    - To receive the data -> `(data)` below
    - To handle errors -> `(err)` below
    - To receive a notification for when the `Observable` completes (no more data will be emitted)
  - ```
    fs.readFile('<directory>').subscribe(
       (data) => {
        // Code that runs once the directory is read
       },
       (err) => {
          // Code that runs if an error happens
       }
    );
    ```
- `Observables` can return multiple values over time
- `Observables` can be cancelled

### [What are Operators?](https://rxjs.dev/guide/operators)
- `Operators` are functions that can be used to adjust the behaviour of `Observables`
- There are more than 100 `operators` in `RxJs`
  - [Here](https://rxmarbles.com/) is a useful resource on operators
- Use the `.pipe()` method on an observable and pass the operators as parameters to that method and `.pipe()` will return a new observable to `.subscribe()` to
  - ```
    observable.pipe(      
      // We can chain several operators here
      map(data => data.name)
    ).subscribe(name => console.log(name));
    ```
- Key Operator Types: 
  - Transformation operators (`map`, `scan`) modify emitted data values
  - Filtering operators (`filter`, `take`) emit only items meeting specified conditions
  - Combination operators (`merge`, `concat`) merge multiple Observables into one
  - Error handling operators (`catchError`, `retry`) manage errors in Observable sequences
  - Utility operators (`tap`, `delay`) perform side effects and utility functions
- List of more operators [here](https://rxjs.dev/guide/operators) same as the link in the section title
- [Sample project of Observables in use](https://blog.angulartraining.com/dynamic-filtering-with-rxjs-and-angular-forms-a-tutorial-6daa3c44076a)

### [Strategies to unsubscribe from Observables](https://blog.angulartraining.com/dynamic-filtering-with-rxjs-and-angular-forms-a-tutorial-6daa3c44076a)
- Always remember to unsubscribe, and always do it at `ngOnDestroy`
  - This is when the component leaves the DOM, thus ensuring it is when it is no longer needed.
- Strategies for unsubscribing:
  - One by one in the `ngOnDestroy`
    - ```
      ngOnDestroy(): void {
          this.sub1.unsubscribe();
          this.sub2.unsubscribe();
          // etc...
      }
      ```
  - Store your `subscriptions` in an `array` and then at the end, loop through your `array`
    - ```
      ngOnDestroy(): void {
          for (let x = 0; x < this.subsArray.length; x++) {
            this.subsArray[x].unsubscribe();
          }
      }
      ```
  - Use the [SubSink](https://github.com/wardbell/subsink) package
    - It adds a `SubSink` class that stores all of your subscriptions and has a method for unsubscribing from all of them at once.
    - ```
      export class SomeComponent implements OnDestroy {
        private subs = new SubSink();
      
        ...
        // easy syntax
        this.subs.sink = observable$.subscribe(...); 
        // Same job done with add()
        this.subs.add(observable$.subscribe(...));
        // Can add multiple subscriptions too
        this.subs.add( 
          observable$.subscribe(...),
          anotherObservable$.subscribe(...)
        ); 
      
        ...
      
        // Unsubscribe when the component dies
        ngOnDestroy() {
          this.subs.unsubscribe();
        }
      }
      ```
