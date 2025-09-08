# Chapter 3.1 - JavaScript
Contents:
- [JavaScript](https://github.com/hurry-harry/level2-angular-dev/blob/main/chapter-3-1.md#javascript)

## JavaScript

### [Variable Scope: Differences between `let`, `var`, and `const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
- [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
  - Re-assignable
  - Block-scoped variable
  - Initialization is optional
  - Temporal Dead Zone
    - Trying to access/reference any `let` variable at a point before they are declared and initialized will cause a `ReferenceError`
  - Does not allow duplicate declarations
- [`var`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var)
  - Re-assignable
  - Function or globally scoped variable
    - Scope is curly-bracket to curly-bracket, and all those enclosed within that level
    - Vars declared in [Block Statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/block), try...catch, switch, and for() can still be referenced out side of the block since these statements do not create scopes for var
      - ```
        for (var a of [1, 2, 3]) {
          // do something
        }
        console.log(a); // 3
        ```
  - Initialization is optional
  - **NOT** under Temporal Dead Zone
    - `var` will instead return `undefined` in these situations
  - Redeclarations / Duplicate var declarations
    - Does **NOT** trigger errors
    - var-to-var
      - Var will only lose its value if the redeclaration has an initializer
        - ```
          var a = 1;
          var a = 2;
          console.log(a); // 2
          var a;
          console.log(a); // 2; not undefined
          ```
    - var-to-function
      - Var declaration's initializer always overrides the function's value
      - ```
        var a = 1;
        function a() {}
        console.log(a); // 1
        ```
    - **HOWEVER** var declarations cannot be in the **same** scope as a let, const, class, or import declaration.
      - ```
        var a = 1;
        let a = 2; // SyntaxError: Identifier 'a' has already been declared

        // This one is allowed since `let` is in child scope and not same scope
        var a = 1;
        {
          let a = 2;
        }
        ```
- [`const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
  - **Not** re-assignable
    - **However** if it is an object, its properties can be added, updated, or removed.
  - Scoped to blocks and functions
  - Temporal Dead Zone
  - Cannot be redeclared by any other declaration in the same scope.
  - Requires an initializer

 ### [Backticks and Template Strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
 - Literals that are delimited with backtick (`) characters
 - Allows for:
   - Multi-line strings
   - String interpolation with embedded expressions
   - Special constructs called tagged templates
- ```
  `string text`

  `string text line 1
  string text line 2`

  console.log("string text line 1\nstring text line 2");
  // "string text line 1
  // string text line 2"

  `string text ${expression} string text`

  // Escaping backticks
  `\`` === "`"; // true
  `\${1}` === "${1}"; // true

  const a = 5;
  const b = 10;
  console.log("Fifteen is " + (a + b) + " and\nnot " + (2 * a + b) + ".");
  // "Fifteen is 15 and
  // not 20."

  const classes = `header ${
    isLargeScreen() ? "" : `icon-${item.isCollapsed ? "expander" : "collapser"}`
  }`;
  ```
- Tagged Templates: Advanced form of template literals
  ```
  const person = "Mike";
  const age = 28;

  function myTag(strings, personExp, ageExp) {
    const str0 = strings[0]; // "That "
    const str1 = strings[1]; // " is a "
    const str2 = strings[2]; // "."

    const ageStr = ageExp < 100 ? "youngster" : "centenarian";

    // We can even return a string built using a template literal
    return `${str0}${personExp}${str1}${ageStr}${str2}`;
  }

  const output = myTag`That ${person} is a ${age}.`;

  console.log(output);
  // That Mike is a youngster.
  ```

### [Destructuring Assignment Syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring)
- Syntax to unpack values from arrays or properties from objects into their own variables
- ```
  let a, b, rest;
  [a, b] = [10, 20];

  console.log(a);
  // Expected output: 10

  console.log(b);
  // Expected output: 20

  [a, b, ...rest] = [10, 20, 30, 40, 50];
  // [rest] gets 30, 40, 50

  console.log(rest);
  // Expected output: Array [30, 40, 50]

  const arr = [1, 2, 3];
  const [a, b, c] = arr;
  // a = 1, b = 2, c = 3
  ```

### [Arrow Function Syntax](https://www.angulartraining.com/daily-newsletter/everything-you-need-to-know-about-arrow-functions/)
- Short way to write functions
- Parentheses are required when using multiple function parameters.
- In a one-liner arrow function, the result of the expression is automatically returned.
- If the function has multiple lines, it must have curly brackets and a return statement. 
- ```
  // Old, "normal" syntax
  function add(a, b) {
    return a + b;
  }

  // Arrow function syntax
  function add = (a, b) => a + b;
  ```

### [Nullish Coalescing Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing)
- The nullish coalescing (??) operator is a logical operator that returns based on the left-hand side
  - Returns **right-hand** side if the left-hand side is **null** or **undefined**
  - Returns **left-hand** side if left-hand side is **NOT** null or undefined
- ```
  const foo = null ?? "default string";
  console.log(foo);
  // Expected output: "default string"
  ```
- Not possible to combine the AND (&&) or OR operators (||) directly with ??. A syntax error will be thrown in such cases.
  - `null || undefined ?? "foo"; // raises a SyntaxError`
  - Must use parenthesis to isolate and define precedence
    - `(null || undefined) ?? "foo"; // returns "foo"`
  
