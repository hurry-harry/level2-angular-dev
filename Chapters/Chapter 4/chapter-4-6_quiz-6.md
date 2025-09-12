# Chapter 4.6: Quiz 6 - Directives and Pipes
Contents:
- Quiz 6

## Quiz 6 - Directives and Pipes
- Which of the following is not an Angular directive from `@angular/common`?
  1. `ngSwitch`
  2. `ngBind`
  3. `ngClass`
  4. `ngFor`
 
- Which of these options is the correct syntax for pipe parameters?
  1. `{{ amount | currency | ‘USD’ }}`
  2. None of the above - all are incorrect
  3. `{{ amount | currency :: ‘$’ }}`
  4. `{{ amount | currency : ‘USD’ }}`
 
- What is the HostBinding decorator doing in this directive?
  ```
  @Directive({
   selector: '[appHighlight]'
  })
  export class HighlightDirective {
   @HostBinding('class.highlighted') highlight = true;
  }
  ```
  1. It is creating an inline style on the host element with a CSS property named highlighted set to true.
  2. HostBinding does not do anything on directives, it only works with components.
  3. It is adding the CSS class named highlighted to any DOM element that has the appHighlight attribute on it.
  4. It is adding the CSS class named highlighted to any DOM element for which the tag name is appHighlight.
 
- What is the proper syntax to display a list of names in `div` elements when `display` is true?
  1. ```
     <ng-template *ngIf="display">
       <div *ngFor="let name of names">{{name}}</div>
     </ng-template>
     ```
  2. ```
     <ng-switch *ngSwitchCase="display">
       <div *ngFor="let name of names">{{name}}</div>
     </ng-switch>
     ```
  3. ```
     <ng-container *ngIf="display">
       <div *ngFor="let name of names">{{name}}</div>
     </ng-container>
     ```
  4. ```
     <div *ngIf="display" *ngFor="let name of names">
       {{name}}
     </div>
     ```
