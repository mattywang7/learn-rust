# Getting started

## Hello, World!

To compile and run a .rs file:
```
rustc main.rs
./main
```
The `main` function is special: it is always the first code that runs in every executable Rust program.
Rust style is to indent with four spaces, not a tab.
`println!` calls a Rust macro. If it called a function instead, it would be entered as `println`.
Most lines of Rust code end with a semicolon.

Rust is an ahead-of-time compiled language, meaning you can compile a program and give the executable to someone else, 
and they can run it even without having Rust installed.
If you give someone a .rb, .py, or .js file, they need to have a Ruby, Python, or JavaScript implementation installed.
But in those languages, you only need one command to compile and run your program.

## Hello, Cargo!

Create a project with Cargo:
```
cargo new my_app
```

The *Cargo.toml* file is Cargo's configuration file.
The last line, `[dependencies]`, is the start of a section for you to list nay of your project's dependencies.
In Rust, packages of code are referred to as *crates*.

Cargo expects your source files to live inside the *src* directory.
The top-level project directory is just for README files, license information, configuration files, and anything else not related to your code.

Build your project:
```
cargo build
```
This command creates an executable file in *target/debug/hello_cargo* rather than in your current directory.
You can run this executable with:
```
./target/debug/hello_cargo
```

Running `cargo build` for the first time also causes Cargo to create a new file at the top level: *Cargo.lock*.
This file keeps track of the exact versions of dependencies in your project.
You won't ever need to change this file manually; Cargo manages its contents for you.

We can also use `cargo run` to compile the code and then run the resulting executable all in one command.

Cargo also provides a command called `cargo check`.
This command quickly checks your code to make sure it compiles but doesn't produce an executable.
`cargo check` is much faster than `cargo build`, because it skips the step of producing an executable.

When your project is finally ready for release, you can use `cargo build --release` to compile it with optimizations.
This command will create an executable in *target/release* instead of *target/debug*.
The optimizations make your Rust code run faster, but turning them on lengthens the time it takes for your program to compile.
If you're benchmarking hour code's running time, be sure to run `cargo build --release` and benchmark with the executable in *target/release*.

# Guessing Game

```rust
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to give an input!");
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        };
    }
}

```

To obtain user input and then print the result as output, we need to bring the `io` (input/output) library into scope.
```
use std::io;
```

## Storing Values with Variables

`let` statement is used to create a *variable*.
In Rust, variables are immutable by default.
Use `mut` before the variable name to make a variable mutable.

`String` is a string type provided by the standard library that is a growable, UTF-8 encoded bit of text.
The `::` syntax in the `::new` line indicates that `new` is an *associated function* of the `String` type.
An associated function is implemented on a type, in this case `String`, rather than on a particular instance of a `String`.
Some languages call this a static method.

Call the `stdin` function from the `io` module:
```
io::stdin()
    .read_line(&mut guess);
```
The `&` indicates that this argument is a *reference*, which gives you a way to let multiple parts of your code access one piece of data without needing to copy that data into memory multiple times.
References are a complex feature, and one of Rust's major advantages is how safe and easu it is to use references.
References are immutable by default. Hence, you need to write `&mut guess` rather than `&guess` to make it mutable.

When you call a method with the `.foo()` syntax, it's often wise to introduce a newline and other whitespace to help break up long lines.

The `Result` types are *enumerations*, often referred to as *enums*.
An enumeration is a type that can have a fixed set of values, and those values are called the enum's variants.

For `Result`, the variants are `Ok` or `Err`.
The `Ok` variant indicates the operation was successful, and inside `Ok` is the successfully generated value.
The `Err` variant means the operation failed, and `Err` contains information about how or why the operation failed.

An instance of `io::Result` has an `expect` method that you can call.
If this instance of `io::Result` is an `Err` value, `expect` will cause the program to crash and display the message that you passed as an argument to `expect`.
If this instance of `io::Result` is an `Ok` value, `expect` will take the return value that `Ok` is holding and return just that value to you so you can use it.

## Using a Crate to Get More Functionality

