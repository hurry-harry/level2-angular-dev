# Chapter 4.1: Directives
Contents:
- [Basics of Directives](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%204/chapter-4-1.md#basics-of-directives)
- [Custom Directives and Advanced Usage]()

## Basics of Directives
- **NOTE:** Very recommended to use the new `@if` control flow block instead on modern Angular projects
- **NOTE:** Very recommended to use the new `@for` control flow block instead on modern Angular projects

Content:
- [ngIf]()
- [ngFor]()
- [Structural Directives]()
- [Attribute Directives]()
- [Common Angular Directives]()

### [ngIf](https://angular.dev/api/common/NgIf?tab=usage-notes) Directive
- **NOTE:** Very recommended to use the new `@if` control flow block instead on modern Angular projects
- Advanced Template Syntax
  - Use `*ngIf="condition; then thenBlock; else elseBlock"` for external templates.
  - Store conditional results with `*ngIf="expression as variable"` to avoid repeated `async` pipe subscriptions and `null` checks.
- Template Guards
  - `NgIf` provides type guards (`ngTemplateGuard_ngIf` and `ngTemplateContextGuard`) that narrow types for strict null checks.
  - The context type excludes falsy values `(false | 0 | '' | null | undefined)`.
- Performance Optimization
  - Using `*ngIf="userObservable | async as user"` creates only one subscription vs multiple `async` pipes.
  - The `as` syntax stores the resolved value locally, eliminating need for safe navigation operators.
- Template Expansion
  - Shorthand `*ngIf` expands to explicit `<ng-template>` with `[ngIf]` and `[ngIfElse]` bindings.
  - This affects structural directive nesting and template object creation.
- ```
  // Component with advanced NgIf patterns
  @Component({
    template: `
      <!-- External templates with then/else -->
      <div *ngIf="showContent; then contentBlock; else loadingBlock"></div>
      
      <!-- Store async result to avoid multiple subscriptions -->
      <div *ngIf="user$ | async as user; else noUser">
        Hello {{ user.name }}! <!-- No ?. needed -->
      </div>
      
      <!-- Switch between templates dynamically -->
      <div *ngIf="isPrimary; then primaryTemplate; else secondaryTemplate"></div>
      
      <!-- Templates -->
      <ng-template #contentBlock>
        <h2>Content loaded</h2>
      </ng-template>
      
      <ng-template #loadingBlock>
        <div>Loading...</div>
      </ng-template>
      
      <ng-template #noUser>
        <div>No user data available</div>
      </ng-template>
      
      <ng-template #primaryTemplate>
        <div>Primary content</div>
      </ng-template>
      
      <ng-template #secondaryTemplate>
        <div>Secondary content</div>
      </ng-template>
    `
  })
  export class AdvancedNgIfComponent {
    showContent = false;
    isPrimary = true;
    user$ = new BehaviorSubject<User | null>(null);
    
    @ViewChild('primaryTemplate') primaryTemplate!: TemplateRef<any>;
    @ViewChild('secondaryTemplate') secondaryTemplate!: TemplateRef<any>;
    
    switchTemplate() {
      this.isPrimary = !this.isPrimary;
    }
  }
  ```

### [ngFor](https://angular.dev/api/common/NgFor?tab=usage-notes) Directive
- **NOTE:** Very recommended to use the new `@for` control flow block instead on modern Angular projects.
  - The `@for` block has been optimized for performance and proven to be much faster than `*ngFor` on large arrays.
- Local Variables
  - Access iteration context with `index as i`, `first as isFirst`, `last as isLast`, `even as isEven`, `odd as isOdd`.
  - Use `count` for total length and `$implicit` for the current item value.
- TrackBy Function
  - Implement `trackBy: trackByFn` to optimize change detection.
    - Prevents expensive DOM rebuilds when object identities change but data remains the same.
    - Essential for performance with dynamic lists.
- Change Propagation
  - NgFor tracks object identity by default.
  - When items are added/removed/reordered, corresponding DOM changes occur.
  - Use trackBy to maintain component state and enable smooth animations.
- Structural Directive Limitations
  - Only one structural directive per element.
    - Wrap `*ngFor` in container with `*ngIf` for conditional iteration.
    - Angular expands shorthand syntax to explicit `<ng-template>` with context merging. 
- ```
  // Component with advanced NgFor patterns
  @Component({
    template: `
      <!-- TrackBy for performance optimization -->
      <div *ngFor="let user of users; trackBy: trackByUserId; index as i; first as isFirst">
        {{i + 1}}. {{user.name}} 
        <span *ngIf="isFirst">(First)</span>
      </div>
      
      <!-- Using all local variables -->
      <div *ngFor="let item of items; 
                   index as idx; 
                   count as total; 
                   first as firstItem; 
                   last as lastItem; 
                   even as isEven; 
                   odd as isOdd">
        <span [class]="isEven ? 'even' : 'odd'">
          {{idx}}/{{total}}: {{item.name}}
          <span *ngIf="firstItem">[START]</span>
          <span *ngIf="lastItem">[END]</span>
        </span>
      </div>
      
      <!-- Conditional iteration with wrapper -->
      <div *ngIf="showList">
        <div *ngFor="let item of filteredItems; trackBy: trackByItem">
          {{item.title}}
        </div>
      </div>
    `
  })
  export class AdvancedNgForComponent {
    users = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ];
    
    items = [
      { id: 'a', name: 'Item A' },
      { id: 'b', name: 'Item B' }
    ];
    
    showList = true;
    filteredItems = this.items;
    
    // TrackBy functions for performance
    trackByUserId(index: number, user: any): number {
      return user.id;
    }
    
    trackByItem(index: number, item: any): string {
      return item.id;
    }
  }
  ```

### [Structural Directives](https://angular.dev/guide/directives/structural-directives)
- Microsyntax Grammar
  - Structural directives use `*prefix="(let | expression) (';' | ',')? (let | as | keyExp)*"` syntax.
  - Key expressions map to PascalCase inputs (e.g., `from source` becomes `[selectFrom]="source"`).
  - Use `let variable` for template context access.
- One Directive Per Element
  - Only one structural directive per element in shorthand syntax.
  - Use `<ng-container>` to wrap multiple structural directives or create nested `<ng-template>` elements for complex scenarios.
- Template Guards
  - Implement `ngTemplateGuard_(input)` for type narrowing and `ngTemplateContextGuard` for context typing.
  - Use `'binding'` literal for truthiness-based guards.
  - These improve compile-time type checking and prevent runtime errors.
- Custom Directive Creation
  - Inject `TemplateRef` and `ViewContainerRef`, use `input.required<T>()` for inputs, and call `viewContainerRef. createEmbeddedView()` with context object containing `$implicit` property for template data.
- ```
  // Custom structural directive with microsyntax
  @Directive({
    selector: '[select]'
  })
  export class SelectDirective<T> {
    private templateRef = inject(TemplateRef);
    private viewContainerRef = inject(ViewContainerRef);
    
    selectFrom = input.required<DataSource<T>>();
    
    // Template guard for type narrowing
    static ngTemplateGuard_selectFrom(dir: SelectDirective<any>, expr: any): expr is DataSource<any> {
      return true;
    }
    
    // Context guard for template typing
    static ngTemplateContextGuard<T>(dir: SelectDirective<T>, ctx: any): ctx is SelectTemplateContext<T> {
      return true;
    }
    
    async ngOnInit() {
      const data = await this.selectFrom().load();
      this.viewContainerRef.createEmbeddedView(this.templateRef, {
        $implicit: data, // Template context with $implicit
      });
    }
  }

  // Template context interface
  export interface SelectTemplateContext<T> {
    $implicit: T;
  }
  ---------------------------------------------------------
  
  // Truthiness-based guard directive
  @Directive({
    selector: '[customIf]'
  })
  export class CustomIfDirective {
    condition = input.required<boolean>();
    
    // Binding guard for truthiness
    static ngTemplateGuard_condition: 'binding';
  }
  ---------------------------------------------------------
  
  // Usage examples in template
  @Component({
    template: `
      <!-- Microsyntax: key expressions map to PascalCase inputs -->
      <div *select="let data from source; index as i">
        {{data.name}} ({{i}})
      </div>
      
      <!-- Multiple structural directives with ng-container -->
      <ng-container *ngIf="showList">
        <div *ngFor="let item of items; trackBy: trackFn">
          {{item.title}}
        </div>
      </ng-container>
      
      <!-- Truthiness guard example -->
      <div *customIf="user">
        {{user.name}} <!-- Type narrowed to non-null -->
      </div>
    `
  })
  export class ExampleComponent {
    source = new DataSource<User>();
    showList = true;
    items = [{ id: 1, title: 'Item' }];
    user: User | null = null;
    
    trackFn(index: number, item: any) {
      return item.id;
    }
  }
  ```
