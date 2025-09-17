# Chapter 6.5 - Challenge: Use the Router to Display Movie Details
Associated files are located in the `files/Chapter-6-5` folder
- Initial files
- Solution by Harry
- Solution provided

## In this challenge, we want to be able to display movie details by clicking on the "Details" button of any movie displayed on the screen, using:
- Routers
- Lazy-loading

### Requirements:
- Edit the provided `src/home/home.component.ts` to make it the new landing page that displays the list of movies.
- Change `app.component.ts` to display just a `<router-outlet />`. The entire page will be controlled by the router.
- Change the router config in `app.routes.ts` by adding two routes:
  - A route for the default path `""` goes to `HomeComponent` (landing page with movies list)
  - A route for the path `"details/:id"` lazy-loads `MovieDetailsComponent` (page with details for a single movie)
- Update the "Details" button in `MovieItemComponent` so it uses a `routerLink` to navigate to the proper movie details.

## Sample Output
![chapter61-screenshot](https://github.com/user-attachments/assets/cded96e3-1988-4246-a681-716c4c7a5cb1)
