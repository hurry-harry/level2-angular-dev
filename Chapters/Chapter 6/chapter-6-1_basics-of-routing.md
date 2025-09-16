# Chapter 6.1: Angular Router
Contents:
- Basics of route configuration
- Router outlet and router links

## Routing using Angular Router
- Angular Router allows users to move between components and views while maintaining the illusion of a traditional multi-page website without involving any server-side navigation.

### [Basics of Route Configuration](https://angular.dev/guide/routing/define-routes#managing-routes-in-your-application)
- Default file for defining routes is in `app.routes.ts`
  - This is automatically generated if the project was created using commands in CLI
- Import the `routes` file into the providers of your `AppConfig`
  - ```   
    import { routes } from './app.routes';
    export const appConfig: ApplicationConfig = {
      providers: [
        provideRouter(routes),
        // ...
      ]
    };
    ```
- Route order matters - Angular uses first-match strategy, so put specific routes before general ones.
- Route Parameters use colon syntax `:paramName` for dynamic segments.
  - Parameters must start with letters and contain only letters, numbers, underscores, and hyphens. Can be accessed via ActivatedRoute service.
- Use wildcard `**` for catch-all routes.
  - Make sure to place this as the very last route.
- For Loading Strategies, for better performance:
  - "Eager load" primary pages, like core pages/modules like Login/Home/Interceptors/ErrorHandling needed for the app to function properly
    - Loads the modules **before** the app starts 
    - Use `component: YourComponent` property for eager loading
    - ```
      { 
        path: '', 
        component: HomeComponent, <- EAGER LOAD
        title: 'Home'
      },
      ```
  - "Lazy load" secondary pages, pages/modules not required for the app to function properly
    - Loads the modules **after** the app starts, only loads on-demand
    - `loadComponent()` with dynamic imports
    - ```
      { 
        path: 'admin', 
        loadComponent: () => import('./admin/admin.component')    <- LAZY LOAD
          .then(m => m.AdminComponent),
        providers: [AdminService, { provide: 'ADMIN_KEY', useValue: 'secret' }]
      },
      ```
- Attach static data via `data:` property for metadata like analytics IDs.
- Use `providers:` property for route-specific dependency injection.
- Use `title:` property for page titles or dynamic resolvers.
- Use `redirectTo:` property to redirect to another page.
- Use `children:` property to define child routes within parent routes. Parent components need `<router-outlet>` to display child content.
  - Child routes are relative to parent path and update only the nested outlet.
- ```
  // app.routes.ts - Route configuration
  import { Routes } from '@angular/router';
  import { ResolveFn } from '@angular/router';
  
  // Dynamic title resolver
  const titleResolver: ResolveFn<string> = (route) => 
    `User ${route.params['id']} Profile`;
  
  export const routes: Routes = [
    // Eager loading - primary pages
    { 
      path: '', 
      component: HomeComponent,
      title: 'Home',
      data: { analyticsId: 'home-123' }
    },
    
    // Route parameters
    { 
      path: 'user/:id', 
      component: UserProfileComponent,
      title: titleResolver,
      data: { requiresAuth: true }
    },
    
    // Multiple parameters
    { 
      path: 'user/:id/posts/:postId', 
      component: PostDetailComponent 
    },
    
    // Lazy loading - secondary pages
    { 
      path: 'admin', 
      loadComponent: () => import('./admin/admin.component')
        .then(m => m.AdminComponent),
      providers: [AdminService, { provide: 'ADMIN_KEY', useValue: 'secret' }]
    },
    
    // Nested routes
    {
      path: 'products/:id',
      component: ProductComponent,
      children: [
        { path: '', redirectTo: 'info', pathMatch: 'full' },
        { path: 'info', component: ProductInfoComponent },
        { path: 'reviews', component: ProductReviewsComponent }
      ]
    },
    
    // Redirects
    { path: 'articles', redirectTo: '/blog' },
    
    // Wildcard - always last
    { path: '**', component: NotFoundComponent }
  ];
  
  // app.config.ts - Router setup
  import { ApplicationConfig } from '@angular/core';
  import { provideRouter } from '@angular/router';
  import { routes } from './app.routes';
  
  export const appConfig: ApplicationConfig = {
    providers: [
      provideRouter(routes),
      // other providers...
    ]
  };
  
  // ProductComponent - Parent with nested outlet
  @Component({
    template: `
      <h1>Product {{ productId }}</h1>
      <nav>
        <a routerLink="info">Info</a>
        <a routerLink="reviews">Reviews</a>
      </nav>
      <router-outlet></router-outlet>
    `
  })
  export class ProductComponent {
    productId = inject(ActivatedRoute).snapshot.params['id'];
  }
  ```

