# 1. Getting Started

## 1.1 Installation

### Installing

```bash
$ curl https://sh.rustup.rs -sSf | sh
$ source $HOME/.cargo/env
```

### Updating

```bash
$ rustup update
```

### Uninstalling

```bash
$ rustup self uninstall
```

### Local documentation

```bash
$ rustup doc
$ rustup doc --book
$ rustup doc --std
```

## 1.2 Hello, world!

### Creating

```bash
$ mkdir hello_world
$ cd hello_world
$ cat <<EOF >main.ts
> fn main() {
>     println!("Hello, world!");
> }
> EOF
```

### Compiling

```bash
$ rustc main.ts
```

### Running

```bash
$ ./main
```

## 1.3 Hello, Cargo!

### Creating

```bash
$ cargo new hello_cargo
$ cd hello_cargo
```

```bash
$ mkdir hello_cargo
$ cd hello_cargo
$ cargo init
```

### Build and run

```bash
$ cargo run
Hello, Cargo!
```

### Build for release

```bash
$ cargo build --release
```

# 2. Programming a Guessing Game

## Use external crates

### Step 1: modify `the Cargo.toml`

```toml
[dependencies]

rand = "0.3.14"
```

> Note: `0.3.14` is shorthand for `^0.3.14`.

### Step 2: build the project

```bash
$ cargo build
```

> Note: `Cargo.lock` ensures reproducible builds in the future.

### Step 3: update a crate

```bash
$ cargo update
    Updating crates.io index
```

## Complete guessing game code

Filename: src/main.rs

```rust
use std::cmp::Ordering;
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Err(_) => continue,
            Ok(n) => n,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Greater => println!("Too big!"),
            Ordering::Less => println!("Too small!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

# 3. Common Programming Concepts

## 3.0 Identifiers

Variables, functions, structs, lots of things, all of these things need identifiers, which can be:

- `/[a-z][_a-z0-9]*/i`
- `/_[_a-z0-9]+/i`

Identifiers cannot be keywords, unless use a “raw identifier”. Raw identifiers start with `r#`:

```rust
let r#fn = "this variable is named 'fn' even though that's a keyword";

// call a function named 'match'
r#match();
```

## 3.1 Variables and Mutability

### Use _snake case_ naming convention

```rust
let max_score = 0;
let last_name = "Lee";
```

### By default variables are immutable

```rust
let x = 5;
// error[E0384]: cannot assign twice to immutable variable `x`
x = 6;
```

### Make variables mutable

```rust
let mut x = 5;
x = 6;
```

### Make a constant

- Constants are always immutable.
- Constants use `const` instead of `let`.
- Constants can be declared in any scope.
- Constants cannot be set at runtime.

```rust
const MAX_POINTS: u32 = 100_000;
```

### Variable shadowing

```rust
let x = "     ";
let x = x.len();
```

## 3.2 Data Types

- Scalar Types
  - Integer Types
  - Floating-Point Types
    - `f64`
      - `let x = 3.0;`
      - `let x = 1.0f64`
      - `let x: f64 = 1.0`
    - `f32`
      - `let x = 1.0f32`
      - `let x: f32 = 1.0`
  - The Boolean Type `bool`
    - `false`
    - `true`
  - The Character Type `char`
    - `'汉'`
- Compound Types
  - Two Primitive Compound Types
    - The Tuple Type
    - The Array Type

### Integer types

| Length  | Signed | Unsigned |
| :-----: | :----: | :------: |
|  8-bit  |   i8   |    u8    |
| 16-bit  |  i16   |   u16    |
| 32-bit  |  i32   |   u32    |
| 64-bit  |  i64   |   u64    |
| 128-bit |  i128  |   u128   |
|  arch   | isize  |  usize   |

### Integer literals

| Number literals |    Example    |
| :-------------: | :-----------: |
|     Decimal     |   `98_222`    |
|       Hex       |    `0xff`     |
|      Octal      |    `0o77`     |
|     Binary      | `0b1111_0000` |
| Byte(`u8` only) |    `b'A'`     |

### Integer variable declaration

```rust
let x = 32; // i32
let a: u32 = 16;
let b = 32i8;
let c = b'A'; // `u8` only
```

