# Chapter 3.2 - TypeScript
Contents:
- [TypeScript](https://github.com/hurry-harry/level2-angular-dev/blob/main/Chapters/Chapter%203/chapter-3-2.md#typescript)

## TypeScript (TS)

### What is TypeScript
- Based on JavaScript (JS)
  - TS adds additional syntax to JS. Stricter integration lets you see and catch errors earlier in the editor
  - TS converts to JS, which lets it run anywhere that JS runs
    - eg. browsers, node.js, etc
 
### [Syntax for Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html)
- Class Members & Fields
  - Support typed fields with optional initializers and readonly modifiers. Use definite assignment assertion `!` when initializing outside constructor.
- Constructor Features
  - Support parameter types, defaults, and overloads. Use super() before this in derived classes. Parameter properties auto-create fields.
- Access Modifiers
  - `public` (default), `private`, and `protected` control member visibility. `Private` is class-only, `protected` includes subclasses.
- Abstract Classes
  - Define contracts with abstract methods that subclasses must implement. Cannot be instantiated directly, serve as base classes.
- Structural Typing
  - Classes with identical structures are compatible. Subtype relationships exist without explicit inheritance.
- ```
  // Basic class with fields, constructor, and methods
  class Person {
    public name: string;
    private age: number;
    protected email: string;
    readonly id: string;

    constructor(name: string, age: number, email: string) {
      this.name = name;
      this.age = age;
      this.email = email;
      this.id = Math.random().toString();
    }

    public greet(): string {
      return `Hello, I'm ${this.name}`;
    }

    private getAge(): number {
      return this.age;
    }
  }

  // Parameter properties (shorthand)
  class Employee extends Person {
    constructor(
      public department: string,
      private salary: number,
      name: string,
      age: number,
      email: string
    ) {
      super(name, age, email);
    }

    public getSalary(): number {
      return this.salary; // Can access private from same class
    }
  }

  // Abstract class example
  abstract class Shape {
    abstract getArea(): number;
  
    public describe(): string {
      return `Area: ${this.getArea()}`;
    }
  }

  class Circle extends Shape {
    constructor(private radius: number) {
      super();
    }

    getArea(): number {
      return Math.PI * this.radius * this.radius;
    }
  }

  // Usage
  const employee = new Employee("IT", 75000, "John", 30, "john@example.com");
  const circle = new Circle(5);
  console.log(employee.greet()); // "Hello, I'm John"
  console.log(circle.describe()); // "Area: 78.54"
  ```

### [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- Primitive Types
  - Use `string`, `number`, and `boolean` (lowercase) for basic types.
  - Avoid `String`, `Number`, `Boolean` (uppercase) which refer to special built-in types.
  - Arrays use `number[]` or `Array<number>` syntax.
- Type Annotations
  - Add explicit types with `: type` syntax when needed, but prefer type inference.
  - Use `any` sparingly as it disables type checking. Enable `noImplicitAny` to catch implicit `any` types.
- Functions
  - Annotate parameters with `(param: string)` and return types with `: string`.
  - Use optional parameters with `?` and default values.
  - Anonymous functions get contextual typing from usage.
- Object Types
  - Define object shapes with `{ prop: string }` syntax.
  - Use optional properties with `prop?: string`.
  - Union types combine multiple types with `string | number`. Type aliases create reusable type names.
- Advanced Types
  - Use type assertions with `as` or `<>` syntax.
  - Literal types restrict values to specific strings/numbers.
  - Handle `null/undefined` with strict null checks and non-null assertion operator `!`.
- ```
  // Primitive types
  let name: string = "John";
  let age: number = 30;
  let isActive: boolean = true;

  // Arrays
  let numbers: number[] = [1, 2, 3];
  let names: Array<string> = ["Alice", "Bob"];

  // Type annotations and inference
  let inferred = "Hello"; // TypeScript infers as string
  let explicit: string = "World";

  // Functions
  function greet(name: string, age?: number): string {
    return `Hello ${name}${age ? `, age ${age}` : ''}`;
  }
  
  const add = (a: number, b: number): number => a + b;
  
  // Object types
  interface User {
    id: number;
    name: string;
    email?: string; // Optional property
  }
  
  let user: User = {
    id: 1,
    name: "John"
  };
  
  // Union types
  let id: string | number = "123";
  id = 456; // Also valid
  
  // Type aliases
  type Status = "pending" | "approved" | "rejected";
  let currentStatus: Status = "pending";
  
  // Type assertions
  let someValue: any = "Hello World";
  let strLength: number = (someValue as string).length;
  // or
  let strLength2: number = (<string>someValue).length;
  
  // Null/undefined handling
  function processValue(value: string | null | undefined): string {
    if (value) {
      return value.toUpperCase();
    }
    return "No value";
  }
  
  // Non-null assertion
  let element = document.getElementById("myDiv")!; // Asserts not null
  ```

### [Union Types](https://blog.angulartraining.com/union-types-in-typescript-2517a82a1ea0)
- Union types in TypeScript allow a variable to hold more than one type of value. This is particularly useful when a function or a variable can accept multiple types. Union types are defined using the vertical bar `|` to separate each type.
- Usage examples of Union types are:
  - Replace `enums`
    - ```
      // Instead of 
      export enum Color {RED = ‘red’, BLUE = ‘blue’, WHITE = ‘white’}

      // We can use, this also lets us use the functions of `string` since Color is `string` typed
      export type Color = 'red' | 'white' | 'blue';
      const myColor: Color = 'red';
      myColor.toUpperCase();
      ```
  - Making a property be `optional`
    - ```
      // Union types work with `undefined`
      let user: User | undefined;

      // Advanced usage, allows us to create a generic type `Optional`
      export type Optional<T> = T | undefined;
      let user: Optional<User>;
      ```
  - Alternative to inheritance
    - Recommended if used with `NgRx`
    - ```
      // Instead of making `AuthAction` be an empty Abstract class that extends all of the Login classes
      // We can do this instead
      export type AuthAction = LoginAction
                          | LoginSuccessfulAction
                          | LoginErrorAction
                          | LogoutAction
                          | LogoutSuccesfulAction;
      ```

### [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
- Object Transformation
  - `Partial<T>` makes all properties optional
  - `Required<T>` makes all required
  - `Readonly<T>` prevents reassignment
  - `Pick<T, K>` selects specific properties
  - `Omit<T, K>` removes specific properties.
- Type Construction
  - `Record<K, V>` creates object types with specific keys and values.
  - `Exclude<T, U>` removes types from unions, `Extract<T, U>` keeps only matching types.
  - `NonNullable<T>` removes null/undefined.
- Function Types
  - `Parameters<T>` extracts function parameter types
  - `ReturnType<T>` extracts return types
  - `ConstructorParameters<T>` gets constructor params
  - `InstanceType<T>` gets class instance types.
- Advanced Utilities
  - `Awaited<T>` unwraps Promise types recursively.
  - `ThisParameterType<T>` and `OmitThisParameter<T>` handle function context.
  - `ThisType<T>` provides contextual typing for object methods.
- String Manipulation
  - `Uppercase<T>`, `Lowercase<T>`, `Capitalize<T>`, `Uncapitalize<T>` transform string literal types.
  - `NoInfer<T>` blocks type inference in generic contexts.
- ```
  // Object transformation utilities
  interface User {
    id: number;
    name: string;
    email: string;
    age?: number;
  }
  
  // Partial - makes all properties optional
  type PartialUser = Partial<User>;
  const updateData: PartialUser = { name: "John" }; // Only name required
  
  // Required - makes all properties required
  type RequiredUser = Required<User>;
  const fullUser: RequiredUser = { id: 1, name: "John", email: "john@example.com", age: 30 };
  
  // Pick - select specific properties
  type UserPreview = Pick<User, "id" | "name">;
  const preview: UserPreview = { id: 1, name: "John" };
  
  // Omit - remove specific properties
  type UserWithoutId = Omit<User, "id">;
  const newUser: UserWithoutId = { name: "John", email: "john@example.com" };
  
  // Record - create object with specific keys and values
  type Status = "pending" | "approved" | "rejected";
  type StatusConfig = Record<Status, string>;
  const config: StatusConfig = {
    pending: "yellow",
    approved: "green", 
    rejected: "red"
  };
  
  // Union type utilities
  type Colors = "red" | "green" | "blue" | "yellow";
  type PrimaryColors = Exclude<Colors, "yellow">; // "red" | "green" | "blue"
  type WarmColors = Extract<Colors, "red" | "yellow">; // "red" | "yellow"
  
  // Function type utilities
  function getUser(id: number, includeEmail: boolean): User {
    return { id, name: "John", email: "john@example.com" };
  }
  
  type UserParams = Parameters<typeof getUser>; // [number, boolean]
  type UserReturn = ReturnType<typeof getUser>; // User
  
  // Non-nullable
  type MaybeString = string | null | undefined;
  type DefiniteString = NonNullable<MaybeString>; // string
  
  // Awaited for Promise unwrapping
  type PromiseUser = Promise<User>;
  type ResolvedUser = Awaited<PromiseUser>; // User
  
  // String manipulation
  type EventName = "click" | "hover";
  type EventHandler = `on${Capitalize<EventName>}`; // "onClick" | "onHover"
  ```

### [Constructor Syntax Options](https://www.typescriptlang.org/docs/handbook/2/classes.html#constructors)
- Constructors can’t have type parameters or `returns`
- You can add parameters with type annotations, default values, and overloads
  - ```
    class PointA {
      x: number;
      y: number;
     
      // Normal signature with defaults
      constructor(x = 0, y = 0) {
        this.x = x;
        this.y = y;
      }
    }

    // Overloads
    class Point {
      x: number = 0;
      y: number = 0;
     
      // Constructor overloads
      constructor(x: number, y: number);
      constructor(xy: string);
      constructor(x: string | number, y: number = 0) {
        // Code logic here
      }
    }
    ```
- `super()` calls
  - If the class has a base class, `super()` must be called before making any `this.(base class member)`
  - ```
    class Base {
      k = 4;
    }
     
    class Derived extends Base {
      constructor() {
        // Prints a wrong value in ES5; throws exception in ES6
        console.log(this.k); // Results in -> 'super' must be called before accessing 'this' in the constructor of a derived class.
        super();
      }
    }
    ```

### Enums
- Numeric Enums
  - Auto-increment from 0 or specified value. `enum Direction { Up, Down }` creates Up=0, Down=1.
  - Can mix computed and constant members, but computed members need initializers.
- String Enums
  - Each member must be constant-initialized with string literals.
  - Better for debugging and serialization than numeric enums.
  - No auto-increment behavior or reverse mapping.
  - String enums only have forward mappings.
- Const Enums
  - Use `const enum` for performance - completely removed at compile time with inlined values.
  - Only supports constant expressions, no computed members.
  - Avoid ambient const enums in shared libraries.
- Runtime Behavior
  - Numeric enums create both forward and reverse mappings (value→name).
  - Use `keyof typeof Enum` to get union of enum keys.
- Modern Alternative
  - Consider objects with as const instead of enums for better JavaScript alignment.
  - `const Direction = { Up: 0, Down: 1 } as const` provides similar functionality with better compatibility.
- ```
  // Numeric enums (auto-increment)
  enum Direction {
    Up,    // 0
    Down,  // 1
    Left,  // 2
    Right  // 3
  }
  
  enum Status {
    Pending = 1,
    Approved,  // 2
    Rejected   // 3
  }
  
  // String enums
  enum UserRole {
    Admin = "ADMIN",
    User = "USER",
    Guest = "GUEST"
  }
  
  // Using enums
  function move(direction: Direction): void {
    console.log(`Moving ${direction}`); // Prints: Moving 0
  }
  
  function checkAccess(role: UserRole): boolean {
    return role === UserRole.Admin;
  }
  
  // Reverse mapping (numeric enums only)
  console.log(Direction[0]); // "Up"
  console.log(Direction.Up); // 0
  
  // Const enums (inlined at compile time)
  const enum Priority {
    Low = 1,
    Medium,
    High
  }
  
  const priority = Priority.High; // Becomes: const priority = 3;
  
  // Modern alternative with objects
  const ODirection = {
    Up: 0,
    Down: 1,
    Left: 2,
    Right: 3,
  } as const;
  
  type DirectionType = typeof ODirection[keyof typeof ODirection]; // 0 | 1 | 2 | 3
  
  // Union enum types
  enum LogLevel {
    ERROR,
    WARN,
    INFO,
    DEBUG,
  }
  
  type LogLevelStrings = keyof typeof LogLevel; // "ERROR" | "WARN" | "INFO" | "DEBUG"
  
  function log(level: LogLevelStrings, message: string): void {
    const num = LogLevel[level];
    console.log(`[${level}] ${message}`);
  }
  
  // Usage examples
  move(Direction.Up);
  checkAccess(UserRole.Admin);
  log("ERROR", "Something went wrong");
  ```
