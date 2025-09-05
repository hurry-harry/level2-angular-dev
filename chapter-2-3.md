# Chapter 2.3: Signals - Lifecycle Hooks - Other tips
Contents:
- [Signals](https://github.com/hurry-harry/level2-angular-dev/blob/main/chapter-2-3.md#signals)
- [Lifecycle Hooks](https://github.com/hurry-harry/level2-angular-dev/blob/main/chapter-2-3.md#lifecycle-hooks)
- [Other Tips](https://github.com/hurry-harry/level2-angular-dev/blob/main/chapter-2-3.md#other-tips)
- [Quiz 4](https://github.com/hurry-harry/level2-angular-dev/blob/main/chapter-2-3.md#quiz-4)
  
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
- Additional info in this section: [input(), output(), viewChild(), contentChild()](https://github.com/hurry-harry/level2-angular-dev/blob/main/chapter-2-1.md#component-selector-and-decorators)
- Inputs: Can be used instead of the `@Input()` decorator.
  - `name = input<string>('Iris'); -> <p>Hello {{name()}}</p>`
  - A benefit is being able to use `computed()` instead of `ngOnInit` or `ngOnChanges` for deriving values
    - ```
      name = input<string>('Nicole');
      greeting = computed(() => 'Hello ' + this.name());
      ```
  - Also works with `required`
    - ```
      firstName = input<string>();          // string | undefined - due to not required and no initial value
      lastName = input.required<string>();  // string
      age = input(0);                       // number             - not required BUT it has an initial value
      ```
- Outputs: Can be used instead of the `@Output()` decorator
  - ```
    // How to create
    nameChange = output<string>();          // OutputEmitterRef<string>
    onClick = output();                     // OutputEmitterRef<void>

    // How to emit values to the consumers
    updateName(newName: string): void {
      this.nameChange.emit(newName);
    }

    // How to subscribe for the changes
    (myOutput)="doSomething($event)"        // template event bindings
    onClick.subscribe(() => {               // programmatically via .subscribe()
      // do something
    });
    ```
- Model: Signal-based data binding
  - Can be one-way or two-way
  - Also allows to specify that a value is required for a property
  - ```
    name = model.required<string>();        // Setting a property that is required
    age = model(27);
    <app-person [age]="age" />              // Lets us pass the age property to the PersonComponent as an input()
    <app-person [(age)]="age" />            // Two-way: This one lets us pass age as both an input() and output()

    <button (click)="name.set('Harry')">
      Change Name
    </button>
    <button (click)="increaseAge()">
      Increase Age
    </button>

    increaseAge(): void {
      this.age.update(() => age + 1);
    }
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

### [ng-template](https://blog.angulartraining.com/what-is-ng-template-and-when-to-use-it-f875b46aa078)
- Under the hood, Angular uses `ng-template` a lot. Like in how it translates `*ngIf` or `*ngFor`
  - `*ngFor` and `*ngIf` were created to be shorthand for the original syntax which uses `ng-template` with params
- `ng-template` are:
  - By default, invisible in the DOM - the block has to be activated by other code
  - Can render any sibling elements without creating additional “wrapper” parent elements - doesn't create additional DOM elements
- Sample usage for when we need to display info based on a condition. Results in a non-intrusive block that doesn't mess up layout or CSS 
  - ```
    <ng-template [ngIf]="user.isLoggedIn">
      <div>
        Welcome to our app, {{userName}}!
      </div>
      <menu>
        Here are your options for today: 
       ...
      </menu>
    </ng-template>
    ```
- Another, more advanced, sample usage is for passing a template to another component
  - Helpful for when the number of templates is variable
  - ```
    <ng-template #userInfo>
      <div>
        Welcome to our app, {{userName}}!
      </div>
      <menu>
        Here are your options for today: 
       ...
      </menu>
    </ng-template>

    <welcome-page [userTemplate]="userInfo" >
    </welcome-page>
    ```

### [ng-container](https://www.angulartraining.com/daily-newsletter/what-is-ng-container/)
- Similar to `ng-template`, you can use `*ngIf` or `*ngFor` on them
  - `<ng-container *ngFor="let item of items">`
- Rendering doesn't create additional DOM elements
- Perfect usage for this is when you need to use both `*ngIf` and `*ngFor`, since you can't use both at the same time on the same element
  - ```
    <ng-container *ngFor="let item of items">
      <li *ngIf="item.isValid">
        {{ item.name }}
      </li>
    </ng-container>
    ```

### [Standalone Components](https://www.angulartraining.com/daily-newsletter/what-are-standalone-components/)
- Covered in [Chapter 1](https://www.angulartraining.com/daily-newsletter/what-are-standalone-components/)

## Quiz 4
- The following syntax does not compile: Which alternative syntax would generate the correct DOM structure as intended below?
  ```
  <div *ngIf="showItems" *ngFor="let item of items">{{item}}</div>
  ```
  1. ```
     <ng-container *ngIf="showItems">
       <div *ngFor="let item of items">{{item}}</div>
     </ng-container>
     ```
  2. ```
     <div [hidden]="! showItems" *ngFor="let item of items">{{item}}</div>
     ```
  3. ```
     <div *ngIf="showItems">
       <span *ngFor="let item of items">{{item}}</span>
     </div>
     ```
  4. ```
     <ng-template *ngIf="showItems">
       <div *ngFor="let item of items">{{item}}</div>
     </ng-template>
     ```
- What is the purpose of the ViewChild decorator in this component example?
  ```
  @Component({
    template: '<p #test></p>'
  })
  export class UserDetailsComponent {
    @ViewChild('test') test;
  }
  ```
  1. It indicates that the tag be rendered as a child of the parent view that uses this component.
  2. It makes the tag in the template support content projection.
  3. It provides access from within the component class to the ElementRef object for the tag that has the test template reference variable.
  4. It makes the tag visible in the final render. If @ViewChild was not used in the class, then Angular would automatically hide the tag that has #test on it.
- Which of the following functions is not a part of the Signals API?
  1. computed()
  2. observable()
  3. signal()
  4. effect()

    
