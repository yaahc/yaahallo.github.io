---
layout: slides
title: "Rust Doesnt Suck: Enums"
date: 2019-07-03 12:58
categories: rust enums ADT tagged-unions
---

<section data-markdown data-separator="^\n---\n$" data-separator-vertical="^\n--\n$">
<textarea data-template>

# Rust Doesn't Suck - Part 1
## Enums

Jane Lusby

--

## What are enums in rust?

- Algebraic Data Types <!-- .element: class="fragment" -->
- Sum Types <!-- .element: class="fragment" -->
- Tagged Unions <!-- .element: class="fragment" -->

--

## What does an enum look like in rust?

--

```rust
enum ExampleEnum {
    Variant1(i32), // tuple-like
    Variant2{ name: String, age: u8 }, // struct-like
    Variant3, // fieldless
}
```

--

## What would this look like in C/C++?

--

```cpp
// assume the tag maps to the following variants
// 1 = Variant1, 2 = Variant2, 3 = Variant3
struct ExampleEnum {
    uint8_t tag;
    union internal {
        int32_t variant1;
        Variant2_T variant2;
        // variant3 is represented purely by the tag
    } internal;
};
struct Variant2_T {
    std::string name;
    uint8_t age;
};
```

--

## How do you declare an enum in rust?

--

## Enums implicitly declare a set of constructors, one for each variant.

```rust
let my_cats_name = String::from("Andrew");
let his_age = 1;

let v = ExampleEnum::Variant2{
    name: my_cats_name,
    age: his_age,
};

// now you can use it
foo(v);
```

--

## How about using enums in rust?

- Pattern matching <!-- .element: class="fragment" -->
- if let expressions <!-- .element: class="fragment" -->
- match expressions <!-- .element: class="fragment" -->
- associated functions <!-- .element: class="fragment" -->

--

```rust
// Example using if let
fn foo(bar: ExampleEnum) {
    if let Variant2{ name, age } = bar {
        println!("name: {} and age: {}", name, age);
    } else {
        panic!("we only ever expect Variant2's in this function! We received {:?}", bar);
    }
}
```

--

```rust
// Example using match expression
fn foo2(bar: ExampleEnum) {
    match bar {
        ExampleEnum::Variant1(val) => println!("arbitrary value: {}", val),
        ExampleEnum::Variant2(name, age) => dbg!(name, age),
        ExampleEnum::Variant3 => println!("whats with that empty variant?");
    }
}
```

--

Equivalent in C++.

--

```cpp
// example recreating if let
void foo(ExampleEnum bar) {
    if bar.tag == 2 {
        auto name = bar.internal.variant2.name;
        auto age = bar.internal.variant2.age;

        cout << "name: " << name << " and age: " << age << endl;
    } else {
        cerr << "we only ever expect variant2's in this function, We received "
            << bar.tag
            << /* omit logic to format and render the other variants for debug output */
            << endl;

        throw "bad enum variant";
    }
}

// match example not recreated
```

---

# Sum types can represent powerful ideas

- Nullable values <!-- .element: class="fragment" -->
- Exceptions <!-- .element: class="fragment" -->
- Copy on Write types <!-- .element: class="fragment" -->
- Abstract Syntax Trees <!-- .element: class="fragment" -->
- Linked List <!-- .element: class="fragment" -->
- Much much more <!-- .element: class="fragment" -->

--

# Nullable Values

```rust
enum Option<T> {
    None,
    Some(T),
}
```

--

# Exceptions

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

--

# CoW types


```rust
pub enum Cow<'a, B: ?Sized + 'a>
    where B: ToOwned
{
    // Borrowed data.
    Borrowed(&'a B),

    // Owned data.
    // Please ignore the \ below, reveal.js
    // is stupid and treats it as an html tag otherwise
    Owned(<B as ToOwned\>::Owned),
}
```

--

# AST - json objects

```rust
enum Value {
    Null,
    Bool(bool),
    Number(Number),
    String(String),
    Array(Vec<Value>),
    Object(Map<String, Value>),
}
```

--

# Singly Linked list

```rust
pub struct List {
    head: Link,
}

enum Link {
    Empty,
    More(Box<Node>),
}

struct Node {
    elem: i32,
    next: Link,
}
```

--

# Much much more

- State machines with state associated data
- Async / Await
- Subcommand Args/configuration (scbuild)

---

# Questions?

- More on pattern matching
- More on traits
- Elaborate on associated functions
- Optimizations involving discriminants, aka NPO, etc

---

# FIN

Now that you know enums, we are ready for the next talk. Error handling and explicitness in rust.

</textarea>

</section>