### The tuple type

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
```

```rust
let tup: (i32,) = (500,);
```

```rust
let tup = (500, 6.4, 1);
```

```rust
let x: (i32, f64, u8) = (500, 6.4, 1);
let five_hundred = x.0;
let six_point_four = x.1;
let one = x.2;
```

### The array type

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

```rust
let a = [1, 2, 3, 4, 5];
let first = a[0];
let second = a[1];
```

## 3.3 How Functions work

### How blocks work

- Cannot declare variables in `mod` level block.
- Blocks always evaluate to the last expression:
  - statement blocks evaluate to `()`, the empty tuple.
  - expression blocks evaluate to non-`()`.
  - loop blocks are supposed to evaluate to `()` due to uncertainty of how or when the loop terminated, but `loop` blocks can break with a value.
- Blocks can terminate the nearst containing `fn` with the `return` statement.
- Blocks can break out of the nearst containing `loop` with the `break` statement.

### How to use `;` in a block

- All expressions except the last or nesting _block statements_ in a block should end with `;`.
- If the last expression evaluates to `()`, it should end with `;` too.
- Block statements are those evaluating to `()`.
- If an exprssion evaluates to `()`, it should not be assigned to a variable.
- Expressions ending with `;` evaluate to `()`.

### How to use functions

- Use _snake case_ naming convention.
- Must declare parameters type if have any.
- Need to explicitly declare return type unless `()`.
- Always return a value or `()`.

```rust
fn main() {
    println!("{}", abs({ 1 + 3 } * 4)); // 16
    println!("{}", abs(-4)); // 4
}

fn abs(x: i32) -> i32 {
    opp(if x >= 0 {
        return x;
    } else {
        x
    })
}

fn opp(x: i32) -> i32 {
    {
        {
            {
                -x
            }
        }
    }
}

```

## 3.4 Comments

| Symbol     | Explanation             |
| :--------- | :---------------------- |
| `//`       | Line comment            |
| `/*...*/`  | Blok comment            |
| `//!`      | Inner line doc comment  |
| `/*!...*/` | Inner block doc comment |
| `///`      | Outer line doc comment  |
| `/**...*/` | Outer block doc comment |

```rust
// So we’re doing something complicated here, long enough that we need
// multiple lines of comments to do it! Whew! Hopefully, this comment will
// explain what’s going on.
```

## 3.5 Control Flow

### `if` expression

- `if` blocks are expressions.
- `if` condition must evaluate to `bool` type.
- parentheses around `if` condition are omitted.

```rust
let number = 6;

if number % 3 == 0 {
    println!("number is divisible by 3");
} else if number % 2 == 0 {
    println!("number is divisible by 2");
} else {
    println!("number is not divisible by 4, 3, or 2");
}
```

```rust
let condition = true;

let number = if condition {
    5
} else {
    6
};
```

### Repeating Code with `loop`

```rust
loop {
    println!("again!");
}
```

Unlike `while` and `for` mentioned below can be terminated both by condition turning into `false` and by `break` statement, `loop` can only be terminated by `break` statement, so a value can be returned from loops:

```rust
let mut counter = 0;

let result = loop {
    counter += 1;

    if counter == 10 {
        break counter * 2;
    }
};
```

### Conditional Loops with `while`

```rust
let mut number = 3;

while number != 0 {
    println!("{}!", number);

    number = number - 1;
}
```

### Looping Through a Collection with `for`

```rust
let a = [10, 20, 30, 40, 50];

for element in &a {
    println!("the value is: {}", element);
}
```

# 4. Understanding Ownership

## 4.1 What is Ownership?

Keeping track of what parts of code are using what data on the heap, minimizing the amount of duplicate data on the heap, and cleaning up unused data on the heap so you don’t run out of space, three common ways:

- **allocate and free:** efficient but not safe
  - memory leak
  - double free
  - dangling pointer
  - data race
- **garbage collection:** safe but not efficient
  - data race
- **ownership system:** safe and efficient

### Ownership rules

- Each block of data on the heap has a owner.
- A owner is a value on the stack.
- A value is bound to a variable.
- There can only be one owner at a time.
- When a value is assigned to another variable:
  - if the value owns data, it's moved,
  - otherwise, it's copied.
- When the owner goes out of scope, the block of data will be dropped.

```rust
let x = 5;
// this is a copy
let y = x;

println!("x = {}, y = {}", x, y);
```

```rust
let s1 = String::from("hello");
// this is a move, `s1` is unusable ever after
let s2 = s1;

println!("{}, world!", s1); // error
```

![`s1` is moved into `s2`](README.d/images/trpl04-04.svg)

