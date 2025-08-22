# Chapter 2: Angular Components
### Component Selector and Decorators
- Angular uses selectors to know when to render a specific component
- [Choosing a selector and using prefixes](https://angular.dev/guide/components/selectors#choosing-a-selector)
  - Components should use a custom element name.
  - Angular has default behaviour where it reports an error if it sees a custom tag name not matching any available component. For preventing typo errors on names.
  - Recommended to use a short and uniform prefix for all custom components. Eg. Building YouTube using Angular would use `yt-`: `yt-menu`, `yt-player`
  - Use an attribute selector when you want to create a custom component on a standard native element.
    - Eg. a custom button can use the `<button>` element -> `selector: 'button[yt-upload]',`
    - This lets the consumer use the standard APIs of the element immediately.
