# Chapter 6.4: Quiz 8
Contents:
- Quiz 8

## Quiz 8: Angular Router
- In Angular, what is the purpose of the `router-outlet` tag?
  1. It specifies navigation links for the user
  2. It defines the root component of the application
  3. It's a way to embed an iframe in an Angular application
  4. It indicates where routed components will be rendered
 
- Considering the following router config, which component will be rendered when a user navigates to `/products/21`?
  ```
  RouterModule.forRoot([
     { path: '', component: ProductListComponent },
     { path: 'products/:productId', component: ProductDetailsComponent }
  ])
  ```
  1. None; there is no mapping for that route
  2. `ProductListComponent`
  3. `ProductDetailsComponent`
  4. A 404 page would show up
 
- Which of the following is not an existing guard function that can be used with the Angular router?
  1. `CanLoad`
  2. `CanDeactivate`
  3. `CanNavigate`
  4. `CanActivateChild`
 
- What is one requirement for this syntax to work? `loadComponent: () => import('./admin/admin.component').then(comp => comp.AdminComponent)`
  1. `AdminComponent` must be a standalone component
  2. `AdminComponent` must be in a lazy-loaded module
  3. This syntax does not work
  4. `AdminComponent` must be the default export in its source file
