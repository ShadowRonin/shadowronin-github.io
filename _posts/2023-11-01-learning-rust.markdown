---
layout: post
title:  "Learning Rust: the plan"
subtitle: "The start of my Rust journey"
date:   2023-11-01 00:00:15 -0400
categories: rust
background: '/assets/images/rusty-chain.jpg'
---
# Overview
I was inspired to start learning Rust a few days ago after watching [How to Learn Rust](https://www.youtube.com/watch?v=2hXNd6x9sZs) by [No Boilerplate](https://www.youtube.com/@NoBoilerplate). Rust is a language I've been interested for awhile now, both because of it has some novel approaches I haven't seen in other langs, and because it's closer to the metal (and more efficient) then the JS and C# I am used too. I've worked with C++ some in the past, but I found it to be cumbersome at times. I am excited to try Rust's more functional approach, as well as it's memory safe features. 

# The plan
I am going to try to roughly follow [No Boilerplate](https://www.youtube.com/@NoBoilerplate)'s plan, with the additional of some projects. In general:
- Read [the book](https://doc.rust-lang.org/stable/book/title-page.html) twice
    - This gives a strong foundation on the Rust language features
    - on the first read skip the exercises
    - on the second follow [this version](https://rust-book.cs.brown.edu/), which has additional exercise and quizzes
- Do [rustlings](https://github.com/rust-lang/rustlings)
    - rustlings is a collection of small exercises
    - Pick out a few to re-do regularly(every week or two), this will act as short practice sessions to keep you fresh
- While reading the book, follow the related [Rust By Example](https://doc.rust-lang.org/rust-by-example/)
    - This gives more detailed code examples

I plan on also adding some additional projects, before or during the above, including:
- [Oort](https://oort.rs/?utm_source=tldrwebdev)
    - a spaceship programming game in Rust
- re-implement existing tools in rust
    - [build-your-own-x](https://github.com/codecrafters-io/build-your-own-x?utm_source=tldrwebdev)
    - Maybe redis?
- Take a look at different Rust based tech stacks
    - [BETH](https://www.youtube.com/watch?v=cpzowDDJj24)
    - [No Boilerplate's recommended stack](https://www.youtube.com/watch?v=pocWrUj68tU)
- A project using WASM
- Look into data processing with Rust

# Starting the book
Over the last few days I've read the first half of [the book](https://doc.rust-lang.org/stable/book/title-page.html), up through chapter 11. From hearing some about Rust before hand, I was expecting this to be confusing, so far though it reminds me a lot of TypeScript. The only feature that is going to take some getting used to is ownership.

## Ownership
Ownership is an interesting feature of Rust that helps make sure memory usage is safe; that there are no leaks or invalid references, two serious possibilities in C++. In short, the function that created a variable "owns" it, and ownership is passed on if the variable is passed to another function(or struct). Once ownership is passed the original function can no longer use the variable. Note ownership does not apply to *scaler* values. Example:
```rust
fn main() {
    let a = String::from("foo");

    // Pass `a` to `print`, thereby passing the ownership
    print(a); 

    // Now `a` is no longer valid in `main`, since `main` does not own it

    // If we try to print again..
    print(a);
    // We would get a compiler error
    // `error[E0382]: use of moved value: `a``

}
fn print(a_string: String) {
    println!("{a_string}");
}

```

If we still need a variable after passing ownership we must:
- create a copy before
- have the function we are passing to, return the variable, thereby passing ownership back
- instead pass a reference of the variable, this will let you keep ownership

Ownership can take a minute to get used too, but once you do it is pretty natural to keep track of. To learn more checkout [chapter 4 of the book](https://doc.rust-lang.org/stable/book/ch04-00-understanding-ownership.html).

## Structs

Rust doesn't have classes like OOP languages, instead it has `structs` and `enums`. These remind me of a mix of classes and interfaces/types in TypeScript. You can use them to define new types, like so:
```rust
struct Foo {
    bar: u64,
}
```

Which could then get used like:
```rust
let a = Foo { bar: 1 };
println!("{}", a.bar); // prints 1
```

Additional, you can add methods to structs by adding an `impl`:
```rust
impl Foo {
    fn print(&self) {
        println!("{}", self.bar);
    }
}
```

Called like 
```rust
a.print(); // prints 1
```

Note that there is no way (without abusing macros at least) to do inheritance with structs. Inheritance can add a lot of unneeded complexity, instead [composition is preferred](https://en.wikipedia.org/wiki/Composition_over_inheritance).

-----

In my next post, I will take a break from the book to mess around with [Oort](https://oort.rs/?utm_source=tldrwebdev).