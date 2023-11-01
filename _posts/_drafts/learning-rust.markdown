# Learning Rust

## The plan - 10/23/223
The plan is to follow [No Boilerplate](https://www.youtube.com/@NoBoilerplate)'s [How to Learn Rust](https://www.youtube.com/watch?v=2hXNd6x9sZs), then follow it up with some small projects.

The general outline per *No Boilerplate* is:
- Read [the book](https://doc.rust-lang.org/stable/book/title-page.html) through once without doing the excercises
- Then read it a second time, but [this version](https://rust-book.cs.brown.edu/) as it has more quizes and excercies
- During the second read:
    - Do related [rustlings](https://github.com/rust-lang/rustlings)
        - By the end you should do all the rustlings and should pick a few to do weekly as katas
    - Do related [Rust By Example](https://doc.rust-lang.org/rust-by-example/)
- (optional) Read Ultralearning by Scott H Young

After completing the above, then I plan on doing some more intense projects. Possible ideas:
- [Oort](https://oort.rs/?utm_source=tldrwebdev), spaceship game
- re-implement existing tools in rust
    - [build-your-own-x](https://github.com/codecrafters-io/build-your-own-x?utm_source=tldrwebdev)
    - Maybe redis?
- build a blog site, where i can post this stuff?
    - tbh dont really need rust for that unless we add comments or make it more of a CMS
- WASM, maybe maze generation/solving? or large math-y art?


## Day 1 - 10/24/2023
Finished setting up my enviroment this morning. Doing the hello worlds from the book, then going to read it some.

Read chapters 1-5. Ownership makes sense. Only real question is that at one point they imply that you should/could shadow vars instead of making them mut. And i dont really understand why that would be better? They made it sound like you should not use mut (when possible) cause then the value could change unexpectedly, but that would still effectively be the case when shadowing. The only advantage I see is chaging the type.

# Day 2 - 10/25/2023

Chapeters 6 - 10

took a glance at oort, its alot more than I thought, from what I saw. Like semi realilistic phsyics, radar, different ship types, messaging systems. Pretty neat

took a small break and did oort tutiorals 1-5. Pretty neat stuff. Having to deal with all this psyics stuff brings back good memories. Going to try to do a couple more tomorrow, saving my code in `oort` directory, need to figure out how to properly setup this up for local development.

Might be cool to either expand this project to deal more with orbital machinics or at least use this as inspiration. Should also play some KSP?

Huh, used my tut 5 code on tut 6, and worked just fine. Think #6 is supposed to teach me to add there accelation to calculate their change in V to properly calc their future position. However, it doesnt appear to be completly nessacary. After futz with it, this appears to be because I left some leeway about when to fire, e.g. +-5deg

# Day 3 - 10/26/2023

oort, tut 6-

# Day 4 - 10/27/28
Finally finished tut 7. Not super happy with my solution though

# Day 5 - 10/28/2023