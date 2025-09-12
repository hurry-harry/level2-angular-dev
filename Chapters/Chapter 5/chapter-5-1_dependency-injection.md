# Chapter 5.1: Dependency Injection
Contents:
- How to provide a dependency
- How to inject a dependency with inject()
- How to inject a dependency using a constructor
- Provider configuration options

## Basics of Dependency Injection
- Allows classes with Angular decorators, such as Components, Directives, Pipes, Services, and Injectables, to configure dependencies that they need.

### [How to provide a dependency](https://angular.dev/guide/di/dependency-injection#providing-a-dependency)
- Make sure the class for `YourService` has the `@Injectable()` decorator to allow it to be injected to other components
- Then provide it by making it available via one of the following ways:
  - Using `providedIn` to make it available at the application root level
    - This is the preferred way to provide a service
    - `providedIn: root`-level injects the service to all other classes.
      - Angular will create a single shared instance that will be used by other classes that requires it. It will be a singleton.
    - Allows Angular optimizers to remove unused services.
    - ```
      @Injectable({
        providedIn: 'root'
      })
      class YourService { }
      -----------------------
      @Component({
        selector: 'app-your-component',
        etc...
      })
      class YourComponent {
        users: Signal<User[]> = inject(YourService).getUsers();

        // It can also be like this
        yourService = inject(YourService);
        users = yourService.getUsers();
      }
      ```
  - Using `providers: [ ]` at the component level
    - `providers: [YourService]` will inject it to all instances of this component and other components and directives used in the template.
    - In component level, you get a new instance of the service with each new instance of that component.
      - The service also gets destroyed when the component its tied to is destroyed.
    - ```
      @Component({
        selector: 'app-your-component',
        template: '...',
        providers: [YourService]
      })
      class YourComponent {
        users: Signal<User[]>;

        constructor(yourService: YourService) {
          this.users = yourService.getUsers();
        }
      }
      ```
  - Using `ApplicationConfig` to provide at the application root level
    - This makes it available to all components, directives, and pipes.
    - ```
      // In the `app.config.ts` file
      export const appConfig: ApplicationConfig = {
          providers: [
            { provide: YourService },
          ]
      };
      -----------------------------------------------
      // and then in `main.ts` add at the end
      bootstrapApplication(AppComponent, appConfig)
      ```

### [How to inject a dependency with `inject()`](https://www.angulartraining.com/daily-newsletter/the-inject-function/)
- The to inject is via the class constructor -> `constructor(public yourService: YourService)`
- We can also use the `inject()` method
- `inject()` can be useful in places like `RouteGuards`
  - ```
    const route = {
      path: 'admin',
      canActivate: [() => inject(AdminService).isAdmin();]
    };
    ```
- It can be used in three areas:
  - As a field initializer in a class:
    - ```
      export class YourComponent {
        yourService = inject(YourService);
      }
      ```
  - In the constructor body
    - ```
      export class YourComponent {
        yourService: YourService;
      
        constructor() {
          this.yourService = inject(YourService);
        }
      }
      ```
  - In the provider factory of the `AppConfig`
    - ```
      providers: [
        { provide: Car, useFactory: () => {
            const engine = inject(Engine);
            return new Car(engine);
        }}
      ]
      ```

### [Provider Configuration Options](https://www.angulartraining.com/daily-newsletter/dependency-injection-and-provider-config/)
- If the service is `providedIn: 'root'` at `your.service.ts`, this service will be a singleton that can be injected to any component in your app.
  - ```
    @Injectable({
      providedIn: 'root'
    })
    class YourService { }
    ```
- If you want to restrict the scope of the service, you can instead specify a Module or a Component instead of `'root'`
  - If Module: it will be restricted to the components/directives/pipes/services of that module
  - If Component: that component and its children can inject that service
  - ```
    @Injectable({
      providedIn: YourComponent
    })
    export class YourService { }

    // The above is equivalent to the one below. ONLY ONE WAY IS NEEDED.
    // USING BOTH IS REDUNDANT
    @Component({
      selector: 'app-your-component',
      providers: [YourService],
      template: '...'
    })
    export class YourComponent { }
    ```
- There is also `platform`, this lets the service be injected to ANY Angular app in the current browser tab.
  - For running multiple Angular applications on the same page and want to share a service between these apps.
  - ```
    @Injectable({
      providedIn: 'platform'
    })
    export class YourService { }
    ```

  
