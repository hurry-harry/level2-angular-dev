# Chapter 6.2: Route Guards
Contents:
- Basics of Route Guards
- Legacy Syntax vs. v14+ Syntax

## Control Access using Route Guards
- Route guards protect access to specific routes within your Angular application.
- They are functions that control whether a user can navigate to or leave a particular route
- Common examples of using route guards include authentication and access control.

### [Basics of Route Guards](https://angular.dev/guide/routing/route-guards)
- You can use the CLI command `ng generate guard CUSTOM_NAME` to generate a file
  - Or create your own file manually.
  - The suffix `-guard.ts` is commonly used to distinguish RouteGuard files
- Route Guards use the following return types:
  - `boolean`
    - `true` allows navigation, `false` blocks it
    - `CanMatch` is an exception, when `false` Angular will try other matching routes first
  - `UrlTree` or `RedirectCommand`
    - Redirects to another route instead of blocking
  - `Promise<T>` or `Observable<T>`
    - Router uses the first emitted value and then unsubscribes
- Four types of Route Guards:
  - `CanActivate`
    - Determines whether a user can access a route
    - Params:
      - `route: ActivatedRouteSnapshot`
        - Contains information about the route being activated
      - `state: RouterStateSnapshot`
        - Contains the router's current state
    - Can return any of the four guard types
      - If you need to redirect the user, return a `URLTree` or `RedirectCommand`. Do not return `false` and then programmatically navigate the user.
    - ```
      export const authGuard: CanActivateFn = (route: ActivatedRouteSnapshot, state: RouterStateSnapshot) => {
        const authService = inject(AuthService);
        return authService.isAuthenticated();
      };
      ```
  - `CanActivateChild`
    - Determines whether a user can access the child routes of the parent route
      - It will also protect the child of the child routes if there are any
    - Params:
      - `childRoute: ActivatedRouteSnapshot`
        - Contains information about the child route being activated
      - `state: RouterStateSnapshot`
        - Contains the router's current state
    - Can return any of the four guard types (same case with `CanActivate`)
    - ```
      export const adminChildGuard: CanActivateChildFn = (childRoute: ActivatedRouteSnapshot, state: RouterStateSnapshot) => {
        const authService = inject(AuthService);
        return authService.hasRole('admin');
      };
      ```
  - `CanDeactivate`
    - Determines whether a user can leave a route
      - Useful for preventing navigation away from unsaved forms.
    - Params:
      - `component: T`
        - The component instance being deactivated
      - `currentRoute: ActivatedRouteSnapshot`
        - Contains information about the current route
      - `currentState: RouterStateSnapshot`
        - Contains the current router state
      - `nextState: RouterStateSnapshot`
        - Contains the next router state being navigated to
    - Can return any of the four guard types (same case with `CanActivate`)
    - ```
      export const unsavedChangesGuard: CanDeactivateFn<FormComponent> = (component: FormComponent, currentRoute: ActivatedRouteSnapshot, currentState: RouterStateSnapshot, nextState: RouterStateSnapshot) => {
        return component.hasUnsavedChanges()
          ? confirm('You have unsaved changes. Are you sure you want to leave?')
          : true;
      };
      ```
  - `CanMatch`
    - Determines whether a route can be matched 'positively' during path matching.
      - Useful for feature flags, A/B testing, or conditional route loading.
    - Params:
      - `route: Route`
        - The route configuration being evaluated
      - `segments: UrlSegment[]`
        - The URL segments that have not been consumed by previous parent route evaluations
    - Can return any of the four guard types
      - However `false` will make it move on to try and match with other routes, instead of completely blocking navigation
    - ```
      export const featureToggleGuard: CanMatchFn = (route: Route, segments: UrlSegment[]) => {
        const featureService = inject(FeatureService);
        return featureService.isFeatureEnabled('newDashboard');
      };
      -----------------------------------

      // Usage with multiple components for the same path
      // When the user visits `/dashboard`, the first one that matches the correct guard will be used.
      // 📄 routes.ts
      const routes: Routes = [
        {
          path: 'dashboard',
          component: AdminDashboard,
          canMatch: [adminGuard]
        },
        {
          path: 'dashboard',
          component: UserDashboard,
          canMatch: [userGuard]
        }
      ]
      ```
- ```
  // Applying your route guards
  import { Routes } from '@angular/router';
  import { authGuard } from './guards/auth.guard';
  import { adminGuard } from './guards/admin.guard';
  import { canDeactivateGuard } from './guards/can-deactivate.guard';
  import { featureToggleGuard } from './guards/feature-toggle.guard';
  const routes: Routes = [
    // Basic CanActivate - requires authentication
    {
      path: 'dashboard',
      component: DashboardComponent,
      canActivate: [authGuard]
    },
    // Multiple CanActivate guards - requires authentication AND admin role
    {
      path: 'admin',
      component: AdminComponent,
      canActivate: [authGuard, adminGuard]
    },
    // CanActivate + CanDeactivate - protected route with unsaved changes check
    {
      path: 'profile',
      component: ProfileComponent,
      canActivate: [authGuard],
      canDeactivate: [canDeactivateGuard]
    },
    // CanActivateChild - protects all child routes
    {
      path: 'users', // /user - NOT protected
      canActivateChild: [authGuard],
      children: [
        // /users/list - PROTECTED
        { path: 'list', component: UserListComponent },
        // /useres/detail/:id - PROTECTED
        { path: 'detail/:id', component: UserDetailComponent }
      ]
    },
    // CanMatch - conditionally matches route based on feature flag
    {
      path: 'beta-feature',
      component: BetaFeatureComponent,
      canMatch: [featureToggleGuard]
    },
    // Fallback route if beta feature is disabled
    {
      path: 'beta-feature',
      component: ComingSoonComponent
    }
  ];
  ```

### [Legacy Syntax vs. v14+ Syntax](https://blog.angulartraining.com/router-utility-functions-in-angular-14-8d843b50d2e2)
- New syntax is function-based
- ```
  // You can inject a service, call a method, and use the result of that for your guard
  const route = {
    path: ‘admin’,
    canActivate: [() => inject(AdminService).isUserBoss()]
  };
  ----------- OR -----------

  // You can access the context of the current component to disable navigation w/o using a service
  const route = {
    path: ‘edit’,
    component: EditCmp,
    canDeactivate: [
      (component: EditCmp) => component.hasSavedChanges
    ]
  };
  ```

### [How to Read Route Parameters and Route Information](https://www.angulartraining.com/daily-newsletter/accessing-route-information-with-angular/)
- The built-in `ActivatedRoute` contains these parameters and information
- ```
  url - Observable of the URL segments for this route
  params - Observable of the matrix parameters scoped to this route
  queryParams - Observable of the query parameters shared by all routes
  fragment - Observable of the URL fragment shared by all routes
  data - Observable of static and resolved data from this route
  outlet - The outlet name of the route
  component - The component that should be loaded for this route
  routeConfig - The route configuration used to match this route
  root - The root of the route tree
  parent - The parent of this route in the route tree
  firstChild - The first child of this route in the route tree
  children - The children of this route in the route tree
  pathFromRoot - An array of route objects from the root to this route
  paramMap - Observable of the matrix parameters as a ParamMap
  queryParamMap - Observable of the query parameters as a ParamMap
  snapshot - The current snapshot of this route
  title - Observable of the title associated with this route
  ```



