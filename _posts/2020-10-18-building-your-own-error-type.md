---
layout: post
title: "Building Your Own Error Type: Part 1"
date: 2020-10-18 10:30
categories: rust error handling
---

# Introduction

So yesterday I had a friend on twitter [ask for help](https://twitter.com/zkat__/status/1317613730830512128?s=20) creating an error type for a library that acted like anyhow but was suitable to expose as part of a library API. I tossed something together based on my experience writing error handling libraries. Then [another mutual](https://twitter.com/felipesere/status/1317790349880938497) pointed out that they were amazed by the code snippet I'd posted and asked if I could write a blog post about it. So here I am.

I've decided to structure this explanation by going over the problem I was trying to solve, and how I built up the solution. The techniques here aren't new or anything, the overall design is very similar to `std::io::Error` and the error kind pattern described in [The Failure Book](https://rust-lang-nursery.github.io/failure/), with a little hint of trait fun that I learned from working with the source code in `anyhow` in the process of writing `eyre`. I just applied the design patterns that are common in the ecosystem to this particular problem in the best way I could think of. Most importantly, I don't want anyone coming away from this post thinking this is the best way to write an error type for a library, and that you should all copy this pattern. My goal is to get everyone comfortable mixing and matching design patterns to get an error type that matches their needs and their software design patterns best.

## The Problem and Plan

So first let me summarize the needs that Kat described for her theoretical error type. It needed a programatic interface suitible for a library. I interpreted this to mean that it needed an enum that could easily be matched upon to handle specific kinds of errors. It needed the ability to capture backtraces, and it needed the ability to add contextual stack traces to the error, which I interpreted to mean she wanted to add new error messages to the error without necessarily changing the error's kind that you would match against, something like the `.wrap_err` method on `eyre` or the `.context` method on `anyhow`.

So here I formed a vague plan, I knew for the API I'd want to to make an Error type and a Kind type, where the error was a struct with private internal members, and the Kind was a `non_exhaustive` enum. Getting the backtrace in there is pretty easy, just add a member for it to the outer `Error` type and capture it whenever an `Error` is constructed. The last feature is where I had to get a little clever, my plan was to make a separate `ErrorMessage` type that could be constructed from arbitrary `impl Display` types, and which optionally stored another `ErrorMessage` to act as the source to grab the previous error message whenever you add a new error message. I imagined an API like this:

```rust
fn parse_config() -> Result<(), Error> {
    Err(Kind::NoFile).wrap_err("config is invalid")?
}
```

Where when you print this error with an error reporter like `anyhow` or `eyre` you'd get:

```
Error:
    0: config is invalid
    1: file not found

Backtrace:
    ...
```

Or something along those lines.

### Programatic API - aka Reacting To Specific Errors

So I started by sketching out the API and the basic types. I started with just the handling bit because it seemed easiest. I split it into a `lib.rs` file and another `examples/report.rs` file to easily run the "test" to visually inspect the output and make sure that all the APIs worked together as expected:

`lib.rs`:

```rust
#![allow(clippy::try_err)]

pub fn foo_handle() -> Result<(), Error> {
    Err(Kind::Important)?
}

pub fn foo_no_handle() -> Result<(), Error> {
    Err(Kind::NotImportant)?
}

pub struct Error {
    kind: Kind,
}

#[non_exhaustive]
pub enum Kind {
    Important,
    NotImportant,
}
```

`examples/report.rs`:

```rust
fn main() -> Result<(), eyre::Report> {
    match playground::foo_no_handle() {
        Ok(_) => {}
        Err(e) if e.kind() == playground::Kind::Important => Err(e)?,
        Err(_) => {}
    }

    match playground::foo_handle() {
        Ok(_) => {}
        Err(e) if e.kind() == playground::Kind::Important => Err(e)?,
        Err(_) => {}
    }

    Ok(())
}
```

Now that I've got that setup here's where I like to let `rustc` take the driver's seat so lets see what it says.

<pre><font color="#F15D22"><b>error[E0277]</b></font><b>: `?` couldn&apos;t convert the error to `Error`</b>
 <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:8:28
  <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>7</b></font> <font color="#48B9C7"><b>| </b></font>pub fn foo_no_handle() -&gt; Result&lt;(), Error&gt; {
  <font color="#48B9C7"><b>| </b></font>                          <font color="#48B9C7"><b>-----------------</b></font> <font color="#48B9C7"><b>expected `Error` because of this</b></font>
<font color="#48B9C7"><b>8</b></font> <font color="#48B9C7"><b>| </b></font>    Err(Kind::NotImportant)?
  <font color="#48B9C7"><b>| </b></font>                           <font color="#F15D22"><b>^</b></font> <font color="#F15D22"><b>the trait `From&lt;Kind&gt;` is not implemented for `Error`</b></font>
  <font color="#48B9C7"><b>|</b></font>
  <font color="#48B9C7"><b>= </b></font><b>note</b>: the question mark operation (`?`) implicitly performs a conversion on the error value using the `From` trait
  <font color="#48B9C7"><b>= </b></font><b>note</b>: required by `from`

<font color="#F15D22"><b>error</b></font><b>: aborting due to 2 previous errors</b></pre>

Alright, lets go ahead and add that rq:

```rust
impl From<Kind> for Error {
    fn from(kind: Kind) -> Self {
        Self { kind }
    }
}
```

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo check --example report
<font color="#C4A000"><b>warning</b></font><b>: field is never read: `kind`</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:12:5
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>12</b></font> <font color="#48B9C7"><b>| </b></font>    kind: Kind,
   <font color="#48B9C7"><b>| </b></font>    <font color="#C4A000"><b>^^^^^^^^^^</b></font>
   <font color="#48B9C7"><b>|</b></font>
   <font color="#48B9C7"><b>= </b></font><b>note</b>: `#[warn(dead_code)]` on by default

<font color="#C4A000"><b>warning</b></font><b>: 1 warning emitted</b>

<font color="#4E9A06"><b>    Checking</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0599]</b></font><b>: no method named `kind` found for struct `playground::Error` in the current scope</b>
 <font color="#48B9C7"><b>--&gt; </b></font>examples/report.rs:4:21
  <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>4</b></font> <font color="#48B9C7"><b>| </b></font>        Err(e) if e.kind() == playground::Kind::Important =&gt; Err(e)?,
  <font color="#48B9C7"><b>| </b></font>                    <font color="#F15D22"><b>^^^^</b></font> <font color="#F15D22"><b>private field, not a method</b></font></pre>


Alright, lets go silence that dead code warning rq and then add the kind method next:

```rust
#![allow(dead_code)]

impl Error {
    pub fn kind(&self) -> Kind {
        self.kind
    }
}
```

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo check --example report
<font color="#4E9A06"><b>    Checking</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0507]</b></font><b>: cannot move out of `self.kind` which is behind a shared reference</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:18:9
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>18</b></font> <font color="#48B9C7"><b>| </b></font>        self.kind
   <font color="#48B9C7"><b>| </b></font>        <font color="#F15D22"><b>^^^^^^^^^</b></font> <font color="#F15D22"><b>move occurs because `self.kind` has type `Kind`, which does not implement the `Copy` trait</b></font>

<font color="#F15D22"><b>error</b></font><b>: aborting due to previous error</b>
</pre>

Oops, forgot to impl copy, lets go add that rq, and `Debug` while we're at it:

```rust
#[derive(Debug, Copy, Clone)]
#[non_exhaustive]
pub enum Kind {
    Important,
    NotImportant,
}
```

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo check --example report
<font color="#4E9A06"><b>    Checking</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0369]</b></font><b>: binary operation `==` cannot be applied to type `Kind`</b>
 <font color="#48B9C7"><b>--&gt; </b></font>examples/report.rs:4:28
  <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>4</b></font> <font color="#48B9C7"><b>| </b></font>        Err(e) if e.kind() == playground::Kind::Important =&gt; Err(e)?,
  <font color="#48B9C7"><b>| </b></font>                  <font color="#48B9C7"><b>--------</b></font> <font color="#F15D22"><b>^^</b></font> <font color="#48B9C7"><b>---------------------------</b></font> <font color="#48B9C7"><b>Kind</b></font>
  <font color="#48B9C7"><b>| </b></font>                  <font color="#48B9C7"><b>|</b></font>
  <font color="#48B9C7"><b>| </b></font>                  <font color="#48B9C7"><b>Kind</b></font>

