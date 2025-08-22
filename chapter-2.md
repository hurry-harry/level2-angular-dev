# Chapter 2: Angular Components
### Component Selector and Decorators
- Angular uses selectors to know when to render a specific component
- [Choosing a selector and using prefixes](https://angular.dev/guide/components/selectors#choosing-a-selector)
  - Components should use a custom element name.
  - Angular has default behaviour where it reports an error if it sees a custom tag name not matching any available component. For preventing typo errors on names.
  - Recommended to use a short and uniform prefix for all custom components. Eg. Building YouTube using Angular would use `yt-`: `yt-menu`, `yt-player`
  - Use an attribute selector when you want to create a custom component on a standard native element.
    - Eg. a custom button can use the `<button>` element -> `selector: 'button[yt-upload]',`
    - This lets the consumer use the standard APIs/features of the element immediately.
  - We can make the selector apply to all specific elements, [eg. buttons](https://www.angulartraining.com/daily-newsletter/the-power-of-angular-selectors/).
    - Attributes can also be passed if we don't want it to be applied to a specific button.
- [Working with `inputs` to pass data](https://angular.dev/guide/components/inputs)
  - New, and recommended, style is signals-based but the old style of using the `@Input()` decorator is still fully supported
    - Signals-based
      - `value = input<number>(50)`
      - `<custom-slider [value]="50" />`
      - ``label = computed(() => `The slider's value is ${this.value()}`)``
      - `value = input.required<number>()`
      - `input()` also accepts secondary parameters such as `transform` and `alias`. Although avoiding `alias` usage is encouraged.
      - Model Input allows the parent to write to the child. If it is two-way binding, any updates in the child will reflect to the parent
        - P:`volume = signal(0);` -> P:``template: `<custom-slider [(value)]="volume" />`,``, -> C:`this.value.update(oldValue => oldValue + 10);`
        - You can read the value by calling the signal function, including in reactive contexts like computed and effect.
        - **NOTE** that binding passes the volume signal instance and not just the value of the signal.
        - `required` and `alias` also work in model input, but `transform` does not
      - Model Outputs are automatically created by Angular in the format of `(input name)Change`
        - Eg. `checked = model(false)` -> and then in the template `(checkedChange)="handler()"`
        - Change events are emitted whenever new values are written into the model input via `set` or `update`
    - Decorator
      - `@Input() value = 0`
      - `<custom-slider [value]="50" />`
      - [Old stuff here](https://angular.dev/guide/components/inputs#declaring-inputs-with-the-input-decorator)   
- [Custom Output Events](https://angular.dev/guide/components/outputs)
  - New signals-based is defined by assigning a property to the `output` function
    - `panelClosed = output<void>();` -> `<expandable-panel (panelClosed)="savePanelState()" />`
    - `output()` returns an `OutputEmitterRef`
    - Calling the `emit()` method on the `OutputEmitterRef` will trigger an event
      - `this.panelClosed.emit();`
      - You can emit primitive values or custom objects
      - Access the event data using `$event` -> `<custom-slider (valueChanged)="logValue($event)" />`
      - You can also assign an `alias` like in `input` and also not encouraged
      - `OutputRef` has `subscribe()` for subscribing programmatically to output events
        - `someComponentRef.instance.someEventProperty.subscribe(eventData => { console.log(eventData); });`
        - `Unsubcribe` is automatically done when the component is destroyed but can also be done manually `eventSubscription.unsubscribe();` 
    - **Always use camelCase for the output name**
  - [The old `@Output` decorator way is still supported but recommended to use the signals based](https://angular.dev/guide/components/outputs#declaring-outputs-with-the-output-decorator)
  - `outputFromObservable` allows an `output` to emit based on an RxJS observable
    - Eg. `pointerMoves$: Observable<PointerMovements> = listenToPointerMoves();` -> `pointerMove = outputFromObservable(this.pointerMoves$);`
  - `outputToObservable` allows an `output` to emit an `observable`
    - Eg. `class CustomSlider { valueChange = output<number>() }` -> `const slider: CustomSlider = createSlider();` -> `outputToObservable(slider.valueChange).pipe(...).subscribe(...);`
      - `Observable<number>`
- [Viewing Queries (viewChild)](https://angular.dev/guide/components/queries#view-queries)
  - Used to get references to child components, directives, DOM elements, and etc
    - ```
      @Component({
        selector: 'custom-card-header',
        /*...*/
      })
      export class CustomCardHeader {
        text: string;
      }
      
      @Component({
        selector: 'custom-card',
        template: '<custom-card-header>Visit sunny California!</custom-card-header>',
      })
      export class CustomCard {
        header = viewChild(CustomCardHeader);
        headerText = computed(() => this.header()?.text);
      }
      ```
      - `CustomCard` queries for a `CustomCardHeader` component and uses the result in the call to `computed()`
        - `undefined` is returned if no result is found. Can happen if the element is blocked by an `@if`
