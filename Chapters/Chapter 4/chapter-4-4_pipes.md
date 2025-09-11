# Chapter 4.4 - Pipes
Contents:
- [Pipes]()

## Pipes
- The recommended approach to formatting data in your HTML templates
- They’re optimized for change detection by default
- They don’t re-render and re-format data if the pipe input hasn’t changed.

### [Common Angular Pipes](https://angular.dev/api?type=pipe#angular_common)
- AsyncPipe
  - Subscribes to an Observable or Promise and returns the latest emitted value
- CurrencyPipe
  - Transforms a number to a currency string according to locale rules
- DatePipe
  - Formats a date value according to locale rules
- DecimalPipe
  - Formats a number as a decimal string according to locale rules
- I18nPluralPipe
  - Maps a value to a string that pluralizes the value according to locale rules
- I18nSelectPipe
  - Generic selector that displays the string that matches the current value
- JsonPipe
  - Converts a value into its JSON-format string representation
- KeyValuePipe
  - Transforms an Object or Map into an array of key-value pairs
- LowerCasePipe
  - Transforms text to all lower case
- PercentPipe
  - Formats a number as a percentage according to locale rules
- SlicePipe
  - Creates a new Array or String containing a subset of the elements
- TitleCasePipe
  - Transforms text to title case
- UpperCasePipe
  - Transforms text to all upper case
 
### [Pipe syntax and Pipe parameters](https://angular.dev/guide/templates/pipes#overview)
- Pipe Syntax
  - Use the vertical bar `|` operator to apply transformations.
  - Pipes are binary operators with lower precedence than arithmetic operators but higher than ternary operators.
  - Use parentheses for clarity: `{{ (condition ? 'yes' : 'no') | uppercase }}`.
- Multiple Pipes
  - Chain pipes from left to right using multiple `|` operators.
  - Each pipe receives the output of the previous one. Example: `{{ date | date | uppercase }}` formats date then converts to uppercase.
- Pipe Parameters
  - Pass parameters using colon syntax `pipe:param1:param2`. `DatePipe` accepts format strings and timezone: `{{ date | date:'short':'UTC' }}`.
  - `CurrencyPipe` accepts currency code and display format.
- Pure vs Impure
  - Pipes are `pure` by default, only executing when primitive values or object references change.
  - Use `pure: false` for `impure` pipes that detect internal array/object changes, but avoid due to performance impact.
- Custom Pipes
  - Create with `@Pipe` decorator, implement `PipeTransform` interface, and define `transform(value, ...params)` method.
  - Use camelCase naming and append `"Pipe"` to class names.
  - Import and add to component's imports array.
 
### [When to use Pipes](https://angular.dev/guide/templates/pipes#how-pipes-work)
- Use when you want to format data such as strings, dates, numbers, etc
- ```
  import { Component } from '@angular/core';
  import { CurrencyPipe} from '@angular/common';
  @Component({
    selector: 'app-root',
    imports: [CurrencyPipe],
    template: `
      <main>
        <p>Total: {{ amount | currency }}</p>
      </main>
    `,
  })
  export class AppComponent {
    amount = 123.45;
  }

  // CurrencyPipe is imported from @angular/common
  // CurrencyPipe is added to the imports array
  // The amount data is passed to the currency pipe
  ```

### [How to create custom Pipes](https://www.angulartraining.com/daily-newsletter/how-to-create-custom-pipes/)
- You can use in the Angular CLI `ng generate pipe [name]` or shorthand `ng g p [name]`.
  - Add `--standalone` at the end to make it a standalone Pipe
- Manually create with `@Pipe` decorator, implement `PipeTransform` interface, and define `transform(value, ...params)` method.
- Use camelCase naming and append `"Pipe"` to class names.
- Import and add to component's imports array.
- ```
  @Pipe({
    name: 'personName',
    standalone: true
  })
  export class CustomPipe implements PipeTransform {
  
    transform(person: Person, displayPrefix: boolean = true): string {
      let prefix = "";
      if (displayPrefix)
        prefix = (person.gender === "Male") ? "Mr. " : "Mrs. " ;

      return `${prefix}${person.firstName} ${person.lastName}`;
    }

    // Implements `transform` that takes a `PersonObject` parameter, and an optional boolean parameter with a default `true` value
    // Returns a formatted string based on the `PersonObject` parameters info, and the `displayPrefix` value

    // {{ person | personName }}
    // {{ person | personName: false }}
  ``` 