<font color="#F15D22"><b>error[E0277]</b></font><b>: the trait bound `playground::Error: std::error::Error` is not satisfied</b>
 <font color="#48B9C7"><b>--&gt; </b></font>examples/report.rs:4:68
  <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>4</b></font> <font color="#48B9C7"><b>| </b></font>        Err(e) if e.kind() == playground::Kind::Important =&gt; Err(e)?,
  <font color="#48B9C7"><b>| </b></font>                                                                   <font color="#F15D22"><b>^</b></font> <font color="#F15D22"><b>the trait `std::error::Error` is not implemented for `playground::Error`</b></font>
  <font color="#48B9C7"><b>|</b></font>
  <font color="#48B9C7"><b>= </b></font><b>note</b>: required because of the requirements on the impl of `From&lt;playground::Error&gt;` for `Report`
  <font color="#48B9C7"><b>= </b></font><b>note</b>: required by `from`
</pre>

Looks like its time to add some more traits to `Kind` and `Error`, if memory serves you only need `PartialEq` to use `==`, though I'm surprised the error message here doesn't indicate that:

```rust
// struct Error { ... }

impl std::error::Error for Error {}

#[derive(Debug, Copy, Clone, PartialEq)]
#[non_exhaustive]
pub enum Kind {
    Important,
    NotImportant,
}
```

At this point I know rustc is gonna tell me to add `Debug` and `Display` impls to `Error` so I'll just go ahead and do that myself. In this case however I know that I'm going to change this later, but for now to keep the example easier for a blog post I'm just gonna use the `Display` impl on `Kind` as the source of the `Display` impl on `Error`, which I can later change around when I add my `.wrap_err` function.

```rust
use std::fmt;

impl fmt::Display for Error {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        self.kind.fmt(f)
    }
}

impl fmt::Display for Kind {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        use Kind::*;

        match self {
            Important => write!(f, "this error was important to us"),
            NotImportant => write!(f, "this error was not a place of honor"),
        }
    }
}
```

Now `rustc` should be happy, lets see what it says.

`rustc`:


<pre><font color="#4E9A06"><b>❯</b></font> cargo check --example report
<font color="#C4A000"><b>warning</b></font><b>: returning an `Err(_)` with the `?` operator</b>
 <font color="#48B9C7"><b>--&gt; </b></font>examples/report.rs:4:62
  <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>4</b></font> <font color="#48B9C7"><b>| </b></font>        Err(e) if e.kind() == playground::Kind::Important =&gt; Err(e)?,
  <font color="#48B9C7"><b>| </b></font>                                                             <font color="#C4A000"><b>^^^^^^^</b></font> <font color="#C4A000"><b>help: try this: `return Err(e.into())`</b></font>
  <font color="#48B9C7"><b>|</b></font>
  <font color="#48B9C7"><b>= </b></font><b>note</b>: `#[warn(clippy::try_err)]` on by default
  <font color="#48B9C7"><b>= </b></font><b>help</b>: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#try_err

