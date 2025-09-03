# Chapter 2.3: Signals - Lifecycle Hooks - Other tips
Contents:
- Signals
- Lifecycle Hooks
- Other Tips
  
## Signals - A new way to handle data
- Easier to learn than RxJs, lightweight, and they enable a new type of change detection
- Signals are wrappers around values that notifies interested consumers when the value changes
- Signals can be primitive types or complex data structures
- Read a signals value by calling its getter function
- Can be writable or read-only

### Creating a signal and setting its value
- Created using `signal(value)`
  - ```
    const count = signal(0);                 // Set
    console.log('The count is: ' + count()); // Get
    count.set(3)                             // To change the value
    count.update(value => value + 1);        // To change the value based on the previous value
    ```

### Computed()
- asd
