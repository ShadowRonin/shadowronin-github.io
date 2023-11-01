---
layout: post
title:  "Learning Rust: part 1"
subtitle: "The start of my Rust journey"
date:   2023-11-01 00:00:15 -0400
categories: rust
background: '/assets/images/rusty-chain.jpg'
---
# Overview
I was inspired to start learning Rust a few days ago after watching [How to Learn Rust](https://www.youtube.com/watch?v=2hXNd6x9sZs) by [No Boilerplate](https://www.youtube.com/@NoBoilerplate). Rust was a language I've been interested for awhile now, both because the language and its features takes some novel approaches, and because I wanted to work with a more efficient language, that is closer to the metal. I've worked with C++ some in the past, but I found it to be cumbersome at times. I am excited to try Rust's more functional approach, which is closer to the JS I know well, as well as Rust's memory safe features. 

# The plan
I am going to try to roughly follow [No Boilerplate](https://www.youtube.com/@NoBoilerplate)'s plan, with some additional fiddling around with projects. In general:
- Read [the book](https://doc.rust-lang.org/stable/book/title-page.html) twice
    - This gives a strong foundation on the Rust language features and what how it all works
    - first read skip the exercises
    - second read follow [this version](https://rust-book.cs.brown.edu/) which has additional exercise and quizzes
- Do [rustlings](https://github.com/rust-lang/rustlings)
    - Pick out a few to re-do regularly(every week or two), this will act as short practice sessions to keep you fresh
- While reading the book, follow the related [Rust By Example](https://doc.rust-lang.org/rust-by-example/), which gives more detailed code examples

I plan on also adding some additional projects, before or during the above, including:
- [Oort](https://oort.rs/?utm_source=tldrwebdev)
    - a spaceship programming game in Rust
- re-implement existing tools in rust
    - [build-your-own-x](https://github.com/codecrafters-io/build-your-own-x?utm_source=tldrwebdev)
    - Maybe redis?
- A project using WASM
- Take a look at different Rust based tech stacks
    - [BETH](https://www.youtube.com/watch?v=cpzowDDJj24)
    - [No Boilerplate's recommended stack](https://www.youtube.com/watch?v=pocWrUj68tU)

# Starting the book
Over the last few days I've read the first half of [the book](https://doc.rust-lang.org/stable/book/title-page.html), up through chapter 11. From hearing some about Rust before hand, I was expecting this to be confusing and hard. So far though it reminds me a lot of TypeScript. The only feature that is going to take some getting used to is ownership.

## Ownership
EXPLAIN OWNERSHIP HERE

## Types and Enums
explain here, and how similar they are to typescript.