<font color="#C4A000"><b>warning</b></font><b>: returning an `Err(_)` with the `?` operator</b>
  <font color="#48B9C7"><b>--&gt; </b></font>examples/report.rs:10:62
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>10</b></font> <font color="#48B9C7"><b>| </b></font>        Err(e) if e.kind() == playground::Kind::Important =&gt; Err(e)?,
   <font color="#48B9C7"><b>| </b></font>                                                             <font color="#C4A000"><b>^^^^^^^</b></font> <font color="#C4A000"><b>help: try this: `return Err(e.into())`</b></font>
   <font color="#48B9C7"><b>|</b></font>
   <font color="#48B9C7"><b>= </b></font><b>help</b>: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#try_err

<font color="#C4A000"><b>warning</b></font><b>: 2 warnings emitted</b>

<font color="#4E9A06"><b>    Finished</b></font> dev [unoptimized + debuginfo] target(s) in 0.00s
</pre>

Me: _I really need to disable this warning in clippy, ugh_

```rust
#![allow(clippy::try_err)]
```

Okay, it should work now, lets see our initial report format:

`cargo`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo run --example report
<font color="#4E9A06"><b>   Compiling</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#4E9A06"><b>    Finished</b></font> dev [unoptimized + debuginfo] target(s) in 0.23s
<font color="#4E9A06"><b>     Running</b></font> `target/debug/examples/report`
Error: this error was important to us

Location:
    examples/report.rs:10:68
</pre>

Beautiful, alright so now we've got our initial error type setup. We compile, we show nice error messages, we create it conveniently from a `Kind` and the `?` operator, and we have an outer `struct` type to work with to start adding our new features. Lets move onto backtrace next.

## Backtrace

So here I'll start by just slapping a `Backtrace` in the struct and let rustc remind me what features to enable. In this example I'm going to use `std::backtrace::Backtrace` because it's easier to work with and is compatible with `eyre` and `anyhow` by default. I could make this work with `backtrace::Backtrace` from the backtrace-rs crate but its a bit more involved so I'll leave that to a later post if people are interested. Hopefully it won't be needed however because backtrace stabilization is starting to move forward again ^_^

```rust
use std::backtrace::Backtrace;

#[derive(Debug)]
pub struct Error {
    kind: Kind,
    backtrace: Backtrace,
}
```

Alright `rustc`, gimmi your worse:

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo check --example report
<font color="#4E9A06"><b>    Checking</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0658]</b></font><b>: use of unstable library feature &apos;backtrace&apos;</b>
 <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:4:5
  <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>4</b></font> <font color="#48B9C7"><b>| </b></font>use std::backtrace::Backtrace;
  <font color="#48B9C7"><b>| </b></font>    <font color="#F15D22"><b>^^^^^^^^^^^^^^^^^^^^^^^^^</b></font>
  <font color="#48B9C7"><b>|</b></font>
  <font color="#48B9C7"><b>= </b></font><b>note</b>: see issue #53487 &lt;https://github.com/rust-lang/rust/issues/53487&gt; for more information
  <font color="#48B9C7"><b>= </b></font><b>help</b>: add `#![feature(backtrace)]` to the crate attributes to enable

<font color="#F15D22"><b>error[E0658]</b></font><b>: use of unstable library feature &apos;backtrace&apos;</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:18:16
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>18</b></font> <font color="#48B9C7"><b>| </b></font>    backtrace: Backtrace,
   <font color="#48B9C7"><b>| </b></font>               <font color="#F15D22"><b>^^^^^^^^^</b></font>
   <font color="#48B9C7"><b>|</b></font>
   <font color="#48B9C7"><b>= </b></font><b>note</b>: see issue #53487 &lt;https://github.com/rust-lang/rust/issues/53487&gt; for more information
   <font color="#48B9C7"><b>= </b></font><b>help</b>: add `#![feature(backtrace)]` to the crate attributes to enable

<font color="#F15D22"><b>error[E0063]</b></font><b>: missing field `backtrace` in initializer of `Error`</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:37:9
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>37</b></font> <font color="#48B9C7"><b>| </b></font>        Self { kind }
   <font color="#48B9C7"><b>| </b></font>        <font color="#F15D22"><b>^^^^</b></font> <font color="#F15D22"><b>missing `backtrace`</b></font>

<font color="#F15D22"><b>error</b></font><b>: aborting due to 3 previous errors</b>

<b>Some errors have detailed explanations: E0063, E0658.</b>
<b>For more information about an error, try `rustc --explain E0063`.</b>
<font color="#CC0000"><b>error</b></font><b>:</b> could not compile `playground`
</pre>

Alright so lets enable that feature, I already know I gotta switch to nightly so I'll start using that too:

```rust
#![feature(backtrace)]

impl From<Kind> for Error {
    fn from(kind: Kind) -> Self {
        Self {
            kind,
            // and we gotta actually capture it when we construct an `Error`
            backtrace: Backtrace::capture(),
        }
    }
}
```

At this point rustc was immediately happy, so lets go straight into running it:

<pre><font color="#4E9A06"><b>❯</b></font> RUST_LIB_BACKTRACE=1 cargo +nightly run --example report
<font color="#4E9A06"><b>    Finished</b></font> dev [unoptimized + debuginfo] target(s) in 0.01s
<font color="#4E9A06"><b>     Running</b></font> `target/debug/examples/report`
Error: this error was important to us

Location:
    examples/report.rs:12:68

