# Chapter 5.3: Quiz 7
Contents:
- Quiz 7

## Quiz 7: Services and Dependency Injection
- In Angular, which of the following decorators is used to register a class as a service?
  1. `@Service`
  2. `@NgService`
  3. `@Inject`
  4. `@Injectable`
 
- Can the following class be injected into a component as-is?
  ```
  import { Injectable } from '@angular/core';
  import { HttpClient } from '@angular/common/http';
  @Injectable()
  export class CartService {
      constructor(private http: HttpClient) {}
  
      getCartContents() {
          // Some code
      }
  }
  ```
  1. No, that code does not compile
  2. No, it has to be added to an array of providers first
  3. No, it does not have the @Service decorator
  4. Yes
 
- According to the following code, how many instances of TokenService can we have in an Angular application? (assuming there's no other dependency injection configuration anywhere else for that service)
  ```
  @Injectable({
    providedIn: 'root'
  })
  export class TokenService {
    // ... (rest of the class implementation)
  }
  ```
  1. It could be any number of instances
  2. Zero or one
  3. One
  4. One per module
 
- One of the following syntaxes is correct for injecting a `LoginService` in `LoginComponent`. Which one is it?
  1. `myService = inject(LoginService);`
  2. `loginService = constructor(loginService: LoginService);`
  3. `loginservice = create(LoginService)`
  4. `@Injectable() loginService: LoginService;`
