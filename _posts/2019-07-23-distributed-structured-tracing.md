---
layout: post
title: "Scoped, Structured, and Distributed Logging"
date: 2019-07-23 10:23
categories: c++ cpp rust rustlang distributed tracing structured logging
---

# Scoped, Structured, and Distributed Logging - Jane Lusby

Okay, so what is this talk? This talk is intended to introduce my coworkers to
logging concepts I learned about at a rust meetup. Eliza Wiesman gave a talk on
her library `tokio-rs/tracing` https://www.youtube.com/watch?v=j_kXRg3zlec
which I found compelling, in no small part because I felt it would be useful to
have similar features in our clusters.

`tracing` itself is a library that is based on Distributed Tracing as defined by the microservices world. 

The main concepts I would like to introduce are the Event+Span model of
logging, structured logging, and as little rust syntax as is necessary to
demonstrait those two ideas. The Event+Span model of logging encodes
relationships between log messsages and units of work done by the system and
makes it easier to find logs which are tied logically to some event on another
node, and to isolate logs from those on different threads to handle the
confusion and chattiness that is common when debugging heavily asyncronous
code. Structured logging is a paradigm for how data is formatted so that it can
be output in various formats (plain text vs json vs binary) and parsed more
effectively.

All of the examples will be in rust, mostly because none of the c++
implementations of distributed tracing or structured logging are anywhere near
as clean to look at as `tracing` is. This is 100% thanks to rust's powerful
macro system.

## Definitions

Some quick definitions so the examples make sense

- Event - Essentially a log message
- Span  - An abstract representation of a unit of work, usually a few ids, a
  name, and whatever associated data you want.
- Trace - A Span and all of its children

The secret sauce is in that children bit. Every span and event has a parent
span associated with it. These relationships define a treelike structure that
represents the various code paths and events you go through when completing a
unit of work.


## A Basic Example, logging in rust.

The following example is taken from the `log` crate, not `tracing`. It is the
current existing standard for logging in the rust ecosystem, you can view
`tracing` as an extension upon `log`'s standard. The includes are from
`tracing` rather than `log` but they're a drop in replacement, you can replace
`tracing` with `log` on line 2 and everything will work the same more or less.

```rust
// Import `tracing`'s macros rather than `log`'s
use tracing::{span, info, warn, Level};

// unchanged from here forward
pub fn shave_the_yak(yak: &mut Yak) {
    info!(target: "yak_events", "Commencing yak shaving for {:?}", yak);

    loop {
        match find_a_razor() {
            Ok(razor) => {
                info!("Razor located: {}", razor);
                yak.shave(razor);
                break;
            }
            Err(err) => {
                warn!("Unable to locate a razor: {}, retrying", err);
            }
        }
    }
}

// Dummy impls to make the example compile
#[derive(Debug)] pub struct Yak(String);
impl Yak { fn shave(&mut self, _: u32) {} }
fn find_a_razor() -> Result<u32, u32> { Ok(1) }
```

So in this example we have a function, on entry we log a message saying we are
gonna shave a yak, and what yak we're going to shave, then we loop and find a
razor, and if we get one we shave the yak and log that, and if we dont we warn
that we cant find a razor.

## The Same Basic Example: Reloaded

So now we will rewrite this example to use features from tracing. This will
introduce spans and structured log messages, and will lead into the rest of the
talk.

```rust
use tracing::{span, info, warn, Level};

pub fn shave_the_yak(yak: &mut Yak) {
    // create and enter a span to represent the scope
    let span = span!(Level::TRACE, "shave_the_yak", ?yak);
    let _enter = span.enter();

    // Since the span is annotated with the yak, it is part of the context
    // for everything happening inside the span. Therefore, we don't need
    // to add it to the message for this event, as the `log` crate does.
    info!(target: "yak_events", "Commencing yak shaving");
    loop {
        match find_a_razor() {
            Ok(razor) => {
                // We can add the razor as a field rather than formatting it
                // as part of the message, allowing subscribers to consume it
                // in a more structured manner:
                info!({ %razor }, "Razor located");
                yak.shave(razor);
                break;
            }
            Err(err) => {
                // However, we can also create events with formatted messages,
                // just as we would for log records.
                warn!("Unable to locate a razor: {}, retrying", err);
            }
        }
    }
}

#[derive(Debug)] pub struct Yak(String);
impl Yak { fn shave(&mut self, _: u32) {} }
fn find_a_razor() -> Result<u32, u32> { Ok(1) }
```

Ok so what changed, now instead of logging a message on entering the function
we create a span and do something with our yak argument and then enter said
span. Then we log the same message as before to say that we're starting our yak
shave. Then in the loop when we have a razor instead of formatting the razor
into the log string we do something weird with it. The warn log line is
unchanged.

Okay so you probably guessed already but those weird things are structured
logging. The `%` and `?` infront of them are a special syntax implemented by
the macros in `tracing` that tell the logger whether or not to use `Debug` or
`Display` when formatting the data. These are just two traits for how to
essentially ostream objects in rust. Display is the default user defined
version, and is intended to be user facing. `Debug` is generally automatically
generated by the compiler for you and is essentially a jsonification of the
struct for use by developers when debugging. In this case `%` means format this
with `Display` and `?` means format this with `Debug`.


## What does the output look like?

This is a tricky question because it's not actually defined by `tracing` but by
the subscriber implementation, aka the thing that tracing passes all the events
to and delegates all decision logic to. The tracing group provides a couple of
implementations of subscribers, we will focus on `tracing-fmt` because its the
most basic "normal" logger that converts your events straight into log lines on
stdout, just like you'd expect.

The basic example above might print something like this.

```
Jul 23 14:01:51.803 INFO  shave_the_yak{yak=Yak{name: "hairy"}}: Commencing yak shaving
Jul 23 14:01:51.804 INFO  shave_the_yak{yak=Yak{name: "hairy"}}: Razor located razor=1
```

As you can see each log line is associated with the parent span, this logger
outputs all parent spans and the associated data of each parent span and then
the event message followed by any associated data of the log event. The actual
output can be whatever you like, the point is that when you go to log you have
all of this data available to you in a highly structured manner.

The structured logging lets the logger format and stringify the name of your
fields and append them or prepend them nicely and consistently. No more having
to write `"Shaving yak (" << yak << ")"` or similar custom and inconsistent
formatting for each and every log message.


## What am I proposing? Why is this useful for Scale Computing?

What im not proposing is replacing or rewriting our entire log system. I want
to see what incremental changes we can make to our existing log framework to
move us towards a Span+Event model of logging.

With our tasks especially, its very common to have a few fields that we put in
every log message like the rsdID or journalID or w/e, that really should be
associated with the span that represents the task.

Many of our tasks spin up tasks on remote nodes and it can be hard to trace a
parent child relationship between them. We do have parent ids for tasks, which
is amazing and is already I think part of the way there, but its not really
formalized or easy to work with, and its very specific to our Task/PDU system.

With general logging im constantly having to write messages like `"variable
name = " << variable"` just to get some basic debug information available. I
want structured logging so I can just write something like `DEBUG("whatever
event just happened", var)` and have it magically and consistently output the
variable name and its value, and possibly output the data as json so I can
query for a specific set of lines and then grab the associated json data and
parse / process it.

The goal here is to make logs easier to write, easier to process, and easier to
search. I doubt we'll ever get all the way there to every feature I can
imagine, but I think there are certainly some low hanging fruit that we could
pick, and I want to make sure everyone is familiar with these concepts so when
opportunities arrise we're ready for them.