Stack backtrace:
   0: eyre::DefaultHandler::default_with
             at /home/jlusby/.cargo/registry/src/github.com-1ecc6299db9ec823/eyre-0.6.1/src/lib.rs:689
   1: core::ops::function::Fn::call
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/core/src/ops/function.rs:70
   2: eyre::capture_handler
             at /home/jlusby/.cargo/registry/src/github.com-1ecc6299db9ec823/eyre-0.6.1/src/lib.rs:551
   3: eyre::error::&lt;impl eyre::Report&gt;::from_std
             at /home/jlusby/.cargo/registry/src/github.com-1ecc6299db9ec823/eyre-0.6.1/src/error.rs:87
   4: eyre::error::&lt;impl core::convert::From&lt;E&gt; for eyre::Report&gt;::from
             at /home/jlusby/.cargo/registry/src/github.com-1ecc6299db9ec823/eyre-0.6.1/src/error.rs:461
   5: report::main
             at ./examples/report.rs:12
   6: core::ops::function::FnOnce::call_once
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/core/src/ops/function.rs:227
   7: std::sys_common::backtrace::__rust_begin_short_backtrace
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/std/src/sys_common/backtrace.rs:137
   8: std::rt::lang_start::{{closure}}
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/std/src/rt.rs:66
   9: core::ops::function::impls::&lt;impl core::ops::function::FnOnce&lt;A&gt; for &amp;F&gt;::call_once
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/core/src/ops/function.rs:259
      std::panicking::try::do_call
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/panicking.rs:381
      std::panicking::try
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/panicking.rs:345
      std::panic::catch_unwind
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/panic.rs:382
      std::rt::lang_start_internal
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/rt.rs:51
  10: std::rt::lang_start
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/std/src/rt.rs:65
  11: main
  12: __libc_start_main
  13: _start
</pre>

_looks at backtrace_ hmm, somethings not right here, oh shit, I forgot to return the backtrace type via the `Error` trait, okay lets go back and add that.

```rust
impl std::error::Error for Error {
    fn backtrace(&self) -> Option<&Backtrace> {
        Some(&self.backtrace)
    }
}
```

<pre><font color="#4E9A06"><b>❯</b></font> RUST_LIB_BACKTRACE=1 cargo +nightly run --example report
<font color="#4E9A06"><b>    Finished</b></font> dev [unoptimized + debuginfo] target(s) in 0.00s
<font color="#4E9A06"><b>     Running</b></font> `target/debug/examples/report`
Error: this error was important to us

Location:
    examples/report.rs:12:68

Stack backtrace:
   0: &lt;playground::Error as core::convert::From&lt;playground::Kind&gt;&gt;::from
             at ./src/lib.rs:44
   1: playground::foo_handle
             at ./src/lib.rs:9
   2: report::main
             at ./examples/report.rs:10
   3: core::ops::function::FnOnce::call_once
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/core/src/ops/function.rs:227
   4: std::sys_common::backtrace::__rust_begin_short_backtrace
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/std/src/sys_common/backtrace.rs:137
   5: std::rt::lang_start::{{closure}}
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/std/src/rt.rs:66
   6: core::ops::function::impls::&lt;impl core::ops::function::FnOnce&lt;A&gt; for &amp;F&gt;::call_once
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/core/src/ops/function.rs:259
      std::panicking::try::do_call
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/panicking.rs:381
      std::panicking::try
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/panicking.rs:345
      std::panic::catch_unwind
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/panic.rs:382
      std::rt::lang_start_internal
             at /rustc/3525087ada7018ef227b10846648660b7f07b6d1/library/std/src/rt.rs:51
   7: std::rt::lang_start
             at /home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/std/src/rt.rs:65
   8: main
   9: __libc_start_main
  10: _start
</pre>

There we go, perfect. We can see the error being constructed from the `Kind` in the `playground` library I'm using to write this example, looks like we're done here. Now comes the fun part, adding new error messages to the chain of errors.


## Context Stack Traces aka Wrapping Errors Internally

So here I'm gonna start much like I did before, I'll sketch out the new API and fill it in based on compiler errors. I tweek the example throwing functions to add the extra method call I want to make. In this case however I know that the new method I'm adding will not be added as an inherent method on `Kind` or `Error`, in order to use it through `Result` the same way you can with `eyre` and `anyhow` you need to do it as an extension trait. So I'll go ahead and define that trait as well.

Disclaimer, the trait I'm going to define here is something that I first learned about from `anyhow`'s source and it took me months to get good enough with traits to figure out how to write it as cleanly as this. Even then when I was first writing this example I had to go look up [the source code](https://docs.rs/color-eyre/0.5.6/src/color_eyre/section/mod.rs.html#135-318) for the  `Section` trait in `color-eyre` to remember exactly how it's supposed to be done.

```rust
pub fn foo_handle() -> Result<(), Error> {
    Err(Kind::Important).wrap_err("this error should not be ignored")?
}

pub fn foo_no_handle() -> Result<(), Error> {
    Err(Kind::NotImportant).wrap_err("it's okay to ignore this error")?
}

trait WrapErr {
    type Return;

    fn wrap_err<D>(self, msg: D) -> Self::Return
    where
        D: fmt::Display + Send + Sync + 'static;
}
```

<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly run --example report
<font color="#4E9A06"><b>   Compiling</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0599]</b></font><b>: no method named `wrap_err` found for enum `std::result::Result&lt;_, Kind&gt;` in the current scope</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:9:26
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>9</b></font>  <font color="#48B9C7"><b>| </b></font>    Err(Kind::Important).wrap_err(&quot;this error should not be ignored&quot;)?
   <font color="#48B9C7"><b>| </b></font>                         <font color="#F15D22"><b>^^^^^^^^</b></font> <font color="#F15D22"><b>help: there is an associated function with a similar name: `map_err`</b></font>
   <font color="#48B9C7"><b>|</b></font>
   <font color="#48B9C7"><b>= </b></font><b>help</b>: items from traits can only be used if the trait is implemented and in scope
