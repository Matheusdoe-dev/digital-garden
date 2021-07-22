# Classes

- Classes are blueprints for JavaScript objects.

## Fiels

- A field declaration creates a public writeable property on a class

```ts
class Point {
  x: number;
  y: number;
}

const pt = new Point();
pt.x = 0;
pt.y = 0;
```

- Fields can also have _initializers_, these will run automatically when the class is instantiated

```ts
class Point {
  x = 0;
  y = 0;
}

const pt = new Point();
console.log(`${pt.x}, ${pt.y}`); // Prints 0, 0
```

- Just like with `const`, `let`, and `var`, the initializer of a class property will be used to infer its type

### --strictPropertyInitialization

- The **strictProperyInitialization** setting controls wheter class fields need to be initialized in the constructor.

```ts
class BadGreeter {
  name: string;
  // Property 'name' has no initializer and is not definitely assigned in the constructor.
}
```

```ts
class GoodGreeter {
  name: string;

  constructor() {
    this.name = "hello";
  }
}
```

### readonly

- Fields may be prefixed with the `readonly` modifier. This prevents assignments to the field outside of the constructor.

```ts
class Greeter {
  readonly name: string = "world";

  constructor(otherName?: string) {
    if (otherName !== undefined) {
      this.name = otherName;
    }
  }

  err() {
    this.name = "not ok";
    // Cannot assign to 'name' because it is a read-only property.
  }
}

const g = new Greeter();
g.name = "also not ok";
// Cannot assign to 'name' because it is a read-only property.
```

## Constructs

- Class constructors are very similar to functions. You can add parameters with type annotations default values, and overloads

```ts
class Point {
  x: number;
  y: number;

  // Normal signature with defaults
  constructor(x = 0, y = 0) {
    this.x = x;
    this.y = y;
  }
}
```

### Differeces between class constructor and function signatures

- Constructors can't have type parameters;
- Constructors can't have return type annotations.

## Methods

- A function property on a class is called a _method_. Methods can use all the same type annotations as functions and constructs

```ts
class Point {
  x = 10;
  y = 10;

  scale(n: number): void {
    this.x *= n;
    this.y *= n;
  }
}
```

## Getters / Setters

```ts
class C {
  _length = 0;

  get length() {
    return this._length;
  }

  set length(value: number) {
    this._length = value;
  }
}
```

- TypeScript has some special inference rules for accessors
  - If `get` exists but not set, the property is automatically `readonly`
  - If the type of the setter parameter is not specified, it is inferred form the return type of the getter
  - Getters and setters must have the same Member Visibility.

## Class Heritage

- Like other languages with object-oriented feautres, classes in JavaScript can inherit from base classes.

### `implements` Clauses

- You can use an `implements` clause to check that a class satisfies a particular `interface`. An error will be issued if a class fails to correctly implement it.

```ts
interface Pingable {
  ping(): void;
}

class Sonar implements Pingable {
  ping() {
    console.log("ping!");
  }
}

class Ball implements Pingable {
  // Class 'Ball' incorrectly implements interface 'Pingable'.
  // Property 'ping' is missing in type 'Ball' but required in type 'Pingable'.
  pong() {
    console.log("pong!");
  }
}
```

#### Cautions

- It's important to understand that an `implements` clause is only a check that the class can be treated as the interface type. It doesn't change the type of the class or its methods _at all_. A commom source of error is to assume that an `implements` clause will change the class type - it doesn't!

```ts
interface Checkable {
  check(name: string): boolean;
}

class NameChecker implements Checkable {
  check(s) {
    // Parameter 's' implicitly has an 'any' type.
    return s.toLowercase() === "ok";
  }
}
```

### `extends` Clauses

- Classes may `extend` from a base class. A derived class has all the properties and methods of its base class, and also define additional members.

```ts
class Animal {
  move() {
    console.log("Moving along!");
  }
}

class Dog extends Animal {
  woof(times: number) {
    for (let i = 0; i < times; i++) {
      console.log("woof!");
    }
  }
}

const d = new Dog();
// Base class method
d.move();
// Derived class method
d.woof(3);
```

#### Overriding Methods

- A derived class can also override a base class field or property. You can use the `super.` syntax to access base class methods.
- TypeScript enforces that a derived class is always a subtype of its base class.

```ts
class Base {
  greet() {
    console.log("Hello, world!);
  }
}

class Derived extends Base {
  greet(name?: string) {
    if (name === undefined) {
      super.greet();
    } else {
      console.log(`Hello, ${name.toUpperCase}`);
    }
  }
}

const d = new Derived();
d.greet();
d.greet("reader");
```

#### Initialization Order

- The order that JavaScript classes initialize can be surprising in some cases.

