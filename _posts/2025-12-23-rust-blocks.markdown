---
author: emwilson
comments: true
date: 2025-12-23 13:00:00+00:00
layout: post
slug: rust-blocks
title: When Rust Code Blocks Beat Functions
excerpt: "Reflecting on Rust's code blocks. My instinct says to use named functions, but these inline blocks carve out their own, sometimes indispensable, purpose. It's about knowing when."
photo:
  source: /images/2025/12/confusing-read
  url: /images/2025/12/confusing-read.jpg
  description: Even I Have to Keep Re-Reading This
categories:
- Rust
- Programming
published: true
---

Rust beginners often ask:

“Why would I use a code block when a small named function would do the same thing?”

Totally fair question. I asked the same thing for a long time.

This article actually came out of some self‑reflection after running into [“The Block Pattern” by notgull](https://notgull.net/block-pattern/) on Hacker News. It pushed me to think more critically not just about Rust, but about my own language use and biases while programming — especially the reflex to assume that “named = better” or “function = more correct, more understandable.”

Once you start questioning those assumptions, Rust blocks stop looking like a weird quirk and start looking like a deliberate tool.

What follows are real situations where code blocks aren't just convenient, but genuinely the right tool.

## What a Rust block really is

In Rust, a block is:

*	A scope (variables live only inside it)
* An expression (it evaluates to a value)
* A lifetime boundary (drops happen at the end of the block)

```rust
let x = {
    let a = 2;
    let b = 3;
    a + b
};
```

Think of blocks as inline, unnamed functions with precise lifetime control.

## 1. Precisely ending borrows, the most important use (according to ChatGPT, anyway)

Blocks let you force a borrow to end early, something functions often can't do cleanly.

```rust
let mut v = vec![1, 2, 3];

{
    let first = &v[0];
    println!("{}", first);
} // borrow ends here

v.push(4); // ✅ allowed
```

Why a function is worse:
* The borrow's lifetime may be conservatively extended
* You lose clarity about where the borrow ends
* The compiler has fewer guarantees

This pattern is essential when working with:
* Iterators borrowing collections
* RefCell
* Interior mutability

If we tried to use a function, this is what would have happened:

```rust
fn borrow_first(vec: &Vec<i32>) -> &i32 {
    &vec[0]
}

let mut v = vec![1, 2, 3];
let first = borrow_first(&v);
println!("{}", first);
v.push(4); // ❌ ERROR: cannot borrow `v` as mutable while `first` is live
```

## 2. Limiting the lifetime of locks and guards

[RAII guards](https://rust-unofficial.github.io/patterns/patterns/behavioural/RAII.html) unlock when they are dropped — blocks let you control exactly when that happens. This is 90% where you will see blocks in code that I've written.

```rust
use std::sync::Mutex;

let data = Mutex::new(vec![1, 2, 3]);

{
    let mut guard = data.lock().unwrap();
    guard.push(4);
} // mutex unlocked here

// safe to do other work now
```

Why this matters:
* Makes critical sections obvious
* Prevents accidental long-held locks
* Especially important in async or multi-threaded code

## 3. Temporary values that must not escape

Blocks are perfect for pipelines of intermediate values that shouldn't leak into the outer scope.

```rust
let checksum = {
    let tmp = expensive_preprocessing();
    let normalized = normalize(tmp);
    hash(normalized)
};
```

Benefits:
* No scope pollution
* Clear transformation flow
* No throwaway helper function

## 4. Inline branching logic without helper functions

Blocks shine when you want branching logic exactly where the value is created.

```rust
let config = {
    if env::var("MODE") == Ok("prod".into()) {
        load_prod_config()
    } else {
        load_dev_config()
    }
};
```

A function here would:
* Add indirection
* Require an artificial name
* Often be used only once

Blocks keep logic local and readable.

## 5. Isolating fallible logic with ?

Blocks pair well with ? to group error-prone operations.

```rust
let result = {
    let a = parse_a()?;
    let b = parse_b()?;
    combine(a, b)
}?;
```

Why this is useful:
* Keeps error-prone logic together
* Avoids deep nesting
* Doesn't force new error boundaries

## 6. Refining values with shadowing

Rust encourages shadowing, and blocks make it safer and clearer.

```rust
let input = read_input();

let input = {
    let input = input.trim();
    let input = input.to_lowercase();
    input
};
```

Advantages:
* Each transformation is explicit
* No accidental reuse of partial values
* The final value keeps the same semantic name

## 7. Macro hygiene and safety

Macros rely heavily on blocks to avoid variable collisions and scope leaks.

{% raw %}
```rust
macro_rules! timed {
    ($expr:expr) => {{
        let start = std::time::Instant::now();
        let result = $expr;
        println!("Took {:?}", start.elapsed());
        result
    }};
}
```
{% endraw %}

Without blocks, macros would be unsafe and unpredictable.

## When a block beats a function

Use blocks when:
* You care about exact drop timing
* You need tighter borrow or lifetime control
* The logic is single-use and local
* Naming a function would be artificial
* You want to visually group related operations

Use functions when:
* Logic is reused
* The abstraction has a meaningful name
* You want to expose behavior, not mechanics

## Final takeaway

Rust blocks aren't about reuse — they're about control.

They give you:
* Precise lifetimes
* Clear borrow boundaries
* Scope hygiene
* Expression-level composition

That's why they exist even though functions could do the computation.

If you internalize blocks as “unnamed, scoped expressions with drop semantics,” Rust code starts to feel a lot more intentional.

## Common block anti-patterns (or: _how to overdo a good thing_)

Blocks are powerful, but like most sharp tools, they're easy to overuse. When that happens, readability and design tend to suffer. Below are the most common anti-patterns — and what to do instead.

### 1. Blocks that hide reusable logic

```rust
let result = {
    let a = compute_a();
    let b = compute_b();
    a * b
};
```

If this logic appears in multiple places, a block becomes a liability. The lack of a name hides intent and encourages copy‑paste reuse.

Prefer a function when:
* The logic is used more than once
* There is a clear domain name for the behavior
* You want to test it independently

### 2. Deeply nested blocks as control flow

```rust
let value = {
    if cond1 {
        {
            if cond2 {
                compute_a()
            } else {
                compute_b()
            }
        }
    } else {
        compute_c()
    }
};
```

This quickly becomes hard to read and reason about.

Prefer:
* Early returns
* match expressions
* Extracting a small helper function

Blocks should flatten logic, not obscure it.

### 3. Using blocks to simulate variable reassignment

```rust
let x = 5;

let x = {
    let x = x + 1;
    let x = x * 2;
    x
};
```

While legal, this is often harder to read than simple shadowing without an extra block.

Prefer:
* Straightforward shadowing
* A block only when you need to limit intermediate lifetimes

### 4. Over-scoping trivial expressions

```rust
let y = {
    3
};
```

Blocks are not free abstractions. When there's no lifetime, borrow, or readability benefit, the block adds noise.

Rule of thumb:
If removing the block doesn't change behavior or clarity, it probably shouldn't be there.

### 5. Blocks that capture too much context

```rust
let result = {
    use std::env;
    use std::fs;

    let path = env::var("PATH").unwrap();
    fs::read_to_string(path).unwrap()
};
```

Large blocks that pull in many dependencies are often doing too much.

Smell:
* Multiple responsibilities
* Many imports or side effects
* Hard to test in isolation

This is usually a sign the code wants to be a function.

### 6. Using blocks instead of meaningful names

Blocks are anonymous by design. If the code reads better with a name, that's a strong signal.

```rust
let score = calculate_score(player, game_state);
```

is almost always clearer than an inline block with the same logic.