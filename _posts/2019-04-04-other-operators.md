---
layout: post
title: "An Exploration of C++'s 'Other Operators'"
date: 2019-04-04 13:39:00 -0700
categories: c++ cpp rust rustlang
---

# C++ Other Operators

It's dangerous to go alone, take this.

- <https://en.cppreference.com/w/cpp/language/operator_other>

Feel free to reference this while you are reading. I think it's a interesting read, but don't worry too much because I include the most relevant sections inline. What follows are a series of _fun_ facts C++'s other operators, how I feel about them, and how they relate to Rust. (_Of course_)

## A Story of What's to Come

I plan on going in order through the list of three operators from the above page. The function call operator, the comma operator, and the conditional operator (ternary conditional). The first story discusses the implications of [order of evaluation][1] in C++. It's a bit of an outlier and does not factor in much to the rest of the talk but I felt I could not leave one of the operators out. The second story explores the behaviors of the often used and far less often understood comma operator. The last story is about the conditional operator. It's the genesis of this post, and briefly visits the crazy ball of wax known as [value categories][2].

Let's get right into it!

## The Function Call Operator

This story starts with a simple example from "Effective Modern C++" chapter 21.

```cpp
processWidget(std::shared_ptr<Widget>(new Widget), // potential
              computePriority());                  // resource
                                                   // leak!
```

Potential resource leak, Wait... what? Okay, well, I guess that means we should refer to the documentation for the function call operator.

```
E ( A1, A2, A3,... )

- The expression E as well as all expressions A1, A2, A3, etc, provided as
  arguments are evaluated in arbitrary order, unsequenced with respect to each
  other. (until C++17)
- The expression E is sequenced before each of the expressions A1, A2, A3 as
  well as default arguments, if any. The argument expressions are evaluated in
  arbitrary order, indeterminately sequenced with respect to each other. (since
  C++17)
```

Unsequenced? Indeterminately Sequenced? What does that mean? Let's dig deeper.

```
- If A is not sequenced before B and B is not sequenced before A, then two
  possibilities exist:
    - evaluations of A and B are unsequenced: they may be performed in any
      order and may overlap (within a single thread of execution, the compiler
      may interleave the CPU instructions that comprise A and B)
    - evaluations of A and B are indeterminately sequenced: they may be
      performed in any order but may not overlap: either A will be complete
      before B, or B will be complete before A. The order may be the opposite
      the next time the same expression is evaluated.
```

Okay got it, the expressions in the function call can be reordered however the compiler likes. In this case those expressions are `new Widget`, `std::shared_ptr<Widget>(ptr from previous expression)`, and `computePriority()`. The real problem occurs if one of the expressions can throw an exception. In this case we'd be worried about `computePriority()`. If the compiler orders the execution such that it first allocates the widget, then calls `computePriority()` prior to constructing a shared pointer to manage the new Widget's memory, and the `computePriority()` call throws, we will never construct a shared pointer to manage the memory for our Widget, and we unknowingly miss out on all the wonderful RAII style memory management we thought we'd signed up for.

What are we supposed to do you might ask. Let's start with the name of the chapter this nugget of wisdom came from, `Prefer std::make_unique and std::make_shared to direct use of new`. Oh, okay then.

```cpp
processWidget(std::make_shared<Widget>(), // no potential
              computePriority());         // resource leak
```

By using `make_shared` rather than `new` and a constructor we turn two expressions into one as far as the function call operator is concerned. It will probably make sure not to interleave a throw from another expression with our expression. Now we either get to run make_shared and then throw after, in which case we get our widget deallocated. Or we throw before make_shared, and we never allocate it in the first place. Perfect!

What's the moral of the story? I'm inclined to say it's `avoid new whenever possible` but that's just me. If you need dynamic memory you should probably always use some generic class that abstracts the memory management for you. That's the only way to allocate dynamic memory in safe Rust and I hear they're pretty good about not messing up their memory management. Particularly with shared_ptr there are other benefits such as `make_shared` only requiring one dynamic memory allocation whereas the constructor plus new require two. Though this can have some undesirable side effects when you mix in large memory allocations and weak_ptrs. If you want to learn more about the intricacies of smart pointers in C++ I cannot recommend `Effective Modern C++` enough.

## The Comma Operator

This next tale is a fun one that starts to touch on the main topic, [statements][3] vs [expressions][4] in C++ and Rust. Let us start with a some examples of what is and is not the comma operator.