### [Router Outlet](https://angular.dev/guide/routing/show-routes-with-outlets)
- `<router-outlet />` is a placeholder where Angular renders route components.
- The outlet element remains in DOM while content is inserted as siblings.
- For nested routes:
  - Create child routes using `children:` array property in route configuration.
  - Parent components need their own `<router-outlet />` to display child content.
  - Child routes are relative to parent path and enable partial page updates instead of full page refreshes.
- You can use multiple outlets with unique names for complex layouts.
  - Default outlet name is 'primary'.
  - Match outlet names to route outlet property.
    - eg: `<router-outlet name='sidebar' />` with `{ outlet: 'sidebar' }` in `routes`.
- Listen to `activate`, `deactivate`, `attach`, and `detach` events for route component lifecycle management.
  - Use standard event binding syntax: `(activate)='onActivate($event)'`.
  - Useful for cleanup, analytics, or component state management.

### [Router Links](https://angular.dev/guide/routing/navigate-to-routes)
- Use `routerLink` directive on anchor `<a>` elements instead of `href` for Angular routing.
  - Can use both string syntax `routerLink="/path"` and array syntax `[routerLink]="['path', param]"` for dynamic parameters.
- For maintainable navigation within the app, use relative URLs (without leading `/`).
- Absolute URLs (with `/`) navigate from `root`.
- Array syntax required for dynamic parameters: `[routerLink]="['user', userId]"` or `[routerLink]="['/team', teamId, 'user', userId]"`.
- Inject `Router` service and use `router.navigate()` with path arrays for programmatic navigation.
  - Supports route parameters, query parameters, and relative navigation with `relativeTo` option.
    - Use `relativeTo: this.route` for relative navigation from current route
  - Use `router.navigateByUrl()` for full URL strings.
- Use `queryParams:` for query string parameters.
- Use `replaceUrl: true` to replace current URL in browser history instead of adding new entry.
  - Replacing the current URL makes it so that the browser `back` button won't return to the replaced page.
- Combine `ActivatedRoute` injection with `router.navigate()` for context-aware navigation.
  - Use `['..']` to navigate to parent route or `['edit']` for sibling routes.
- ```
  // Component with dynamic navigation patterns
  @Component({
    template: `
      <!-- RouterLink with dynamic parameters -->
      <nav>
        <a routerLink="/dashboard">Dashboard</a>
        <a [routerLink]="['/user', currentUserId]">My Profile</a>
        <a [routerLink]="['/team', teamId, 'user', userId]">Team Member</a>
      </nav>
      
      <!-- Relative navigation -->
      <div>
        <button (click)="navigateToEdit()">Edit</button>
        <button (click)="navigateToParent()">Back to List</button>
        <button (click)="navigateToSibling()">View Details</button>
      </div>
      
      <!-- Conditional navigation -->
      <button (click)="handleFormSubmit()">Submit Form</button>
      <button (click)="handleLogin()">Login</button>
    `
  })
  export class NavigationExampleComponent {
    private router = inject(Router);
    private route = inject(ActivatedRoute);
    
    // Dynamic data
    currentUserId = '123';
    teamId = 'team-456';
    userId = 'user-789';
    
    // Relative navigation using current route context
    navigateToEdit() {
      // From: /users/123 -> To: /users/123/edit
      this.router.navigate(['edit'], { relativeTo: this.route });
    }
    
    navigateToParent() {
      // From: /users/123 -> To: /users
      this.router.navigate(['..'], { relativeTo: this.route });
    }
    
    navigateToSibling() {
      // From: /users/123 -> To: /users/123/details
      this.router.navigate(['details'], { relativeTo: this.route });
    }
    
    // Programmatic navigation with parameters
    handleFormSubmit() {
      this.router.navigate(['/success'], {
        queryParams: { formId: 'contact-form' },
        replaceUrl: true // Replace form page in history
      });
    }
    
    handleLogin() {
      this.router.navigateByUrl('/dashboard', {
        replaceUrl: true // Remove login page from history
      });
    }
    
    // Complex navigation with multiple options
    navigateWithOptions() {
      this.router.navigate(['/search'], {
        queryParams: { 
          category: 'books', 
          sort: 'price',
          page: 1 
        },
        fragment: 'results',
        relativeTo: this.route
      });
    }
  }
  ```
