# Chapter 5.4 - Challenge: Use a Service to Manage Favorite Movies
Associated files are located in the `files/Chapter-5-4` folder
- Initial files
- Solution by Harry
- Solution provided

## In this challenge, we want to be able to manage favorite movies in the app by:
- Adding a movie as a favorite by clicking on a "star" icon ☆
- Displaying current favorite movies by displaying their "star" icon in yellow color ⭐
- Removing a movie from our favorites by clicking again on the "star" icon ☆

### Requirements
- Edit the provided `src/services/favorites.service.ts`
- Use a Signal to store the list of current favorite movies (empty array by default)
- Implement a method `toggleFavorite` to toggle (add/remove) a movie from the list of favorites
- Implement a method `isFavorite(movie)` that returns whether a movie is a favorite or not
- Add `inputs/outputs` to `movie-item.component.ts` to pass the information when a movie is a favorite, as well as `emit` an event when the "star" icon is clicked.
- Use the active CSS class to turn the ☆ icon into ⭐. Remove that class to do the opposite.
- Update `app.component.ts` to handle the interactions with `favorites.service.ts` and pass the favorite information to `movie-item.component.ts`.

## Sample Output
![chapter51-screenshot](https://github.com/user-attachments/assets/c82fcf81-6d78-4a82-a1eb-6cb68917dd92)
