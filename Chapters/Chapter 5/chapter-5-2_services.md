# Chapter 5.2: Services
Contents:
- Services are singletons
- Lifecycle of Angular services in an application
- Using services to cache data: An example

## Basics of Angular Services
- Angular services provide a way for you to separate Angular app data and functions that can be used by multiple components in your app.
- Services are singleton objects that share data and logic across different components in an Angular application.
- By encapsulating specific functionalities, they promote modularity, reusability, and maintainability of code.
- Using too many Services can become an [anti-pattern](https://www.angulartraining.com/daily-newsletter/anti-pattern-series-using-too-many-services/) over time. Sometimes, a simple function can do the work of a service.
  - Instead think about whether `input()` would be enough for passing data.
    - Using `input()` allows for the use of optimization in change detection using `onPush`

### Services are Singletons
- `providedIn: root`-level makes the service available to all other classes.
  - Angular will create a single shared instance that will be used by other classes that requires it. It will be a singleton.
  - ```
    @Injectable({
      providedIn: 'root'
    })
    class YourService { }
    ```

### [Lifecycle of Angular services in an application](https://www.angulartraining.com/daily-newsletter/lifecycle-of-angular-applications/)
- If `providedIn: root`-level, this singleton service will stay alive for as long as the app is alive.
- If scoped at `providedIn: YourComponent`-level or `YourModule`-level, it will stay alive as long as that component or module is alive.
  - **NOTE:** it will not be a singleton. Each instance of `YourComponent` will have its own different `YourService` instance, etc.
 
### [Using services to cache data: An example](https://www.angulartraining.com/daily-newsletter/using-services-to-cache-data/)
- You can store, share, update data in a service via signals
- ```
  @Injectable({
    providedIn: 'root'
  })
  export class CurrencyService {
    private currencySignal = signal<Currency>("USD");
    public currency: Signal<Currency> = this.currencySignal.asReadOnly();

    setCurrency(curr: Currency): void {
      this.currencySignal.set(curr);
    }

    getCurrency(): Currency {
      return this.currencySignal();
    }
  }
  ```

