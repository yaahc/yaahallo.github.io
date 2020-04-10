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
lang team is arriving at consensus to merge try blocks, where as historically
this has been blocked on disagreements about ok-wrapping. So what changed? Two
things, first, it became apparent that try blocks and ok wrapping could be
separated from proposals for try functions and `throws` syntax. And second, [a
comment](https://www.reddit.com/r/rust/comments/fw4jsx/from_failure_to_fehler/fmmtt7o/)
on reddit compellingly compared try and async blocks as similar effect systems.

## Background

Currently, fallibility in rust is expressed with the Result enum. This works
like any other enum, in that you have to construct each variant manually, with
results this is known as Ok-wrapping.

```rust
fn foo() -> Result<PathBuf, io::Error> {
    let base = env::current_dir()?;
    Ok(base.join("foo"))
}
```

For a while now there have been proposals to introduce fallible functions, also
known as try functions or throws syntax. These proposals have historically
looked a little like this.

```rust
fn foo() -> PathBuf throws io::Error {
    let base = env::current_dir()?;
    base.join("foo")
}
```

## Separating Ok-Wrapping From Try Functions

Ok-wrapping and try functions are often bundled together in proposals about
either, but this doesnt have to be the case. In fact, on nightly rust it's been
possible to enable try _blocks_ (with Ok-wrapping) for a while. You can go on
[nightly right
now](https://play.rust-lang.org/?version=nightly&mode=debug&edition=2018&gist=347d8f346dff1fc29273aa436421ea3c)
and write the previous example like this:

```rust
#![feature(try_blocks)]

fn foo() -> Result<PathBuf, io::Error> {
    try {
        let base = env::current_dir()?;
        base.join("foo")
    }
}
```

It turns out that separating the two proposals from each other allowed the
language team to come to consensus on try blocks and narrow the disagreements
to just function-level try. It's not the syntax sugar of wrapping returns that
blocked the try block proposal, it was the idea that this would lead
immediately to Result being stripped from the return type. And, when you dig
deeper into ok-wrapping as a block level effect, it starts to lead to some
attractive symmetry in the language.

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
true for `try {}` and `?`. When framed this way you can start to see `try` and
`async` as effects applied to blocks, rather than seeing them as just `try
blocks` or `async blocks`.

## What I'd Like to See Next

To start we gotta stabilize try blocks. This is already in progress so there's
not much to add on this point other than bikeshedding which keyword, `raise`,
`fail`, `throw`, or `yeet`  should be used to return errors within the try
block. And I dont want to get into that, it is not the point of this post.

However, once we have try blocks I imagine I'm not going to love having to put
a try block and indent level around the full body of every function that
returns a Result. To deal with this I would like to add support for annotating
the function body block itself with `try`. With this change the above example
becomes:


```rust
fn foo() -> Result<PathBuf, io::Error> try {
    let base = env::current_dir()?;
    base.join("foo")
}
```

With this change we already get one of the major benefits of Saoirse's
proposal, we don't have to edit every return location to wrap it with a result
when we introduce an error return channel. However the Result is still visible,
and you can think of the function definition as outside the "result monad" or
"try effect" while the function body block is inside. This way, if you want to
keep Ok wrapping your returns you can just leave off the try and carry on as
you always have.

Going back to the comparison for async, I don't see why we couldn't extend this
change to also apply to `async` blocks. I can't think of any particular reasons
why this would be desirable other than language consistency, but given the
intensity of the discussion over whether or not async fns should hide the
`Future` from the return type I'm guessing there are at least some reasons to
allow this.

```rust
async fn foo() -> i32 {
    bar().await
}
```

Could be also then be written as:

```rust
fn foo() -> impl Future<Output = i32> async {
    bar().await
}
```

In this version of rust `async` and `try` would effectively become block
effects and act like `do` notation for their respective monads. Now, we could
stop here, and only allow block effects in places where you couldnt already put
an expression, function body blocks and bare blocks. Or we can go for maximum
consistency and allow block effects in any expression with a block:

```rust
let d = if s.is_empty() try {
    let a = b?;
    a + c
} else {
    Err(...)
}?;

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

We could do the same thing with unsafe, and treat it like a block effect. Fun
fact, rust is currently moving away from unsafe functions having an [unsafe
body block](https://github.com/rust-lang/rfcs/pull/2585) by default, and this
generalization could go hand in hand with that change.

```rust
// with new rfc body is safe by default
unsafe fn foo() {
    ...
}

// new way to get an entirely unsafe body
unsafe fn foo() {
    unsafe {
        ...
    }
}

// same unsafe body with generalized block effects
unsafe fn foo() unsafe {
    ...
}
```

Or even treat expressions like `if`, `match` and `loop` as block effects as well:

```

fn foo(bar: Bar) -> Baz match bar {
    Bar::Quix(q) => ...,
    ...
}

for bar in bars match bar {
    Bar::Quix(q) => ...,
    ...
}

if is_empty loop try {
};

// these examples could go on for QUITE a while...
```

We treat the keywords in `fn`, `if`, `match`, `loop`, `async`, `unsafe`, and
`try`  as composable effects applied to blocks. This lets us concisely and
intuitively compose types and control-flow via syntax sugar.

The nice thing about this symmetry is that it helps neutralize a lot of
people’s concerns around rust getting “too big”. We’re not making it bigger,
we’re making it more consistent!

Finally, this leaves room for discussions over whether or not there should also be a
`try` effect equivalent to `async fn`, where the function definition itself
also exists within the monad, similar to the original `throws function`
proposals. And, it remains to be seen whether or not any of this can actually
parse given existing stable syntax, but I hope others agree with me that
this generalization is worth digging into.

## Conclusion

With all three of these steps you would be able pick and choose how you want to
handle control flow with Try types. A single keyword enables the effect, and
you have a slew of scopes at which you can apply it. The similarity between
async effects and try effects would make both of them easier to teach. Once
you're familiar with one of the effects the other should come intuitively.
