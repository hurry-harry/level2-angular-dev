# Chapter 2.3: Signals - Lifecycle Hooks - Other tips
Contents:
- Signals
- Lifecycle Hooks
- Other Tips
  
## Signals - A new way to handle data
- Easier to learn than RxJs, lightweight, and they enable a new type of change detection
- Signals are wrappers around values that notifies interested consumers when the value changes
- Signals can be primitive types or complex data structures
- Read a signals value by calling its getter function
- Can be writable or read-only
- [More info](https://medium.com/@gurunadhpukkalla/angular-signals-the-complete-guide-d7585f44addd)

### [Creating a signal and setting its value](https://angular.dev/guide/signals#writable-signals)
- Created using `signal(value)`
  - ```
    const count = signal(0);                 // Set
    console.log('The count is: ' + count()); // Get
    count.set(3)                             // To change the value
    count.update(value => value + 1);        // To change the value based on the previous value
    ```

### [computed()](https://www.angulartraining.com/daily-newsletter/signals-computed/)
- Emits a new signal value when one or more signals involved get updated.
- ```
  const firstName() = signal('Harry');
  const name = computed<string>(() => `${firstName()}}`);
  console.log(name());                                                          // Outputs -> "Harry"

  firstName.set('Iris');
  console.log(name());                                                          // Outputs -> "Iris"
  ```

### [effect()](https://www.angulartraining.com/daily-newsletter/signals-effect/)
- Similar to `computed()`, except this one doesn't return or emit anything.
- Useful for debugging, logging, or running code that doesn't need to update other signals.
- `effect(() => console.log('Count changed', this.count()));`
- Updating other signals inside an `effect()` is not allowed by default to avoid possible infinite loops by signals triggering each other
  - ```
    effect(
      () => console.log('Count changed', this.count()),
      { allowSignalWrites: true }                        // Default behaviour can be overridden by adding this option
    );
    ```

### [RxJs and Signals interoperability](https://www.angulartraining.com/daily-newsletter/rxjs-and-signals-interoperability/)
- `toObservable()` - takes a signal and returns an observable
  - The observable gets updated everytime the signal gets updated
  - ```
    count = signal(0);
    count$ = toObservable(this.count);
    -------
    <div>{{count$ | async}}</div>       // Subscribe to it using the async pipe
    ```
- `toSignal()` - takes a observable and returns a signal
  - By default `toSignal()` returns a signal that supports `undefined` as the default value
  - ```
    count: Signal<undefined | number> = toSignal(this.count$);           // Default
    count: Signal<number> = toSignal(this.count$, { initialValue: 0 });  // You can change this by including an initial value
    ```
  
### [Signals-based Components Tutorial](https://blog.angulartraining.com/angular-signal-based-components-tutorial-4e4b4b1dfa96)
- The link above contains info on how to create a component that is signals-based, or convert a previous component to be signals-based

## Lifecycle Hooks
- Allows the notification of when a component/directive reaches a specific lifecycle state

### [Lifecycle of Angular Applications](https://www.angulartraining.com/daily-newsletter/lifecycle-of-angular-applications/)
- A component is loaded and instantiated in memory when it is displayed on the screen.
- It is destroyed when removed from the screen, eg. because of navigation, `ngIf` directive, etc.
- **NOTE:** Services are different. They are created when needed for the first time, and that unique instance is used by all components that inject it. It remains in memory as long as the app is open and you don't close the tab and window.

### [ngOnInit](https://www.angulartraining.com/daily-newsletter/ngoninit-lifecycle-hook/)
- Only ever runs once, when all `@Input()` have received their initial values.
- Runs **after** the class constructor.
- Used to handle additional initialization tasks.
- Typically used for additional code that uses values from `@Input()` properties. 
  - Contrary to popular thinking that the purpose of `ngOnInit()` is to run all initialization tasks.
  - You can instead use the constructor to initialize earlier.
 
### [ngOnChanges](https://www.angulartraining.com/daily-newsletter/ngonchanges-lifecycle-hook/)
- Called whenever the value of any of the component's `input` properties changes.
- Useful for updating the component's state or view as a response to the change in the `input` properties.
- Common usage is for notifying when an ID changes which triggers an API Request call via the relevant service.
- ```
  export class HiComponent implements OnChanges {
  @Input() name!: string;

  ngOnChanges(changes: SimpleChange) {
    console.log(changes);
  }
  -------------------------------------------------
  SAMPLE OUTPUT - the changes parameter, of type SimpleChange with the following properties
  name: SimpleChange
    currentValue: 'Iris'        - The value of the property after it changed.
    firstChange: true           - Boolean value, true if it’s the first change made to that input, false otherwise.
    previousValue: undefined    - The value of the property before it changed.
  ```
  - Whenever the parent component updates the value of `name`, the `ngOnChanges` method will run
  
### [ngOnDestroy](https://www.angulartraining.com/daily-newsletter/ngondestroy-lifecycle-hook/)
- Called whenever the component/directive/pipe is destroyed.
- 'Destroyed' typically means when the item is removed from the DOM or the app itself is destroyed/closed.
  - When an `ngIf` hides the element
  - Router navigation to another view
- Typically used for doing the following, helping prevent memory leaks:
  - Unsubscribing from observables
  - Closing sockets
  - Cancelling `setInterval()` or `setTimeout()` tasks
 
## Other Topics

### [Signal-based equivalents of query decorators](https://www.angulartraining.com/daily-newsletter/viewchild-and-contentchild-for-signal-based-queries/)
- `viewChild()` - a way to get a reference on an HTML element in the current components template. Giving you access to a child component, and its public properties, within the TypeScript space
  - ```
    listComponent: Signal<ListComponent | undefined> = viewChild(ListComponent);
    this.listComponent().items = "";                                               // Sample usage, accessing the `items` property in ListComponent
    ```
- `contentChild()` - looks for an element projected by the parent component into the ng-content element of the current component using [content projection](https://www.angulartraining.com/daily-newsletter/passing-custom-content-to-a-component-with-content-projection/).
  - Can be thought of as being able to look for an element either directly in the current component's template, but also in the template of any child components in the current template. Like the template of the child projects its contents to the template of the current component.
  - `infoContent = contentChild<ElementRef>('test');`
- Can make use of `required` to get rid of undefined values
  - ```
    listComponent = viewChild.required(ListComponent);
    length = computed(() => this.listComponent().items.length);
    ```

### Signal-based functions for [inputs](https://www.angulartraining.com/daily-newsletter/whats-new-in-angular-17-1/), [outputs](https://angular.dev/api/core/output?tab=usage-notes), and [model](https://www.angulartraining.com/daily-newsletter/model-for-signal-based-2-way-data-bindings/)
- Inputs
  - 