```ts
class Base {
  name = "base",
  constructor() {
    console.log("My name is " + this.name);
  }
}

class Derived extends Base {
  name = "derived";
}

// Prints "base", not "derived"
const d = new Derived();
```

- The order of class initialization, as defined by JavaScript, is:
  - The base class fields are initialized
  - The base class constructor runs
  - The derived class fields are initialized
  - The derived class constructor runs

## Member Visibility

### `public`

- The default visibility of class members is `public`. A `public` member can be accessed anywhere.

### `protected`

- `protected` members are only visible to subclasses of the class they're declared in.

#### Exposure of `protected` members

- Derived classes need to follow their base class contracts, but may choose to expose a subtype of base class with more capabilities. This includes making `protected` members `public`

```ts
class Base {
  protected m = 10;
}

class Derived extends Base {
  // No modifier, so default is 'public'
  m = 15;
}
const d = new Derived();
console.log(d.m); // OK
```

- Note that `Derived` was already able to freely read and write `m`, so this doesn't meaningfully alter the 'security' of this situation. The main thing to note here is that in the derived class, we need to be careful to repeat the `protected` modifier if this exposure isn't intentional.

### `private`

- `private` is like `protected`, but doesn't allow access to the member even from subclasses

#### Cross-instance private access

- TypeScript does allow cross-instance `private` access

```ts
class A {
  private x = 10;

  public sameAs(other: A) {
    // No error
    return other.x === this.x;
  }
}
```

#### Caveats

- Like other aspects of TypeScript's type system, `private` and `protected` are only enforced during type checking. This means that JavaScript runtime constructs like `in` or simple property lookup can still access a `private` or `protected` member

```ts
class MySafe {
  private secretKey = 12345;
}
```

```js
// In a JavaScript file...
const s = new MySafe();
// Will print 12345
console.log(s.secretKey);
```

- If you need to protect values in your class from malicious actors, you should use mechanisms that offer hard runtime privacy,
  such as closures, weak maps, or `private fields`.

## Static Members

- Classes may have `static` members. These members aren't associated with a particular instance of the class. They can be accessed through the class constructor object itself.

```ts
class MyClass {
  static x = 0;
  static printX() {
    console.log(MyClass.x);
  }
}
console.log(MyClass.x);
MyClass.printX();
```

- Static members can also use the same `public`, `protected`, and `private` visibility modifiers

```ts
class MyClass {
  private static x = 0;
}
console.log(MyClass.x);
// Property 'x' is private and only accessible within class 'MyClass"
```

- Static members are also inherited

```ts
class Base {
  static getGreeting() {
    return "Hello World";
  }
}

class Derived extends Base {
  myGreeting = Derived.getGreeting();
}
```

### Special Static Names

- It's generally not safe/possible to overwrite properties from the `Function` prototype. Because classes are themselves functions that can be invoked with `new`, certain `static` names can't be used. Function properties like `name`, `length`, and `call` aren't valid to define as `static` members.

## Generic Classes

- Classes, much like interfaces, can be generic. When a generic class is instantiated with `new`, its type parameters are inferred the same way as in a function call

```ts
class Box<Type> {
  contents: Type;
  constructor(value: Type) {
    this.contents = value;
  }
}

const b = new Box("hello!");
// const b: Box<string>
```

- Classes can use generic constraints and defaults the same way as interfaces.

### Type Parameters in Static Members

- This code isn't legal

```ts
class Box<Type> {
  static defaultValue: Type;
  // Static members cannot reference class type parameters
}
```

- Remember that types are always fully erased! At runtime, there's only one `Box.defautValue` property slot. This means that setting `Box<string>.defaultValue` (if that were possible) would _also_ change `Box<number>.defaultValue` - not good. The `static` members of a generic class can never refer to the class's type parameters.

## `this` at Runtime in Classes

- It's important to remember that TypeScript doesn't change the runtime behavior of JavaScript, and that JavaScript is somewhat famous for having some peculiar runtime behaviors.

```js
class MyClass {
  name = "MyClass";
  getName() {
    return this.name;
  }
}
const c = new MyClass();
const obj = {
  name: "obj",
  getName: c.getName,
};

// Prints "obj", not "MyClass"
console.log(obj.getName());
```

- By default, the value of `this` inside a function depends on _how the function was called_. In this example, because the function was called through the `obj` reference, its value of `this` was `obj` rather thant the class instance. This is rarely what you want to happen!

### Arrow functions

-

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/2/basic-types.html#emitting-with-errors)
- [Understanding TypeScript - 2021 Edition, Maximilian Schwarzmüller, Udemy](https://www.udemy.com/share/1013yQBUUdcFZaRnw=/)