<font color="#73C48F"><b>note</b></font>: `WrapErr` defines an item `wrap_err`, perhaps you need to implement it
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:67:1
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>67</b></font> <font color="#48B9C7"><b>| </b></font>trait WrapErr {
   <font color="#48B9C7"><b>| </b></font><font color="#73C48F"><b>^^^^^^^^^^^^^</b></font>
</pre>

So good so far. The cool thing that this trait in `eyre` and friends does is convert the inner error type too an `eyre::Report` immediately before adding the new error message to the report. However, going straight for this feature and implementing `WrapErr` for the generic set `<E: Error>` will get you into trouble, because `rustc` won't be able to be sure that `Result<...>` can't also be turned into your `Error` type.

The key here is to split it into two impls, the first impl, directly on your `Error` type, just handles adding the context, then your second impl and beyond all handle just the conversions and `map_err`ness or w/e. Here's what it ends up looking like:

```rust
impl WrapErr for Error {
    type Return = Error;

    fn wrap_err<D>(self, msg: D) -> Self::Return
    where
        D: fmt::Display + Send + Sync + 'static,
    {
        todo!()
    }
}

impl<T, E> WrapErr for Result<T, E>
where
    E: Into<Error>,
{
    type Return = Result<T, Error>;

    fn wrap_err<D>(self, msg: D) -> Self::Return
    where
        D: fmt::Display + Send + Sync + 'static,
    {
        todo!()
    }
}
```

For now I'm going to leave this unimplemented, because we haven't yet created the spot to store our error messages, rustc seems pretty happy with us tho, the only complaint is about the unused `msg` argument.

So the next step is we need a way to store an ErrorMessage, and a previous error message, such that we can create a linked list of sources internally without losing access to our `Kind` or `Backtrace`.

So we start with a definition for the error message struct that looks like a singly linked list:

```rust
struct ErrorMessage {
    message: Box<dyn fmt::Display + Send + Sync + 'static>,
    source: Option<Box<ErrorMessage>>,
}
```

Add this to our `Error` type:

```rust
#[derive(Debug)]
pub struct Error {
    kind: Kind,
    backtrace: Backtrace,
    message: ErrorMessage,
}
```

And adjust our `Display` impl on `Error` to use the new message member instead of kind:

```rust
impl fmt::Display for Error {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        self.message.fmt(f)
    }
}
```

I'm already sure this will anger `rustc` so lets see where they tell us to start next:

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly check --example report
<font color="#4E9A06"><b>    Checking</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0277]</b></font><b>: `ErrorMessage` doesn&apos;t implement `Debug`</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:20:5
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>20</b></font> <font color="#48B9C7"><b>| </b></font>    message: ErrorMessage,
   <font color="#48B9C7"><b>| </b></font>    <font color="#F15D22"><b>^^^^^^^^^^^^^^^^^^^^^</b></font> <font color="#F15D22"><b>`ErrorMessage` cannot be formatted using `{:?}`</b></font>
   <font color="#48B9C7"><b>|</b></font>
   <font color="#48B9C7"><b>= </b></font><b>help</b>: the trait `Debug` is not implemented for `ErrorMessage`
   <font color="#48B9C7"><b>= </b></font><b>note</b>: add `#[derive(Debug)]` or manually implement `Debug`
   <font color="#48B9C7"><b>= </b></font><b>note</b>: required because of the requirements on the impl of `Debug` for `&amp;ErrorMessage`
   <font color="#48B9C7"><b>= </b></font><b>note</b>: required for the cast to the object type `dyn Debug`
   <font color="#48B9C7"><b>= </b></font><b>note</b>: this error originates in a derive macro (in Nightly builds, run with -Z macro-backtrace for more info)

<font color="#F15D22"><b>error[E0599]</b></font><b>: no method named `fmt` found for struct `ErrorMessage` in the current scope</b>
   <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:25:22
    <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>25</b></font>  <font color="#48B9C7"><b>| </b></font>        self.message.fmt(f)
    <font color="#48B9C7"><b>| </b></font>                     <font color="#F15D22"><b>^^^</b></font> <font color="#F15D22"><b>method not found in `ErrorMessage`</b></font>