Remember that a crate is a collection of Rust source code files.
The project we've been building is a *binary crate*, which is an executable.
The `rand` crate is a *library crate*, which contains code intended to be used in other programs.

Before we can write code that uses `rand`, we need to modify the *Cargo.toml* file to include the `rand` rate as a dependency.

Now that we have an external dependency, Cargo fetches the latest versions of everything from the *registry*,
which is a copy of data from Crates.io. Crates.io is where people in the Rust ecosystem post their open source Rust projects for others to use.

Cargo has a mechanism that ensures you can rebuild the same artifact every time you or anyone else builds your code: 
Cargo will use only the versions of the dependencies you specified until you indicate otherwise.
When you build a project for the first time, Cargo figures out all the versions of the dependencies that fit the criteria 
and then writes them to the *Cargo.lock* file.
When you build your project in the future, Cargo will see that the *Cargo.lock* file exists and use the versions specified there rather than doing all the work of figuring out versions again.

When you do want to update a crate, Cargo provides another command, `update`, which will ignore the *Cargo.lock* file
and figure out all the latest versions that fit you specifications in *Cargo.toml*.
If that works, Cargo will write those versions to the *Cargo.lock* file.

Another neat feature of Cargo is that you can run the `cargo doc --open` command, 
which will build documentation provided by all of your dependencies locally and open it in your browser.

A `match` expression is made up of *arms*.
An arm consists of a *pattern* and the code that should be run if the value given to the beginning of the `match` expression fits that arms' pattern.
The `match` construct and patterns are powerful features in Rust that let you express a variety of situations your code might encounter and make sure that you handle them all.

Rust has a strong, static type system.
However, it also has type inference.
Rust allows us to *shadow* the previous value of `guess` with a new one.
This feature is often used in situations in which you want to convert a value from one type to another type.
Shadowing lets us reuse the `guess` variable name rather than forcing us to create two unique variables.

`u32` is a good default choice for a small positive number.

Switching from an `expect` call to a `match` expression is how you generally move from crashing on an error to handling the error.

# Common Programming Concepts

## Variables and Mutability

In Rust, the compiler guarantees that when you state that a value won't change, 
it really won't change.
That means that when you're reading and writing code, you don't have to keep track of how and where a value might change.
Your code is thus easier to reason through.

You aren't allowed to use `mut` with constants.
Constants aren't just immutable by default -- they're always immutable.

You declare constants using the `const` keyword instead of `let`, and the type of the value *must* be annotated.
Constants can be declared in any scope, including the global scope, which makes them useful for values that many parts of code need to know about.

The last difference is that constants may be set only to a constant expression,
not the result of a function call or any other value that could only be computed at runtime.

Constants are valid for the entire time a program runs, within the scope they were declared in,
making them a useful choice for values in your application domain that multiple parts of the program might need to know about.

### Shadowing
We can shadow a variable by using the same variable's name and repeating the use of `let` keyword as follows:
```rust
fn main() {
    let x = 5;
    let x = x + 1;
    let x = x * 2;
    println!("{}", x);
}
```

Shadowing is different from making the variable `mut`, because we'll get a compile-time error if we accidentally try to reassign to this variable without using `let`.
By using `let`, we can perform a few transformation on a value but have the variable be immutable after those transformations have been completed.

The other difference between `mut` and shadowing is that because we're effectively creating a new variable when we use the `let` keyword again,
we can change the type of the variable but reuse the same name.

## Data Types
Keep in mind that Rust is a statically typed language, which means it must know the types of all variables at compile time.

### Scalar Types
A *scalar* type represents a single value.
Rust has four primary scalar types: integers, floating-point, numbers, Booleans, and characters.

#### Integer Types
`u32` -- unsigned integer that takes up 32 bits of space.

Each variant can be either signed or unsigned and has an explicit size.

Additionally, the `isize` and `usize` types depend on the kind of computer your program is running on:
64 bits if you're on a 64-bit architecture and 32 bits if you're on a 32-bit architecture.