```cpp
int a = 1, b = 2;                       // 1. nope
processWidget(E1, E2);                  // 2. also nope
processWidget((E1, E2));                // 3. yup!

for(int i = 0, j = 1; i < 10; i++, j++) // 4. no and yes
    ;

int i, j;
for(i = 0, j = 1; i < 10; i++, j++)     // 5. yes and yes! (I think)
    ;
```

Let's break these down.

The first one is a [simple declaration statement][5], their words, not mine. The commas in that statement are separators in what is called the `init-declarator-list`.

The next one is a function call, the commas are part of the function call operator. If we go back to the documentation on that bad boy we can find this extra piece of wisdom.

```
A1, A2, A3,... is a possibly empty list of arbitrary expressions, except the
comma operator is not allowed at the top level to avoid ambiguity.
```

Okay so they banned comma operator in function calls, boring but okay, I guess it makes sense. What's with the next one though? To properly explain why that _is_ the comma operator we must first explore the definition of the comma operator.

```
E1 , E2

In a comma expression E1, E2, the expression E1 is evaluated, its result is
discarded (although if it has class type, it won't be destroyed until the end
of the containing full expression), and its side effects are completed before
evaluation of the expression E2 begins (note that a user-defined operator,
cannot guarantee sequencing) (until C++17).

The type, value, and value category of the result of the comma expression are
exactly the type, value, and value category of the second operand, E2. If E2 is
a temporary expression (since C++17), the result of the expression is that
temporary expression (since C++17). If E2 is a bit-field, the result is a
bit-field.
```

Pay attention to that bit about value category because that's going to come up more later, but for now let's focus on the comma operator, I hope it reminds you of `;` (not an operator). There are two key facts here, first, the entire comma separated series of expressions is itself an expression, whereas a semicolon separated series of expressions is a series of [expression statements][4]. Both `;` and `,` are similar in that they separate series of expressions, the difference is that one does so by turning them into statements, and the other does so by turning them into an expression that evaluates to the value of the last expression. The second important fact is that any expression that is wrapped in parentheses is itself considered to be a [primary expression][4].

When we say `processWidget((E1, E2))` it's as if we had said `E1; processWidget(E2)` (don't quote me on this). There are some differences between this and what actually happens around when the destructors from objects created by E1 would get called but dont worry about that. You end up with calling `processWidget` with a single argument `E2`'s type. It's not even calling the same function as the previous example, fun. But now that we know that the rest should be easy.

The for loop statement starts with a declaration statement, followed by a boolean expression, followed by a single expression, but we want to do two things in this part of the for loop, so we use the comma operator to let us sneak two expressions in where one is expected. Brilliant! This is also the use case pretty much everyone is familiar with.

The last example is hopefully obvious by now, the declaration is moved outside of the loop, and instead the first section of the for loop contains a comma operator separated list of assignment expressions and the rest is identical to the previous example.

I think it's worth pointing out that you can implement `operator,` for your own types. It's also worth pointing out that there are a ton of ways you can shoot yourself in the foot doing this and that you probably shouldn't ever do it. For more information look [here][7] and then [here][8] and maybe even [here][9].

Another situation where the comma operator sees legitimate use is C++11 constexpr function programming. Which has the constraint that it must only contain a single return statement (and a few other things, but what's important is that things like expression statements aren't allowed), so you can use the comma operator to sequentially execute multiple expressions where only one is expected. You know what else gets used a lot in constexpr programming for C++11? The `conditional operator`! Seriously check out the [examples][6] for the documentation on constexpr, it's chock-full of conditional operator expressions. And, interestingly but unsurprisingly enough, it's used for the exact same reasons that the comma operator is used! Let us explore why.

## The Conditional Operator

The entire reason I wanted to give this talk is really to mention this simple example my friend [Marbin][10] showed me a week ago.

```c++
void whyThough(bool a, int& b, int& c) {
    ( a ? b : c ) = 1;
}
```

