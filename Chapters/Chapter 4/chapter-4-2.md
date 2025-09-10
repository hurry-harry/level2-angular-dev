# Chapter 4.2: Custom Directives and Advanced Usage
Contents:
- [Custom Directives and Advanced Usage]()

## Custom Directives and Advanced Usage

Contents:
- [When should you create custom directives vs. using components?]()
- [HostListener and HostBinding decorators]()
- [5 different syntax options for ngIf]()
- [Local variables with ngFor]()

### [When should you create custom directives vs. using components?](https://www.angulartraining.com/daily-newsletter/when-to-create-a-directive-vs-a-component/)
- One big difference between Components and Directives is that Components have an HTML template, Directives don't.
  - Directives access HTML by `@HostBinding` and `@HostListener`
- Use a custom directive when using a component makes it restricting and less re-usable.
- For example this scenario
  ```
  // Custom Component
  template: `
  <button class="my-btn" [class.isActive]="!saved || hasError" [disabled]="saved && !hasError">
    {{ buttonText }}
  </button>
  `
  export class MyButtonComponent {
    @Input() saved: boolean;
    @Input() hasError: boolean;
    @Input() buttonText: string;
  }

  // This component is not ideal because:
  //   - The button is limited to displaying only text, not all HTML will be allowed, like icons etc.
  //   - State is always dependent on `saved` and `hasError`, not easy to enhance if for example need to add another variable/case like `hasLoaded` etc.
  -------------------------------------------
  // Custom Directive
  @Directive({
    selector: '[isActive]',
  })
  export class ActiveDirective {
    @HostBinding('class.my-btn')
    private myBtn = true;

    @HostBinding('class.isActive')
    private _isActive: boolean = true;

    @HostBinding('disabled')
    private disabled: boolean = false;

    @Input()
    set isActive(value: boolean) {
      this._isActive = value;
      this.disabled = !value;
    }

  // And then usage would be
  <button [isActive]="!saved || hasError">
    Test 2
  </button>

  // This directive is a lot more flexible and re-usable
  //   - Can be used on other HTML elements that support `disabled`
  //   - The display can include icons, images, etc
  //   - The condition for `[isActive]` is more flexible and easier to add/remove
  ```  

### [HostListener and HostBinding decorators](https://www.angulartraining.com/daily-newsletter/hostbinding-and-hostlistener/)
- This is how directives work with HTML since they don't have a template like Components
- HostBinding: `[]` becomes `@HostBinding`
  - Component: `[one]="two"` -> Directive: `@HostBinding('one') two = 'something';`
  - ```
    // Components
    <button [class]="myClasses" [disabled]="!active">
      Submit
    </button>

    ------------------------------------------- 
    // Directives - it works the exact same but declared/used differently
    @Directive({
      selector: '[appSample]'
    })
    export class SampleDirective {
      @HostBinding('class') myClasses = 'blueBg whiteTxt';
  
      @HostBinding('disabled') notActive = false;
    }
    ```
- HostListener: `()` becomes `@HostListener`
  - Component: `(click)="submit()"` -> Directive: `@HostListener('click') submit() { //submit }`
  - ```
    // Components
    <button (click)="submit()">
      Submit
    </button>

    ------------------------------------------- 
    // Directives - it works the exact same but declared/used differently
    @Directive({
      selector: '[appSample]'
    })
    export class SampleDirective {
    
      @HostListener('click')
      submit() {
        // do something
      }
    }
    ```
  
### [5 different syntax options for ngIf](https://blog.angulartraining.com/5-different-syntax-options-for-ngif-3408dd9050c)
- ```
    @Component({
    template: `
      <!-- 1. Basic syntax -->
      <div *ngIf="isVisible">Basic conditional content</div>
      
      <!-- 2. Ng-template syntax for multiple siblings -->
      <ul>
        <li>Always visible item</li>
        <ng-template [ngIf]="showAdditionalItems">
          <li>Conditional item 1</li>
          <li>Conditional item 2</li>
        </ng-template>
      </ul>
      
      <!-- 3. Else clause -->
      <div *ngIf="user; else noUser">
        Welcome, {{ user.name }}!
      </div>
      <ng-template #noUser>
        <div>Please log in</div>
      </ng-template>
      
      <!-- 4. Then/else blocks -->
      <div *ngIf="isLoading; then loadingBlock else contentBlock"></div>
      <ng-template #loadingBlock>
        <div>Loading...</div>
      </ng-template>
      <ng-template #contentBlock>
        <div>Content loaded!</div>
      </ng-template>
      
      <!-- 5. Async variable storage -->
      <div *ngIf="user$ | async as user; else loading">
        Hello {{ user.firstName }}, {{ user.lastName }}!
        <!-- No ?. needed since user is guaranteed to exist -->
      </div>
      <ng-template #loading>
        <div>Loading user data...</div>
      </ng-template>
    `
  })
  export class NgIfExamplesComponent {
    isVisible = true;
    showAdditionalItems = false;
    user: User | null = null;
    isLoading = false;
    user$ = new BehaviorSubject<User | null>(null);
  }
  ```

### [Local variables with ngFor](https://www.angulartraining.com/daily-newsletter/ngfor-local-variables/)
- You can use as many as you want in a single `ngFor` directive
- `index: number`: The index of the current item in the iterable.
- `count: number`: The length of the iterable.
- `first: boolean`: True when the item is the first item in the iterable.
- `last: boolean`: True when the item is the last item in the iterable.
- `even: boolean`: True when the item has an even index in the iterable.
- `odd: boolean`: True when the item has an odd index in the iterable.
- ```
  <li *ngFor="let user of users; index as i; first as isFirst">
    {{i}}/{{users.length}}. {{user}} <span *ngIf="isFirst">default</span>
  </li>
  ```
  
