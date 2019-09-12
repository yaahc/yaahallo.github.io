---
layout: post
title: "Error Handling Patterns In C++ And Other Languages"
date: 2019-04-04 13:39:00 -0700
categories: c++ cpp rust rustlang swift exceptions
---

# Error handling patterns

In C++ there has recently been a fair bit of noise[][1][][2][][3] about revamping how the language handles errors and exceptions. The primary issue at hand is that many codebases ban exceptions for various reasons, either performance or readability, and use error codes and other hand rolled error reporting mechanisms instead, which has caused major fractures in the C++ ecosystem.

Herb Sutter has proposed a set of changes that are intended to address this fragmentation.

1. Enable zero-overhead exception handling
2. Throw fewer exceptions (95% of exceptions should not be)
3. Support explicit "try" for visible propogation of exceptions.

At the core of this effort is the introduction of "Lightweight Exceptions" which use the return channel and type safe tagged unions instead of exceptions to pass errors up the stack.

These lightweight exceptions are not new, infact this is essentially the way that rust and swift handle errors, and overall Herb's plan is to move C++ in much the same direction rust has gone. In rust there are two forms of errors, "Recoverable Errors" which are represented with the `Result` type, and "Unrecoverable Errors" which are handled via `panics`. The proposed C++ system will use something akin to `Result` for recoverable errors, for which people currently use a mix of exceptions and error codes, and it would use `abort` and `exceptions`(???) for non-recoverable errors. The similarities between the proposal for C++ and the current rust error handling ecosystem make it worth learning how Rust handles errors to prepare for the future of C++ error handling.

## Error Handling in rust

Recoverable error handling in rust leans heavily on the type system, particularly its support for type safe tagged unions via its `enum` type. I've already explained rust enums and how they work in a previous talk but I'll give a quick refresher of how the result type works.

Here is the specific type, called `Result`, that is used to represent a fallible return type.

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

That is to say, we return an enum (union) that is either the Ok variant, which wraps a generic type T, or the Err variant, which wraps a different generic type E. The size of this union is large enough to contain the larger of T or E, and if necessary a discriminant (bool) to record which variant the Result was instantiated as.

Any function that can essentially "throw an exception" will have Result in its return type, for example, from the standard library.

```rust
/// Copies the contents of one file to another. This function will also
/// copy the permission bits of the original file to the destination file.
///
/// # Errors
///
/// This function will return an error in the following situations, but is not
/// limited to just these cases:
///
/// * The `from` path is not a file.
/// * The `from` file does not exist.
/// * The current process does not have the permission rights to access
///   `from` or write `to`.
///
/// # Examples
///
/// ```no_run
/// use std::fs;
///
/// fn main() -> Result<(), std::io::Error> {
///     fs::copy("foo.txt", "bar.txt")?;  // Copy foo.txt to bar.txt
///     Ok(())
/// }
/// ```
pub fn copy(from: impl AsRef<Path>, to: impl AsRef<Path>) -> Result<u64, std::io::Error>
```

This function will "throw" if it is unable to copy the file between `from` and `to`. If you look at the example you will notice one extra piece of syntax. At the end of the call to `copy` there is a random `?` sigil. This is called the propogation operator in rust. It is equivalent to `try` in swift and in the proposed c++ lightweight exceptions syntax. It is essentially syntax sugar for the following extremely common pattern for forwarding errors up the call stack.

```rust
let ret = fs::copy("foo.txt", "bar.txt");
let ret = match ret {
    Ok(inner_value) => inner_value,
    Err(err) => return Err(err.into()),
}
```

To summarize, it checks the variant of the result returned, if it was a `Result::Ok` the entire expression evaluates to the inner value. If it was a `Result::Err` it returns the Err to the calling function, and if necessary it calls the conversion function between the callee's error type and the caller's error type.


## No more hidden control flow.

One advantage of using Result types to pack errors into the return type is that its no longer possible for functions you call to throw exceptions you did not expect. All exception throwing functions must encode that they return exceptions in their return type.

Additionally, with the introduction of `?` or the `try` keyword, these throwing functions gain visibility at their call sites. You no longer have to worry about which of your functions are going to throw an error, you can see exactly which ones throw based on which end in a `?` or which ones have obvious error handling code attached to them. This is a boon when refactoring as mismatches in the exception logic no longer cause strange runtime behavior, instead its all promoted to type system errors which are caught at compile time.

## Still automatic

The only difference between passing an error up the stack in c++ with exceptions and with rust via Results is a `?` character.

## Can use stack or heap and avoids RTTI / stack unwinding
## Faster than hand rolled solutions
## Cannot access types through the wrong variant

stronger type safety than error codes, no dynamic casts during catching exceptions or worrying about catching by value or reference.

## Is not yet zero cost compared to error codes and placement news
mention swift

## Error type fragmentation
## Introduces a minor runtime cost on the happy path

## The actual proposed c++ solution has much more syntax sugar, similar to swift's implementation

The proposed model for c++ is actually to allow annotating functions with throws and leave the return type unchanged. The error type is inferred by the compiler, and the catch / throw syntax still exists. The result will be that it feels like you're using exceptions except everything is done by value and under the hood it is implemented as a tagged union where your exceptions and results share the return channel.


[1]: https://botondballo.wordpress.com/2019/07/26/trip-report-c-standards-meeting-in-cologne-july-2019/
[2]: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p0709r3.pdf
[3]: https://www.youtube.com/watch?v=os7cqJ5qlzo