This is a perfectly legal expression and finding that out blew my mind. An explanation for why this works can be found in both the page for [value categories][2] and the page for [other operators](https://en.cppreference.com/w/cpp/language/operator_other). Sadly it's really dense no matter which one you look at, so I will paraphrase. If both arms of a conditional operator expression are lvalues, then the entire expression is usually an lvalue as well. If you want to know why I said usually I recommend reading the documentation yourself, but for now you'll probably be fine if you just treat this simplification as truth. Disclaimer, this section of the documentation mentions lvalues, glvalues, rvalues, prvalues, xvalues, cv-qualifiers, implicit conversion sequences, and who knows what else. It's a lot to take in.

For those of us who barely remember or don't know what these value categories are I think it helps to know how Rust defines them, because it's a lot simpler and mostly applies back to C++ (except where it doesn't). In Rust there are place expressions and value expressions. These loosely correlate to lvalues and rvalues respectively in C++. Place expressions are defined as expressions that represent a memory address, value expressions are defined as !(place expression). If you have trouble remembering which one is an lvalue and which one is an rvalue, know that lvalue comes from lef-hand side value, and rvalue comes from right-hand side value. In `int a = 0; a = iReturnRandomInts();` `a` is an lvalue and the return value from `iReturnRandomInts` is an rvalue. Conveniently they're both on the correct side of the expression based on their names.

If you apply the definitions of place expressions and value expressions from Rust to lvalues and rvalues in C++ you'll rarely be wrong. If you want to know more about it check out [this lovely blog](http://willtipton.com/coding/2014/12/14/value_categories.html).

Anyways, back to the conditional operator. Because the entire thing is an lvalue we can do things that you're only allowed to do to lvalues with it, like assign to it or take its address! I don't have any profound gotchas about this one, I just feel that it's weird that conditional operator expressions aren't always rvalues. But this leads nicely into the discussion I want to have about these operators and their place in the C++ language.

## My Feelings

Now's where I really start talking about Rust. Super short story time, my old boss was a bit of a Functional Programming Evangelist, and as a result I started paying attention to functional programming whenever it came up as a topic though I didn't dig into it. When I first heard about Rust I went and browsed the Wikipedia article about it and thanks to my conditioning from Ian when I came upon the following blurb it burned into my mind for all eternity.

```
Despite the superficial resemblance to C and C++, the syntax of Rust in a
deeper sense is closer to that of the ML family of languages as well as the
Haskell language. Nearly every part of a function body is an expression, even
control flow operators.
```

In Rust, virtually everything is an expression. Just take a look at this screenshot from the Rust reference.

![statements and expressions from the Rust Reference](/assets/sofew.png)

And compare that to the list of statements in cppreference.

![statements from cppreference](/assets/somany.png)

Where C++ has compound statements Rust has block expressions. Where C++ has return statements Rust has return expressions. Where C++ has iteration statements Rust has loop expressions. You no doubt see a pattern forming. The only things in Rust that are statements are declarations, things like variables or functions, and expression statements, which are expressions followed by a semicolon. Everything else is an expression. To me it feels like the conditional operator and comma operator in C++ were added only because they had already decided that conditions and blocks were statements, but it turns out that compounding expressions and having conditional expressions is super useful, so they added them in after the fact. Whereas Rust was able to learn from the mistakes of its fore bearers and generalized all of these statements into expressions. The equivalent of `auto d = (a ? b : c);` in Rust is `let d = if a { b } else { c };` and the equivalent of `auto d = (E1, E2, E3);` is `let d = {E1; E2; E3 };`. One difference is that these block and if expressions in Rust are always value expressions, so you can't assign to them without using something like a macro, unlike their C++ counterparts which inherit their value category from the relevant sub expressions and can be assigned to, which is nifty.

I think the reason I find these last two operators so fascinating is that they seem out of place. It's like they're two mercenaries from Functional-Programming-Land who ended up settling down in C++Land. I like them conceptually and I value them for the situations where they're genuinely useful, but I think I'm not alone amongst c++ developers who avoid these operators whenever we can. Not because they're bad, but because they're inconsistent with the rest of the language and they feel arcane. Thats where I think rust benefited from the lessons of the past. It managed to include these useful features into the core syntax, so when you use an if statement in the expression position in rust it doesn't feel out of place, it feels concise, which is exactly what these operators are meant to do for C++.

## Conclusion

Quick review of what we covered.

- value categories (place vs value or lvalue vs rvalue)
- indepth comparison between statements and expressions
- the dangers of sequencing and exception safety
- one small reason I'm in love with Rust

Thank you all for reading, I hope you all enjoyed this as much as I enjoyed writing it.

### Moral?

Maybe C++ devs shouldn't hate on using comma-operator / conditional-operator as much as they do.

[1]: https://en.cppreference.com/w/cpp/language/eval_order
[2]: https://en.cppreference.com/w/cpp/language/value_category
[3]: https://en.cppreference.com/w/cpp/language/statements
[4]: https://en.cppreference.com/w/cpp/language/expressions
[5]: https://en.cppreference.com/w/cpp/language/declarations
[6]: https://en.cppreference.com/w/cpp/language/constexpr
[7]: https://ideone.com/cYvQrz
[8]: https://blog.codeisc.com/2017/12/26/cpp-comma-operator-introduction.html
[9]: https://en.wikipedia.org/wiki/Comma_operator
[10]: https://github.com/Chibin