<font color="#48B9C7"><b>...</b></font>
<font color="#48B9C7"><b>101</b></font> <font color="#48B9C7"><b>| </b></font>struct ErrorMessage {
    <font color="#48B9C7"><b>| -------------------</b></font> <font color="#48B9C7"><b>method `fmt` not found for this</b></font>
    <font color="#48B9C7"><b>| </b></font>
   <font color="#48B9C7"><b>::: </b></font>/home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/core/src/fmt/mod.rs:930:8
    <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>930</b></font> <font color="#48B9C7"><b>| </b></font>    fn fmt(&amp;self, f: &amp;mut Formatter&lt;&apos;_&gt;) -&gt; Result;
    <font color="#48B9C7"><b>| </b></font>       <font color="#48B9C7"><b>---</b></font>
    <font color="#48B9C7"><b>| </b></font>       <font color="#48B9C7"><b>|</b></font>
    <font color="#48B9C7"><b>| </b></font>       <font color="#48B9C7"><b>the method is available for `Box&lt;ErrorMessage&gt;` here</b></font>
    <font color="#48B9C7"><b>| </b></font>       <font color="#48B9C7"><b>the method is available for `Arc&lt;ErrorMessage&gt;` here</b></font>
    <font color="#48B9C7"><b>| </b></font>       <font color="#48B9C7"><b>the method is available for `Rc&lt;ErrorMessage&gt;` here</b></font>
    <font color="#48B9C7"><b>|</b></font>
    <font color="#48B9C7"><b>= </b></font><b>help</b>: items from traits can only be used if the trait is implemented and in scope
    <font color="#48B9C7"><b>= </b></font><b>note</b>: the following traits define an item `fmt`, perhaps you need to implement one of them:
            candidate #1: `Debug`
            candidate #2: `std::fmt::Display`
            candidate #3: `Octal`
            candidate #4: `Binary`
            candidate #5: `LowerHex`
            candidate #6: `UpperHex`
            candidate #7: `Pointer`
            candidate #8: `LowerExp`
            candidate #9: `UpperExp`

<font color="#F15D22"><b>error[E0063]</b></font><b>: missing field `message` in initializer of `Error`</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:43:9
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>43</b></font> <font color="#48B9C7"><b>| </b></font>        Self {
   <font color="#48B9C7"><b>| </b></font>        <font color="#F15D22"><b>^^^^</b></font> <font color="#F15D22"><b>missing `message`</b></font>

<font color="#F15D22"><b>error</b></font><b>: aborting due to 3 previous errors</b>

<b>Some errors have detailed explanations: E0063, E0277, E0599.</b>
<b>For more information about an error, try `rustc --explain E0063`.</b>
<font color="#CC0000"><b>error</b></font><b>:</b> could not compile `playground`
</pre>

Whew, big error message, lets go add those impls rq:

```rust
impl From<Kind> for Error {
    fn from(kind: Kind) -> Self {
        Self {
            kind,
            backtrace: Backtrace::capture(),
            message: ErrorMessage::from(kind),
        }
    }
}

#[derive(Debug)]
struct ErrorMessage {
    message: Box<dyn fmt::Display + Send + Sync + 'static>,
    source: Option<Box<ErrorMessage>>,
}

impl fmt::Display for ErrorMessage {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        self.message.fmt(f)
    }
}
```

Here you'll notice im using the `kind` itself to create the initial error message, since it's copy. This seemed like a reasonable default to me, but feel free to take a different approach if you prefer.

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly check --example report
<font color="#4E9A06"><b>    Checking</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0308]</b></font><b>: mismatched types</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:46:41
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>46</b></font> <font color="#48B9C7"><b>| </b></font>            message: ErrorMessage::from(kind),
   <font color="#48B9C7"><b>| </b></font>                                        <font color="#F15D22"><b>^^^^</b></font> <font color="#F15D22"><b>expected struct `ErrorMessage`, found enum `Kind`</b></font>

<font color="#F15D22"><b>error[E0277]</b></font><b>: `(dyn std::fmt::Display + Send + Sync + &apos;static)` doesn&apos;t implement `Debug`</b>
   <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:104:5
    <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>104</b></font> <font color="#48B9C7"><b>| </b></font>    message: Box&lt;dyn fmt::Display + Send + Sync + &apos;static&gt;,
    <font color="#48B9C7"><b>| </b></font>    <font color="#F15D22"><b>^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^</b></font> <font color="#F15D22"><b>`(dyn std::fmt::Display + Send + Sync + &apos;static)` cannot be formatted using `{:?}` because it doesn&apos;t implement `Debug`</b></font>
    <font color="#48B9C7"><b>|</b></font>
    <font color="#48B9C7"><b>= </b></font><b>help</b>: the trait `Debug` is not implemented for `(dyn std::fmt::Display + Send + Sync + &apos;static)`
    <font color="#48B9C7"><b>= </b></font><b>note</b>: required because of the requirements on the impl of `Debug` for `Box&lt;(dyn std::fmt::Display + Send + Sync + &apos;static)&gt;`
    <font color="#48B9C7"><b>= </b></font><b>note</b>: required because of the requirements on the impl of `Debug` for `&amp;Box&lt;(dyn std::fmt::Display + Send + Sync + &apos;static)&gt;`
    <font color="#48B9C7"><b>= </b></font><b>note</b>: required for the cast to the object type `dyn Debug`
    <font color="#48B9C7"><b>= </b></font><b>note</b>: this error originates in a derive macro (in Nightly builds, run with -Z macro-backtrace for more info)

<font color="#F15D22"><b>error</b></font><b>: aborting due to 2 previous errors</b>
</pre>

On god damnit, you can't derive `Debug` with a `dyn Display` type, thats annoying, okay w/e:

```rust
struct ErrorMessage {
    message: Box<dyn fmt::Display + Send + Sync + 'static>,
    source: Option<Box<ErrorMessage>>,
}

impl<D> From<D> for ErrorMessage
where
    D: fmt::Display + Send + Sync + 'static,
{
    fn from(message: D) -> Self {
        ErrorMessage {
            message: Box::new(message),
            source: None,
        }
    }
}

impl fmt::Debug for ErrorMessage {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        self.message.fmt(f)
    }
}
```

Here I decided to go with a from impl for all `Display` types so that we can easily make error messages from whatever, I have secret plans for this later:

`rustc`:


<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly check --example report
<font color="#4E9A06"><b>    Checking</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0119]</b></font><b>: conflicting implementations of trait `std::convert::From&lt;ErrorMessage&gt;` for type `ErrorMessage`:</b>
   <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:107:1
    <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>107</b></font> <font color="#48B9C7"><b>| </b></font><font color="#F15D22"><b>/</b></font> impl&lt;D&gt; From&lt;D&gt; for ErrorMessage