Rust's defaults are generally good choices, and integer types default to `i32`:
this type is generally the fastest, even on 64-bit systems.

#### Floating-Point Types
Rust's floating-point types are `f32` and `f64`, which are 32 bits and 64 bits in size, respectively.
The default if `f64` because on modern CPUs it's roughly the same speed as `f32` but is capable of more precision.

#### The Boolean Type
The Boolean type in Rust has two possible values: `true` and `false`.
Booleans are one byte in size.

#### Character Types
Rust's `char` type is four bytes in size and represents a Unicode Scalar Value.

### Compound Types
*Compound types* can group multiple values into one type.
Rust has two primitive compound types: tuples and arrays.

#### The Tuple Type
In `()`.

A tuple is a general way of grouping together a number of values with a variety of types into one compound type.
Tuples have a fixed length: once declared, they cannot grow or shrink in size.

Each position in the tuple has a type, and the types of the different values in the tuple don't have to be the same.

To get the individual values out of a tuple, we can use pattern matching to destructure a tuple value, like:
```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    let (x, y, z) = tup;
    println!("The value of y is {}", y);
}
```

In addition to destructuring through pattern matching, we can access a tuple element directly by using a period(`.`) followed by the index of the value we want to access:

#### The Array Type
In `[]`.

Unlike a tuple, every element of an array must have the same type.
Arrays in Rust also have a fixed length, like tuples.

Arrays are useful when you want your data allocated on the stack rather than the heap or when you want to ensure you always have a fixed number of elements.

If you're unsure whether to use an array or a vector (provided by the standard library), you should probably use a vector.

You would write an array's type by using square brackets, and within the brackets include the type of each element, a semicolon, 
and then the number of elements in the array:
```rust
fn main() {
    let a: [i32; 5] = [1, 2, 3, 4, 5];
    let b = [3; 5];     // [value for all elements; size]
}
```

You can access elements of an array using indexing:
```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
    let first = a[0];
    let second = a[1];
}
```

When you attempt to access an element using indexing, Rust will check that the index you've specified is less than the array length.
If the index is greater than or equal to the array length, Rust will panic.
In many low-level language, this kind of check is not done, and when you provide an incorrect index, invalid memory can be accessed.
Rust protects you against this kind of error by immediately exiting instead of allowing the memory access and continuing.

## Functions
Rust code uses *snake case* as the conventional style for function and variable names.
In snake case, all letters are lowercase and underscores separate words.

Rust doesn't care where you define your functions, only that they're defined somewhere.

In function signatures, you must declare the type of each parameter.
This is a deliberate decision in Rust's design: requiring type annotations in function definitions means the compiler almost never needs you to use them elsewhere in the code to figure out what you mean.

### Function Bodies Contain Statements and Expressions

Rust is an expression-based language. *Statements* are instructions that perform some action and do not return a value.
*Expressions* evaluate to a resulting value.

Statements do not return values.
Therefore, you can't assign a `let` statement to another variable; you'll get an error:
```rust
fn main() {
    let x = (let x = 6);
}
```
This is different from what happened in other languages, such as C and Ruby, where the assignment returns the value of the assignment.

Expressions evaluate to something and make up most of the rest of the code that you'll write in Rust.
Consider a simple math operation, such as `5 + 6`, which is an expression that evaluates to the value `11`.
Calling a function is an expression. Calling a macro is an expression.
The block `{}` we use to create new scopes, is an expression.
```rust
fn main() {
    let x = 5;
    let y = {
        let x = 3;
        x + 1
    };
    println!("The value of y is {}", y);
}
```
Expressions do not include ending semicolons.
If you add a semicolon to the end of an expression, you turn it into a statement, which will then not return a value.

### Functions with Return Values
We don't name return values, but we do declare their type after an arrow(`->`).

In Rust, the return value of the function is synonymous with the value of the final expression in the block of the body of a function.
You can return early from a function by using the `return` keyword and specifying a value, ut most functions return the last expression implicitly.
```rust
fn main() {
    let x = five();
    println!("The value of x is {}", x);
}

fn five() -> i32 {
    5
}
```