```rust
let s1 = String::from("hello");
// this is a clone, copy value and owned data
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

Here are some of the types that are `Copy`, with a known, fixed size:

- All the integer types, such as `u32`.
- The Boolean type, `bool`, with values `true` and `false`.
- All the floating point types, such as `f64`.
- The character type, `char`.
- Tuples, if they only contain types that are also `Copy`.
  - `(i32, i32)` is `Copy`
  - but `(i32, String)` is not.
- Array of `Copy`, such as `[1, 2, 3]`.
- Slices, such `&str`, `&[32]`

### Ownership and Funtions

The semantics for passing a value to a function are similar to those for assigning a value to a variable. Passing a variable to a function will move or copy, just as assignment does. So without references and borrowing, this what happens:

```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String

    (s, length)
}
```

## 4.2 References and Borrowing

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

The reference operator `&` can creact a _reference_ that refers to a value. When the value owns some data, the reference won't take the ownership.

Having references as function parameters but without taking ownership is called _borrowing_.

![`s` won't take the ownership from `s1`](README.d/images/trpl04-05.svg)

> Note: The opposite of referencing by using `&` is _dereferencing_, which is accomplished with the dereference operator, `*`.

### Mutable References

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

Mutable references can make changes to the original values, but have one big restriction **to prevent data race at compile time**: a mutable reference **can't overlab** with another referenct to the same value in a particular scope. These code will fail:

```rust
let mut s = String::from("hello");

let r1 = &s; // no problem
let r2 = &s; // no problem
let r3 = &mut s; // BIG PROBLEM

//error, `r3` overlap with `r2` and `r1`
println!("{}, {}", r1, r2);
```

```rust
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;

println!("{}", r1); // error, `r2` overlap with `r1`
```

But this code is OK:

```rust
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;

println!("{}", r2); // OK
```

### Dangling References

When a reference refers to some data, ensure that the data will not go out of scope before the reference to the data does. This compile-time error:

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s // error, dangling reference
}
```

## 4.3 The Slice Type

### String Slices

A _string slice_ is a reference to part of a `String`, and it looks like this:

```rust
let s = String::from("hello world");

let hello = &s[0..5]; // or `&s[..5]`
let world = &s[6..11]; // or `&s[6..]`
let hello_world = &s[0..11]; // or &s[..]
```

![A *string slice* is a reference to part of a `String`](README.d/images/trpl04-06.svg)

> Note: String slice range indices must occur at valid UTF-8 character boundaries.

Slices can establish robust connection between logic:

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}


fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error! `&mut self` overlap with `word`

    println!("the first word is: {}", word);
}
```

### String Literals are Slices

String literals are stored inside the binary.

```rust
let s = "Hello, world!";
```

The type of `s` here is `&str`: it’s a slice pointing to that specific point of the binary. This is also why string literals are immutable; `&str` is an immutable reference.

### String Slices as Parameters

Using `&str` as parameters can take both string literals and `&String` values as arguments. This is a improvement on `first_word`:

```rust
fn first_word(s: &str) -> &str {
```

### Other Slices

Slices are not specific to strings. All sorts of other collections has slices, such as:

```rust
let a = [1, 2, 3, 4, 5]; // [i32; 5]

let slice = &a[1..3]; // &[i32]
```

# 5. Using Structs to Structure Related Data

A _struct_, or _structure_, is a custom data type that lets you name and package together multiple related values that make up a meaningful group.

## 5.1 Defining and Instantiating Structs

Structs are more flexible than tuples in ways that they don’t have to rely on the order of the _fields_ to specify or access the values of an _instance_.

```rust
#[derive(Debug)]
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
fn main() {
    let user1 = User {
        email: String::from("user1@example.com"),
        username: String::from("user1"),
        active: true,
        sign_in_count: 1,
    };
    // all fiels are mutable
    let mut user2 = User {
        email: String::from("user2@example.com"),
        username: String::from("user2"),
        ..user1 // the last line and no comma
    };
    user2.email = user1.email;
    let user3 = build_user(String::from("email@example.com"), String::from("user"));
    println!("{:?}", user3);
    println!("{:#?}", user3);
}
```

### Tuple Structs and Unit-Like Structs

```rust
// tuple structs
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);

// unit-like structs
struct V4;
struct V6;
```

## 5.2 An Example Program Using Structs

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

## 5.3 Method Syntax

_Methods_ are similar to functions: define in `impl` block and their first parameter is always `&self`, `&mut selt` or `self`.

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

### Methods with More Parameters

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

### Associated Functions

_Associated functions:_ define within `impl` blocks but don't take `self` as a parameter, such as `String::from`.

```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}

let sq = Rectangle::square(3);
```

### Multiple `impl` Blocks

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}
```
