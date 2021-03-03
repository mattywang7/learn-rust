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