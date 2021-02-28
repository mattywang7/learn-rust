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

