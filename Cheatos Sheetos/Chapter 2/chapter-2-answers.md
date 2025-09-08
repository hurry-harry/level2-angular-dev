# Answers to the Quizzes in Chapter 2
For Chapter 2.1 and 2.3

## Quiz 3
- Which `@for` block option is required?
  - i. track
- Which of the following options is not a valid Angular selector?
  - iv. ng-header
- What is the decorator used to pass data to a component?
  - i. @Input()
- Which of these HTML template examples would automatically render the latest value of data when data changes?
  - ii. [value]="data"
 
## Quiz 4
- The following syntax does not compile: Which alternative syntax would generate the correct DOM structure as intended below?
  ```
  <div *ngIf="showItems" *ngFor="let item of items">{{item}}</div>
  ```
  - ```
    i.
      <ng-container *ngIf="showItems">
        <div *ngFor="let item of items">{{item}}</div>
      </ng-container>
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
  - iii. It provides access from within the component class to the ElementRef object for the tag that has the test template reference variable.
- Which of the following functions is not a part of the Signals API?
  - ii. observable()
      
