# Chapter 3.3
Contents: 
- Quiz 5

## Quiz 5 - JavaScript and TypeScript
- What's the value of a in this example? `let a = otherValue ?? 21`
  1. `"otherValue"`
  2. 12 if `otherValue === 12`, 21 if `otherValue` is `{} or undefined`
  3. 12 if `otherValue === 12`, 21 if `otherValue` is `null or undefined`
  4. 12 if `otherValue == 12`, 21 if `otherValue` is `null`
 
- According to the following function signature `function setCurrency(value: number, currency: 'USD' | 'GBP')`, which of the following code examples would compile?
  1. `setCurrency(10);`
  2. `setCurrency(10, 'USD');`
  3. None of the above, the function syntax is incorrect
  4. `setCurrency('10', 'GBP');`
 
- What does the following code output to the console?
  ```
  enum UserResponse {
   No = 0,
   Yes = 1,
  }
  
  console.log(UserResponse.Yes);
  ```
  1. `UserResponse.Yes`
  2. `Yes`
  3. `1`
  4. `[object Object]
 
- How many parameters does the following function expect? `function test(a, b, ...c) { // ... }`
  1. exactly 3
  2. at least 2
  3. None, this function signature is incorrect
  4. 2 to 5
