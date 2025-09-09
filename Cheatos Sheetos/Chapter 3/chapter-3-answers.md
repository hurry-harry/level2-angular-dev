# Answers to the Quizzes in Chapter 3
For Chapter 3.3

## Quiz 5
- What's the value of a in this example? `let a = otherValue ?? 21`
  - iii. 12 if `otherValue === 12`, 21 if `otherValue` is `null or undefined`
 
- According to the following function signature `function setCurrency(value: number, currency: 'USD' | 'GBP')`, which of the following code examples would compile?
  - ii. `setCurrency(10, 'USD');`
 
- What does the following code output to the console?
  ```
  enum UserResponse {
   No = 0,
   Yes = 1,
  }
  
  console.log(UserResponse.Yes);
  ```
  - iii. `1`
 
- How many parameters does the following function expect? `function test(a, b, ...c) { // ... }`
  - ii. at least 2
