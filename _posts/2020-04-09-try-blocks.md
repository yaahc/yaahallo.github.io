---
layout: post
title: "Symme(try blocks)"
date: 2020-04-09
categories: rust try block trait
---

## Introduction

For the last few days the rust community has been abuzz with renewed
discussions about `try` syntax in response to a series of blog
posts([[1]](https://boats.gitlab.io/blog/post/failure-to-fehler/)[[2]](https://boats.gitlab.io/blog/post/why-ok-wrapping/))
by [@withoutboats](https://twitter.com/withoutboats). This has triggered some
fairly intense bikeshedding and vitriolic objections and, it seems, some
progress!

As of yesterday the lang team is moving forward with a
[proposal](https://github.com/rust-lang/rust/issues/70941) to resolve issues
with `try` blocks, and presumably, begin stabilizing them. It seems that the
majority is in favor of the proposal, where as historically this has been
blocked on disagreements about ok-wrapping. So what changed? Two things, first,
it became apparent that try blocks and ok wrapping could be separated from
proposals for try functions and `throws` syntax. And second, [a
comment](https://www.reddit.com/r/rust/comments/fw4jsx/from_failure_to_fehler/fmmtt7o/)
on reddit compellingly compared try and async blocks as similar effect systems.

## Background

Currently on rust, 

```rust
fn foo() -> Result<PathBuf, io::Error> {
    let base = env::current_dir()?;
    Ok(base.join("foo"))
}
```

To date, most proposals around Ok-wrapping and throw expressions have had a
pretty consistent form, something like this:

```rust
fn foo() -> PathBuf throws io::Error {
    let base = env::current_dir()?;
    base.join("foo")
}
```

## Separation From Try Functions


Try blocks, however, have already existed on nightly for a while with support
for Ok wrapping. You can go on [nightly right
now](https://play.rust-lang.org/?version=nightly&mode=debug&edition=2018&gist=347d8f346dff1fc29273aa436421ea3c)
and write the above like this:

```rust
#![feature(try_blocks)]

fn foo() -> Result<PathBuf, io::Error> {
    try {
        let base = env::current_dir()?;
        base.join("foo")
    }
}
```

It seems that Ok wrapping that is scoped to try blocks is much less
controversial than try functions, and by separating the proposals the lang team
was able to circumvent some of the issues that have been delaying try blocks.

## Comparison To Async

The previously mentioned
[comment](https://www.reddit.com/r/rust/comments/fw4jsx/from_failure_to_fehler/fmmtt7o/)
does a much better job explaining this than I can but it boils down to this
snippet:

```rust
       try { x? } == x == (try{ x })?
async { x.await } == x == (async{ x }).await
```

The idea is that `async {}` and `await` cancel eachother out, and the same is
true for `try {}` and `?`. This symmetry is particularly appealing to me, and
is what I would like to expand upon.

## What I'd Like to See Next

To start we gotta stabilize try blocks, this is already in progress. There's
not much to add on this point other than bikeshedding keywords like `raise`,
`fail`, `throw`, or `yeet` for the keyword to return errors within the try
block, which is not the point of this post.

Once we have try blocks though I imagine I'm not going to love having to put
`try { }` around the full body of every function that returns a Result, given
the extra indentation. To deal with this I would like to add support for
annotating the function body block itself with `try`. With this change the
above example becomes:


```rust
fn foo() -> Result<PathBuf, io::Error> try {
    let base = env::current_dir()?;
    base.join("foo")
}
```

With this change we already get one of the major benefits of Saoirse's
proposal, we don't have to edit every return location to wrap it with a result
when we introduce an error return channel. However the Result is still visible,
and you can think of the function definition as outside the Result monad while
the body is inside. And, if you want to keep Ok wrapping your returns you can
just leave off the try and carry on with your life.

Going back to the comparison for async, I don't see why we couldn't extend this
change to also apply to `async` blocks. I can't think of any particular reasons
why this would be desirable other than language consistency, but given the
intensity of the discussion over whether or not async fns should hide the
future from the return type I'm guessing there are at least some reasons to
allow this.

```rust
async fn foo() -> i32 {
    bar().await
}
```

Becomes:

```rust
fn foo() -> impl Future<Output = i32> async {
    bar().await
}
```

`async` and `try` blocks essentially end up becoming `do` notation for their
respective monads, and we can take this further. Theres no reason to restrict
this to only function body blocks:

```rust
let _: Result<T, E> = if s.is_empty() try { ... };

// Already valid
let _ = || try { ... };
let _ || async { ... };

match foo try {
    ...
}

let c = loop try {
    ...
}?;
```

And, if we wanted to, we could even apply this generalization to other forms of
blocks, other than `async` and `try`, like `if`, `match`, `for`, and `while`
expression blocks.

```rust
fn foo(bar: Bar) -> Baz match bar {
    Bar::Quix(q) => ...,
    ...
}

for bar in bars match bar {
    Bar::Quix(q) => ...,
    ...
}

if is_empty loop {
};

// these examples could go on for QUITE a while...
```

In addition to this, rust is moving away from having unsafe functions having an
[unsafe body block](https://github.com/rust-lang/rfcs/pull/2585) by default, so
this could go hand in hand with that change.

```rust
// body is safe by default
unsafe fn foo() {
    ...
}

// unsafe body after above rfc
unsafe fn foo() {
    unsafe {
        ...
    }
}

// unsafe body with generalized block effects
unsafe fn foo() unsafe {
    ...
}
```

If we treat blocks attached to items like `fn`, `if`, `match` and `loop` as
interchangeable with keyword blocks like `async`, `unsafe`, and `try` we
can intuitively use them in any combination.

This leaves room for discussions over whether or not there should also be a
`try` effect equivalent to `async fn`, where the function definition itself
also exists within the monad, similar to the original `throws function`
proposals. Also, it remains to be seen whether or not any of this can actually
parse successfully but I hope others agree with me that this is all worth
digging into.

## Conclusion

With all three of these proposals you would be able pick and choose how you
want to handle control flow with Try types. A single keyword enables the
effect, and you have a slew of scopes at which you can enable the effect. The
similarity between async effects and try effects would make both of them easier
to teach. Once you're familiar with one of the effects the other should come
intuitively.
