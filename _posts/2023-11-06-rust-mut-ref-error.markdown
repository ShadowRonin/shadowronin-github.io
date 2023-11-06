---
layout: post
title:  "Learning Rust: Houston, we have a problem"
subtitle:  "A mutable reference error"
date:   2023-11-06 00:00:15 -0400
categories: rust, oort
background: '/assets/images/question-mark.jpg'
---

## Error

Recently, while working on my next Oort post, I came across the following error. 
```
rustc failed: error[E0507]: cannot move out of `self.scan_result` which is behind a mutable reference
```
This happened when I tried reassigning from one field in *Ship* to another.

```rust
pub struct Ship {
    scan_result: Option<ScanResult>,
    prev_scan_result: Option<ScanResult>,
}

impl Ship {
    fn scan(&mut self) {
        ...
        self.prev_scan_result = self.scan_result; // This is the problem line
        self.scan_result = Some(scan);
        ...
    }
}
```

While this would be a trivial thing in most languages, Rust does not allow you to move objects out of fields. Normally Rust checks when you move something, to make sure you don't use the old variable. However for fields that would require a lot of extra checks, as you would have to make sure that field is not used after the ownership is transferred. So instead we get this error.

## Solution

Rust comes with several helpers to deal with situations like this:
- [take](https://doc.rust-lang.org/std/mem/fn.take.html), replaces the field with a default value, then returns the content. 
- [swap](https://doc.rust-lang.org/std/mem/fn.swap.html), is used to swap two different muts
- [replace](https://doc.rust-lang.org/std/mem/fn.replace.html), replaces the field with the given value, then returns what was in field

For my use case *replace* works best, as I can assign the new *scan_result* value at the same time I move the old value to *prev_scan_result*. We simply need to change the problematic line to:
```rust
fn scan(&mut self) {
    ...
    self.prev_scan_result = std::mem::replace(&mut self.scan_result, Some(scan));
    ...
}
```