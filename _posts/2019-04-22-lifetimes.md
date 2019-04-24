---
layout: post
title: "Lifetime definitions"
categories: rust rustlang lifetime borrow reference generics
---

# DISCLAIMER

This is a WIP. It does not represent a full or even necessarily correct
explanation of what these terms are or mean or how they relate. This is my
attempt at articulating how I've taken to understanding them. This is going to
evolve over time as I understand lifetimes and borrows better and I hope to
rewrite this page as my understanding improves.

# Confusing terms

My problem with understanding lifetime heavy code is that I find all the terms
involved confusable. This post is my attempt to record clear distinctions
between the terms. Theoretically, these definitions will help ground me when
I'm reasoning about lifetimes.

# Lifetime

A lifetime is a region or scope for which a concrete value is valid. All
variables have a lifetime implicitly, or in the case of `static`, explicitly.
These lifetimes are concrete. They are not the annotations you see in code like
`fn foo<'a>(input: &'a i32) -> &'a i32`, those are lifetime variables.

## Lifetime variable

A type-level variable that refers to a specific scope or region of code (lifetime).

# Reference

A reference is a restricted pointer to a concrete value. You can dereference
them to access the value they point to. The compiler assumes they are never
null.

# Borrow

A borrow is a reference bound by a concrete lifetime. A reference A is a borrow
of a value V if the lifetime variable constraining reference A refers to the
literal lifetime of the value V.

```rust
let x: i32 = 0;
let y: &'a i32 = &x;
```

In the above example, the lifetime variable 'a refers to the literal lifetime
of the variable x. y contains a borrow of x. The above code isn't actually
valid though because you cannot declare lifetimes in `let statements`.

The distinctions between borrows and references are not common as far as I can
tell. This is possibly because references cannot exist without being borrows. I
think of references as the generic version of a borrow, or possibly as a
superset of borrows.

## Rules and Features of borrows

- borrows cannot outlive the lifetime they're templated on, aka, the concrete
  lifetime the borrow was bound to. In the above example, the borrow y cannot
  outlive the value x.
  - The above defines the relationship between a borrows reference and its
    lifetime variable. A borrow's reference is valid for the scope or range
    (lifetime) refered to by the borrows lifetime variable.
- Borrows are implicitly cast to other borrows so long as they fulfill the
  above relationship for the target borrow type.

```rust
static X: i32 = 0;
fn foo<'a>(input: &'a i32) -> &'a i32 {
    &X
}
```

The above code is valid because the lifetime of the borrow of X `&'static i32`
is valid for the entire lifetime of any generic lifetime `'a`. Note, the
compiler sees the output borrow an instance of the input's borrow-type.
Whatever concrete variable this lifetime is infered from will still be borrowed
as long as the borrow returned by `foo` is in scope. That means the following
program wont compile.

```rust
pub fn main() {
    let v: &i32;

    {
        let y: i32 = 1;
        v = foo(&y);
    }

    println!("{}", v);
}
```

This wont compile because the compiler thinks `y` goes out of scope while it's
still borrowed.
