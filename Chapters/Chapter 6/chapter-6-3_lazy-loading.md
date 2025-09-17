# Chapter 6.3: Lazy Loading
Contents:
- What you need to know about lazy-loading
- Lazy-loading standalone components
- Lazy-loading with @defer

## Basics of Lazy Loading
- Instead of building the app as one single bundle of code that gets downloaded when the user accesses the app in a browser, **Lazy Loading** divides our code into several different pieces that get downloaded on-demand when they are needed
- **Lazy Loading** depends on the Angular Router because bundles of code get downloaded when new URLs get activated.
- Most important feature for improving the performance of large applications

### [What you need to know about Lazy Loading](https://www.angulartraining.com/daily-newsletter/lazy-loading-for-better-angular-performance/)
- Instead of putting the whole app codebase in the required bundle that gets loaded at the start, you can instead only put the minimum required modules/pages at the start for the app to still function properly
  - eg. Only have modules/pages like `Login`, `Home`, etc. at the start (Eager Load), and then **Lazy Load** modules like `Users` or `Admin` so that only users need to load the `Users` module and only admins need to load the `Admins` module
- Since modules can have their own router config, it is possible to lazy load multiple routes + components at once.
- ```
  // To Lazy Load a page - the AdminModule will only be loaded when the /admin path is traveled to
  const routes: Routes = [
    {
      path: 'admin',
      loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule)
    }
  ]
  ```

### [Lazy Loading Standalone Components](https://www.angulartraining.com/daily-newsletter/lazy-loading-standalone-components/)
- Similar to regular modules/components but uses `loadComponent` instead of `loadChildren`
  - `{ path: 'admin', loadComponent: () => import('./admin/admin.component').then(m => m.AdminComponent) }`
- Also possible to lazy load multiple routes + components at once by creating a specific file for routing and pointing `loadChildren` to it
  - ```
    // app.routes.ts
    export const routes: Route[] = [
      { path: 'admin', loadChildren: () => import('./admin/routes').then(m => m.AdminRoutes) }
    ]

    // admin/routes.ts
    export const AdminRoutes: Route[] = [
      { path: 'admin_dashboard', component: AdminDashboardComponent },
      { path: 'admin_settings', component: AdminSettingsComponent}
    ]
    ```
- Alternate syntax if `YourComponent` has a default export like `export default class AdminDashboardComponent`
  - This can be lazy loaded in your imports by `loadComponent: () => import('./admin/admin-dashboard.component')`
    - Making the `.then(etc)` part earlier not needed anymore
   
### [Lazy Loading with @defer](https://www.angulartraining.com/daily-newsletter/angular-17-lazy-loading-with-defer/)
- A way to do **Lazy Loading** in the HTML template using the new Control Flow Syntax
- Doesn't rely on `Angular Router`
- You can also put `(minimum X)`
  - `minimum` makes it so that the block appears for at least that time
  - `X` can be in `s` seconds or `ms` milliseconds. eg. `2s` or `50ms`
- The basic `@defer` will load the `HelloMessageComponent` as soon as the browser is idle.
  - ```
    <h1> Hello World </h1>
    @defer {
      <app-hello-message></app-hello-message>
    }
    ```
- The `@placeholder` will display its block while the `@defer` block is not loading yet.
  - ```
    @placeholder (minimum 2s) {
      <b>Stuff will be loaded here</b>
    }
    ```
- The `@loading` will display its block while the `@defer` block is loading
  - ```
    @loading (minimum 50ms) {
      <b> Loading... </b>
    }
    ```
- The `@error` block will display if the `@defer` block fails to load
  - ```
    @error {
      <p> Error, loading failed </p>
    }
- `@placeholder`, `@loading`, and `@error` require a `@defer` block before them