<font color="#48B9C7"><b>108</b></font> <font color="#48B9C7"><b>| </b></font><font color="#F15D22"><b>|</b></font> where
<font color="#48B9C7"><b>109</b></font> <font color="#48B9C7"><b>| </b></font><font color="#F15D22"><b>|</b></font>     D: fmt::Display + Send + Sync + &apos;static,
<font color="#48B9C7"><b>110</b></font> <font color="#48B9C7"><b>| </b></font><font color="#F15D22"><b>|</b></font> {
<font color="#48B9C7"><b>...</b></font>   <font color="#F15D22"><b>|</b></font>
<font color="#48B9C7"><b>116</b></font> <font color="#48B9C7"><b>| </b></font><font color="#F15D22"><b>|</b></font>     }
<font color="#48B9C7"><b>117</b></font> <font color="#48B9C7"><b>| </b></font><font color="#F15D22"><b>|</b></font> }
    <font color="#48B9C7"><b>| </b></font><font color="#F15D22"><b>|_^</b></font>
    <font color="#48B9C7"><b>|</b></font>
    <font color="#48B9C7"><b>= </b></font><b>note</b>: conflicting implementation in crate `core`:
            - impl&lt;T&gt; From&lt;T&gt; for T;

<font color="#F15D22"><b>error</b></font><b>: aborting due to previous error</b>
</pre>

Oh god damnit, fking overlap rule, this says that it the fact that `ErrorMessage` impls `Display` means we can't impl `From<Display>` because then `ErrorMessage::from(error_message)` wouldn't know if it should do an identity conversion (just immediately return the same value) or wrap it inside the message field of a new `ErrorMessage`.

..., Fuck it! We don't need to impl `Display` here, its an internal type, and we already have an identical `Debug` impl, buahahahaha.

```rust
impl fmt::Display for Error {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        use fmt::Debug;
        self.message.fmt(f)
    }
}

// impl fmt::Display for ErrorMessage {
//     fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
//         self.message.fmt(f)
//     }
// }
```

_palpatine.jpg: yess, let the trait flow through you_

`cargo`:


<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly run --example report
<font color="#4E9A06"><b>   Compiling</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#C4A000"><b>warning</b></font><b>: unused variable: `msg`</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:81:26
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>81</b></font> <font color="#48B9C7"><b>| </b></font>    fn wrap_err&lt;D&gt;(self, msg: D) -&gt; Self::Return
   <font color="#48B9C7"><b>| </b></font>                         <font color="#C4A000"><b>^^^</b></font> <font color="#C4A000"><b>help: if this is intentional, prefix it with an underscore: `_msg`</b></font>
   <font color="#48B9C7"><b>|</b></font>
   <font color="#48B9C7"><b>= </b></font><b>note</b>: `#[warn(unused_variables)]` on by default

<font color="#C4A000"><b>warning</b></font><b>: unused variable: `msg`</b>
  <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:95:26
   <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>95</b></font> <font color="#48B9C7"><b>| </b></font>    fn wrap_err&lt;D&gt;(self, msg: D) -&gt; Self::Return
   <font color="#48B9C7"><b>| </b></font>                         <font color="#C4A000"><b>^^^</b></font> <font color="#C4A000"><b>help: if this is intentional, prefix it with an underscore: `_msg`</b></font>

<font color="#C4A000"><b>warning</b></font><b>: 2 warnings emitted</b>

<font color="#4E9A06"><b>    Finished</b></font> dev [unoptimized + debuginfo] target(s) in 0.37s
<font color="#4E9A06"><b>     Running</b></font> `target/debug/examples/report`
thread &apos;main&apos; panicked at &apos;not yet implemented&apos;, src/lib.rs:99:9
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
</pre>

Sick, we're in the final stretch, just gotta fill in the `WrapErr` impls and I think we're good to go, this one will be a little tricky, but it should be fine with our handy dandy friend `std::mem::swap`.

```rust
impl WrapErr for Error {
    type Return = Error;

    fn wrap_err<D>(mut self, msg: D) -> Self::Return
    where
        D: fmt::Display + Send + Sync + 'static,
    {
        let mut message = ErrorMessage {
            message: Box::new(msg),
            source: None,
        };

        std::mem::swap(&mut self.message, &mut message);

        self.message.source = Some(Box::new(message));

        self
    }
}

impl<T, E> WrapErr for Result<T, E>
where
    E: Into<Error>,
{
    type Return = Result<T, Error>;

    fn wrap_err<D>(self, msg: D) -> Self::Return
    where
        D: fmt::Display + Send + Sync + 'static,
    {
        self.map_err(|e| {
            let e = e.into();
            e.wrap_err(msg)
        })
    }
}
```

... Is that it? I think we're done :O

Lets see what we got

`cargo`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly run --example report
<font color="#4E9A06"><b>   Compiling</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#4E9A06"><b>    Finished</b></font> dev [unoptimized + debuginfo] target(s) in 0.30s
<font color="#4E9A06"><b>     Running</b></font> `target/debug/examples/report`
Error: this error should not be ignored

Location:
    examples/report.rs:12:68
</pre>

Hmm, nope, we're missing something... Oh yea, we forgot to return our source in the `Error` trait, I swear I always do this...

```rust
impl std::error::Error for Error {
    fn source(&self) -> Option<&(dyn std::error::Error + 'static)> {
        self.message.source()
    }

    fn backtrace(&self) -> Option<&Backtrace> {
        Some(&self.backtrace)
    }
}
```

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly run --example report
<font color="#4E9A06"><b>   Compiling</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0599]</b></font><b>: no method named `source` found for struct `ErrorMessage` in the current scope</b>
   <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:32:22
    <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>32</b></font>  <font color="#48B9C7"><b>| </b></font>        self.message.source()
    <font color="#48B9C7"><b>| </b></font>                     <font color="#F15D22"><b>^^^^^^</b></font><font color="#48B9C7"><b>--</b></font> <font color="#48B9C7"><b>help: remove the arguments</b></font>
    <font color="#48B9C7"><b>| </b></font>                     <font color="#F15D22"><b>|</b></font>
    <font color="#48B9C7"><b>| </b></font>                     <font color="#F15D22"><b>field, not a method</b></font>
