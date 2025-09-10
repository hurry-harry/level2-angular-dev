# Chapter 1: The Basics of Angular
Contents:
- [What's the difference between Angular JS and Angular?](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%201/chapter-1.md#whats-the-difference-between-angular-js-and-angular)
- [The Angular CLI](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%201/chapter-1.md#the-angular-cli)
- [Conventions and Style Guide](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%201/chapter-1.md#conventions-and-style-guide)
- [Quiz 1](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%201/chapter-1.md#quiz-1)
- [Features of the Framework](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%201/chapter-1.md#features-of-the-framework)
- [Angular Modules and Standalone Components](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%201/chapter-1.md#angular-modules-and-standalone-components)
- [Quiz 2](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%201/chapter-1.md#quiz-2)
  
## What’s the difference between Angular JS and Angular?
- AngularJS is version 1 of the framework. Not supported anymore since January 2022.
- Angular is the modern iteration of the framework. First as Angular 2 in 2016 and now 18/19 in 2024.

## The Angular CLI
- The Command Line Interface (CLI) to generate, compile, build, and deploy Angular projects.
  - [Reference](https://angular.dev/cli) for sample commands.
  - [Full Document Reference](https://angular.dev/tools/cli).
- You can use [Stackblitz](https://stackblitz.com/) as an online sandbox tool for Angular projects.

## Conventions and Style Guide
- The Angular Style Guid can be used for info on best practices and organization for code files and folders.
- Auto generated items from using the Angular CLI will automatically use the recommended naming conventions.
- [Angular Style Guide](https://angular.dev/style-guide)

## Quiz 1
- Which CLI command should we run to compile and serve our project locally in development mode?
  1. ng deploy
  2. ng development
  3. ng serve
  4. ng build
- What does Angular refer to?
  1. The latest version of the framework (Angular 2+)
  2. Version 3
  3. Version 1
  4. Version 16
- Which of the following file names does not follow the Angular style-guide conventions?
  1. name.service.ts
  2. highlight-all.directive.ts
  3. uppercase-pipe.ts
  4. app.component.ts
- Which one of the following is not a valid Angular CLI command?
  1. ng add
  2. ng build
  3. ng test
  4. ng fix

## Features of the Framework
- [What is Angular?](https://angular.dev/overview)
  - Component-based architecture with dependency injection for modular, testable code.
  - Signals-based reactivity with compile-time optimizations for fast state updates.
  - Server-side rendering (SSR) and static site generation (SSG) support.
  - Built-in modules for forms, routing, HTTP client, internationalization, and security.
  - Angular CLI, DevTools, and IDE integration for enhanced development experience.
- [Directives vs Components. When to use which?](https://www.angulartraining.com/daily-newsletter/when-to-create-a-directive-vs-a-component/)
  - **Components** are reusable UI building blocks that encapsulate template, logic, and styling to create self-contained, modular pieces of the user interface.
  - **Directives** are instructions that extend HTML elements with custom behavior, allowing you to manipulate the DOM, add event listeners, or change element appearance and behavior.
  - If your `component` (eg. button) will have implementation logic (eg. `isActive`) that can possibly be reused by other components, implement it as a `directive` instead.
    - Resulting in more flexible and more reusable code.
  - [Additional Example](https://blog.angulartraining.com/tutorial-how-to-create-your-own-angular-directive-3532d7f31fab)
- [Custom Pipes](https://www.angulartraining.com/daily-newsletter/how-to-create-custom-pipes/)
  - Pipes can be used to format data; like strings, dates, and numbers.
- [When to use Services](https://www.angulartraining.com/daily-newsletter/using-services-to-cache-data/)
  - Services are singletons that are created once for all and stay in memory as long as the application is open.
  - This makes services the best place to store data that can be shared between multiple different components using BehaviorSubjects or Signals.
  - [BehaviourSubjects vs Signals](https://blog.angulartraining.com/angular-signals-best-practices-around-exposing-signals-5385452150a1)

## Angular Modules and Standalone Components
- **NOTE:** Modules will most likely get removed from the Angular framework in the future. Angular 17+ relies on standalone components instead.
- [What are ngModules?](https://www.angulartraining.com/daily-newsletter/what-you-need-to-know-about-ngmodules/)
  - [Config Options](https://angular.dev/api/core/NgModule)
  - Used for importing and exposing features to be acceessible "globally"
  - Only reasons you'd create `ngModules` in the past are:
    - Sharing many components/directives/pipes/services as a library. Packaging those in an NgModule was the only option.
    - You needed to lazy-load a bunch of features to improve performance.
  - **However** these issues are solved by **Standalone Components**. They can also be lazy-loaded and bring all their dependencies along. No need for `ngModule`.
- [What are Standalone Components?](https://www.angulartraining.com/daily-newsletter/what-are-standalone-components/)
  - Components (also pipes/directives/etc) that don't belong to any modules.
  - Can be imported on its own and used as is.
  - Better to just import only the exact items we need to keep things leaner.
  - StandaloneComponentsCheatsheet.pdf -> Look for this PDF in the files/chapter1/ folder in this repository
 
## Quiz 2
- Which one of these concepts does not exist in the Angular framework?
  1. NgModules
  2. Standalone components
  3. Redux reducers
  4. Structural directives
- Which of the following statements is true?
  1. Pipes are used to format data such as dates and numbers
  2. Services link components to server-side HTML templates
  3. Component libraries cannot be customized to our needs
  4. Directives are a way to interact with server-side APIs
- What's the main language of the Angular framework?
  1. Javascript
  2. Typescript
  3. JSP
  4. AppScript
- Which @NgModule decorator option defines the main component of an Angular application?
  1. bootstrap: [AppComponent]
  2. bootstrap: AppComponent
  3. start: AppComponent
  4. main: [AppComponent]
