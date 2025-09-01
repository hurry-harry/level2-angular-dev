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
    - Multiple results are also possible via `viewChildren()` returning a signal with an Array of the query results.
  - If the element you want is nested inside the template of the component, use content queries - `contentChild()`
    - ```
        @Component({
          selector: 'custom-toggle',
          /*...*/
        })
        export class CustomToggle {
          text: string;
        }
        @Component({
          selector: 'custom-expando',
          /*...*/
        })
        export class CustomExpando {
          toggle = contentChild(CustomToggle);
          toggleText = computed(() => this.toggle()?.text);
        }
        @Component({ 
          /* ... */
          // CustomToggle is used inside CustomExpando as content.  
          template: `
            <custom-expando>
              <custom-toggle>Show</custom-toggle>
            </custom-expando>
          `
        })
        export class UserProfile { }
      ```
    - Returns `undefined` if no result is found.
    - Use `contentChildren()` for multiple results
  - Use `required` so that Angular will report an error if no result is found
  - The locator is always the first query parameter of the query
    - ```
      <button #save>Save</button>
      <button #cancel>Cancel</button>
      --
      saveButton = viewChild<ElementRef<HTMLButtonElement>>('save');
      ```
    - It will return the first matching element in cases where there are multiple matches
  - For `viewChild/ren()` and `contentChild()`, children templates are accessible by default. For `contentChildren()` use the `descendants: true` parameter to have the same results.

### Expressions and Data Bindings
- [Binding links the component template and the data so that the template remains updated](https://angular.dev/guide/templates/binding)
- Binding static and dynamic text interpolation
  - Static
    - ```
      <p>Your color preference is {{ theme }}.</p>
      --
      theme = 'dark';
      ```
  - Dynamic or bindings that will change should read values via `signals`
    - ```
      <p>Your color preference is {{ theme() }}.</p>
      --
      theme = signal('dark');
      ```
- Binding to dynamic properties and attributes
  - ```
    <button [disabled]="isFormValid()">Save</button>
    <my-listbox [value]="mySelection()" />
    <img [ngSrc]="profilePhotoUrl()" alt="The current user's profile photo">
    <ul [attr.role]="listRole()"> <!-- use the attr prefix for binding to an attribute -->
    ```
  - Text interpolation in properties and attributes
    - ```
      <img src="profile-photo.jpg" alt="Profile photo of {{ firstName() }}" >
      <button attr.aria-label="Save changes to {{ objectType() }}"> <!-- use the attr prefix for binding to an attribute -->
      ```
- Binding to CSS class and style properties
  - Classes
    - Create a binding that can add or remove a class based on a truthy or falsy conditional.
      - `<ul [class.expanded]="isExpanded()">`
      - ```
        @Component({
          template: `
            <ul [class]="listClasses"> ... </ul>
            <section [class]="sectionClasses()"> ... </section>
            <button [class]="buttonClasses()"> ... </button>
          `,
          ...
        })
        export class UserProfile {
          listClasses = 'full-width outlined';
          sectionClasses = signal(['expandable', 'elevated']);
          buttonClasses = ({
            highlighted: true,
            embiggened: false,
          });
        }
        -------------------- Renders into --------------------------
        <ul class="full-width outlined"> ... </ul>
        <section class="expandable elevated"> ... </section>
        <button class="highlighted"> ... </button>
        ```
  - Properties
    - Same case as with classes
      - `<section [style.display]="isExpanded() ? 'block' : 'none'">`
      - Setting specific units and values is also possible
        - `<section [style.height.px]="sectionHeightInPixels()">`
      - You can set multiple styles in one binding
        - ```
          @Component({
            template: `
              <ul [style]="listStyles()"> ... </ul>
              <section [style]="sectionStyles()"> ... </section>
            `,
            ...
          })
          export class UserProfile {
            listStyles = signal('display: flex; padding: 8px');
            sectionStyles = signal({
              border: '1px solid black',
              'font-weight': 'bold',
            });
          }
          -------------------- Renders into --------------------------
          <ul style="display: flex; padding: 8px"> ... </ul>
          <section style="border: 1px solid black; font-weight: bold"> ... </section>
          ```
- [Template Reference Variables](https://www.angulartraining.com/daily-newsletter/template-reference-variables/)
  - Makes use of the hashtag syntax
