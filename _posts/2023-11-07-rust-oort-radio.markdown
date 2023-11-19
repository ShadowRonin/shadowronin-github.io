---
layout: post
title:  "Rust: Oort, radar"
subtitle: "Finding the enemy"
date:   2023-11-07 12:00:15 -0500
categories: [rust, oort]
background: '/assets/images/orion-nebula.jpg'
---

## What is radar?

In the next bit for [Oort](https://oort.rs/), they remove the `target` function we have been calling, so now we need a new way to find our targets, the radar. 


![](/assets/posts/2023-11-07/radar-1.png)


Here we can see an example of our radar. It has a certain width, an arc around our ship, and faces a certain direction. We can change the width in radian with the `set_radar_width` function, and change the direction it is facing with the `set_radar_heading`. Lastly we can read the closest ship from the radar using `scan`, this returns us a [ScanResult](https://docs.rs/oort_api/latest/oort_api/prelude/struct.ScanResult.html). ScanResult contains a lot of information about the scanned ship, but the parts we are focusing today are the position and velocity.

All code can be found [here](https://github.com/ShadowRonin/oort-ships/blob/main/tutorials/7_radar.rs) on my github repo.

## Scanning for targets

Our goal is to move our radar counter clockwise until we find a target, then we shall point our radar at the target until we lose it. Either from the target being destoryed or moving out of our way. It should eventually look something like this:
![](/assets/posts/2023-11-07/radar-scan.gif)

To accomplish this we are going to add a new `scan` method to our `ship` struct. We will also need to add `scan_result` and `prev_scan_result`, that way we can keep track of our target.
```rust
fn scan(&mut self) {
    // Attempt to get info from our radar
    if let Some(scan) = scan() {
        // Turns the radar towards the target
        let towards_scan  = scan.position - position();
        set_radar_heading(towards_scan.angle());

        // Update the scan results
        self.prev_scan_result = std::mem::replace(&mut self.scan_result, Some(scan));
    } else {
        // Turns the radar in a circle until we find a target
        set_radar_heading(radar_heading() + radar_width());

        // Remove old scans, as we have lost, or destroyed, the target
        self.prev_scan_result = Option::None;
        self.scan_result = Option::None;
    }
}
```

For why we use `std::mem::replace` instead of directly assigning `scan_result` to `prev_scan_result`, see my [Houston, we have a problem](/rust/oort/2023/11/06/rust-mut-ref-error.html) post.

Now we have to add a `self.scan()` call to the start of our `tick` method. Additionally `calculate_p1` was updated a little to make use of these scans, instead of the old `target()` function. Both of these changes can be found in the github code.

## Turning fast

While we have now accomplished the goal for this tutorial, we still fail several of the test cases Oort has. The two main reasons for this is that our ship doesnt move, and it turns very slowly. For now we are going to speed up how fast we turn, we'll deal with moving the ship later.

So far we have been using the `turn` function to turn our ship, but it is extreamly slow. Instead we are going to start to use the `tourque` function, which allows as to directly adjust the angular velocity of our ship, those spinning it. We will add a new `self.turn` method to our `ship`, which will accelerate into a spin as fast as it can, then start to break in such a way that we end up stop facing our target. The code for this method is a bit long, but simple, so I encourage you too check it out in my [github repo instead](https://github.com/ShadowRonin/oort-ships/blob/c5f2c191847d1b7ed7532b484ad59a1894a9fa5b/tutorials/7_radar.rs#L96).

## Wrapping up

In this post we've learned the basics of using the radar in Oort, as well as a way to turn the ship faster. In the next post we shall take a look at some more advanced radar uses, and start to actually move our ship towards it's target.


