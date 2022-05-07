---
layout: post
title: "RustConf 2020 CFP Proposal"
date: 2022-05-07 12:00
categories: rust error handling rustconf proposal cfp
---

# Rustconf 2020 CFP Proposal

I figured seeing examples of proposals might help with people who are putting
together proposals for this year's RustConf, so here's the proposal from my
accepted rustconf 2020 talk:

## Error Handling Isn't All About Errors

**Abstract**

Error handling in rust is incredibly diverse and constantly evolving. As such
it is often a source of confusion for new and experienced rustaceans alike.
This talk aims to clarify what error handling in rust means, the patterns that
exist, when to apply them, and what libraries currently exist to help with the
task.

### For Review Committee

**Details**

**Outline**: (30 mins)
- Brief intro of myself and the problem space (2 minutes)
- Touch on error handling primatives in the language (5 minutes)
    - Result
    - Panic
    - Try / ?
- Deeper dive on how these pieces interact and definitions of terms that are currently rather fuzzy (8 mins)
    - What exactly is an "Error"?
    - Talk about idioms that exist and common needs that are encountered (4 mins)
        - Defining errors
            - common constraints
                - size of the error
                - dyn allocations
                - ease of use
        - Chaining errors
    - Seperate what is an Error and what is Context about an error
        - Introduce Backtrace as the canonical example of context about an error
- Introduce the error trait and how it addresses the previous concerns Error Trait (3 mins)
    - Mention some shortcomings, specifically aggregating errors, and its lack
      of support for context other than Backtrace
- Introduce the concept of an Error Reporter (3 mins)
- Tie it all together and discuss libraries that exist to help (4 mins)
    - Error defining libraries: thiserror, snafu, adhocerr, etc
    - Error handling + reporting libraries: anyhow, eyre
    - Libraries that fit both: failure
    - Error utility libraries: fehler, tracing-error, errtools
- Conclusion and Questions (5 mins)

**Intended audience:**

All rustaceans, for beginners it should help clear up confusion and introduce
them to best pratices, and for experienced users it should bring them up to
date on the best practices that have evolved in recent years.

**Outcomes:**

Hopefully this talk will help crystalize the distinctions between context,
errors, and error reporters. My belief is that the lack of these distinctions
is one of the biggest sources of confusion in the error handling ecosystem, and
that if these distinctions become widely known error handling in rust will no
longer be a major roadblock to learning rust. I'd also like to see library APIs
updated in the future to better reflect these distinctions and hopefully some
RFCs for the error trait to fix the shortcomings I've hightlighted.

**Pitch**

Error handling in rust is a topic that ends up confusing new rustaceans and
experts alike. Part of the challenge is that error handling in rust is quite
flexible and the needs of the ecosystem for error handling are incredibly
diverse. Along side this, the error handling patterns and best practices in the
ecosystem are still actively evolving and crystalizing into clear abstractions,
which can make it unsurprisingly difficult to find information that that is
relevant to your needs and up to date with the latest and greatest patterns
while also making it easy to fall behind.

This talk aims to serve as a primer on the state of error handling in rust as
of 2020, the patterns that exist, the best pratices that have evolved but
aren't yet widely known, and the libraries that exist to help implement the
error handling system that best fits your project.

This talk aims to make error handling much less confusing by introducing 3 core
abstractions where there is currently one. Specifically it will introduce
Context and Error Reporters and clarify how these differ from Errors. The hope
is that with these three ideas in mind it is much easier to infer what building
blocks you need based on your problem domain.

This talk will focus on runtime errors, and the types that one would put in the
Err variant of a Result, but it will also touch on Result, the `?` operator,
the try trait, and other various fundamental building blocks of error handling.
It will briefly discuss panics and their relation to Error types. It will
attempt clearly define what an error is and separate this from context about
errors. It will also introduce a new concept of an Error Reporter, and
differentiate this from both Errors and Context.

The goal is that people can use this talk as a comprehensive guide on error
handling. It will be able to serve as a glossery of definitions of various
terms related to Error handling. It will list as many error handling idioms as
it reasonably can, and the various actively maintained and widely applicable
error handling libraries that currently exist. It should be useful to both
beginner and expert rustaceans. For beginners it will hopefully teach them new
tools and clarify distinctions that were confusing them, and for experts it
will hopefully bring them up to date on changes in the error handling ecosystem
since they first started working with rust.

If theres time the section on libraries will also try to include a bit of a
brief history on the evolution of error handling libraries and which libraries
have been superceded.