<font color="#48B9C7"><b>...</b></font>
<font color="#48B9C7"><b>119</b></font> <font color="#48B9C7"><b>| </b></font>struct ErrorMessage {
    <font color="#48B9C7"><b>| -------------------</b></font> <font color="#48B9C7"><b>method `source` not found for this</b></font>
    <font color="#48B9C7"><b>|</b></font>
    <font color="#48B9C7"><b>= </b></font><b>help</b>: items from traits can only be used if the trait is implemented and in scope
    <font color="#48B9C7"><b>= </b></font><b>note</b>: the following trait defines an item `source`, perhaps you need to implement it:
            candidate #1: `std::error::Error`

<font color="#F15D22"><b>error</b></font><b>: aborting due to previous error</b>
</pre>

Oh yea, gotta impl `Error` on `ErrorMessage` if I want to be able to make the previous error messages available in the chain of `source` errors, awe shit I know where this is going...

```rustc
impl std::error::Error for ErrorMessage {
    fn source(&self) -> Option<&(dyn std::error::Error + 'static)> {
        self.source.as_ref().map(|e| e as _)
    }
}
```

Sigh, okay rustc, let me have it:

`rustc`:

<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly run --example report
<font color="#4E9A06"><b>   Compiling</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#F15D22"><b>error[E0277]</b></font><b>: `ErrorMessage` doesn&apos;t implement `std::fmt::Display`</b>
   <font color="#48B9C7"><b>--&gt; </b></font>src/lib.rs:136:6
    <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>136</b></font> <font color="#48B9C7"><b>| </b></font>impl std::error::Error for ErrorMessage {
    <font color="#48B9C7"><b>| </b></font>     <font color="#F15D22"><b>^^^^^^^^^^^^^^^^^</b></font> <font color="#F15D22"><b>`ErrorMessage` cannot be formatted with the default formatter</b></font>
    <font color="#48B9C7"><b>| </b></font>
   <font color="#48B9C7"><b>::: </b></font>/home/jlusby/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/std/src/error.rs:48:26
    <font color="#48B9C7"><b>|</b></font>
<font color="#48B9C7"><b>48</b></font>  <font color="#48B9C7"><b>| </b></font>pub trait Error: Debug + Display {
    <font color="#48B9C7"><b>| </b></font>                         <font color="#48B9C7"><b>-------</b></font> <font color="#48B9C7"><b>required by this bound in `std::error::Error`</b></font>
    <font color="#48B9C7"><b>|</b></font>
    <font color="#48B9C7"><b>= </b></font><b>help</b>: the trait `std::fmt::Display` is not implemented for `ErrorMessage`
    <font color="#48B9C7"><b>= </b></font><b>note</b>: in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
</pre>

Sad face, bitten in the ass by my own cleverness. I guess I'll just manually impl `From` for Kind only :(((

```rust
impl From<Kind> for ErrorMessage {
    fn from(kind: Kind) -> Self {
        ErrorMessage {
            message: Box::new(kind),
            source: None,
        }
    }
}

impl std::error::Error for ErrorMessage {
    fn source(&self) -> Option<&(dyn std::error::Error + 'static)> {
        self.source.as_ref().map(|e| e as _)
    }
}

impl fmt::Debug for ErrorMessage {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        self.message.fmt(f)
    }
}

impl fmt::Display for ErrorMessage {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        self.message.fmt(f)
    }
}
```

Okay, this time I think we got it...

<pre><font color="#4E9A06"><b>❯</b></font> cargo +nightly run --example report
<font color="#4E9A06"><b>   Compiling</b></font> playground v0.1.0 (/home/jlusby/playground)
<font color="#4E9A06"><b>    Finished</b></font> dev [unoptimized + debuginfo] target(s) in 0.35s
<font color="#4E9A06"><b>     Running</b></font> `target/debug/examples/report`
Error: this error should not be ignored

Caused by:
    this error was important to us

Location:
    examples/report.rs:12:68
</pre>

Fuck yea! Nailed it, I'm the best ^_^

## Conclusion

Okay so that's how I write error types, I ended up writing a recreation of the dev process by literally just doing it again from scratch. I hope you all enjoy reading it as much as I enjoyed half assing writing it.

## Extras

There are a few features here that could be added easily that I didn't go ahead and do in this case. For example, `eyre` and `anyhow` both occupy only one pointer on the stack, where as this `Error` kind is rather chonky in comparison, with the multiple Boxes in `ErrorMessage` and the `Backtrace`. It's not much harder to make the `Error` type defined here into a private `ErrorImpl` and then make a new `struct Error(Box<ErrorImpl>);` type to wrap it. This will help with happy path code by keeping stack frames smaller, but adds an extra allocation when errors are actually thrown, its up to you to decide which you need more.

Another thing, in this example I only ever showed creating `Errors` from `Kind`, but you could add other `From` impls for `Error`. Say you have a lot of random errors that aren't important to handle, you could add something like this:

```rust
impl From<&'static str> for Error {
    fn from(msg: &'static str) -> Error {
        Error {
            kind: Kind::Other,
            backtrace: Backtrace::capture(),
            message: ErrorMessage::from(msg),
        }
    }
}
```

Get creative with it, you'll probably run into some of the same `fmt::Display` overlap errors here so you'll have to implement the from impls for each individual type you want to use as a source, which you can deduplicate with macros if you end up doing with many different types.

Thats all I've got for now. If you've got a similar problem where you don't know how to write just the right error handling type for your library or application please feel free to nerd snipe me. I'll love to do this again and get a Part 2 up, and so on. Take care everyone!