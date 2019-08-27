---
layout: post
title: "Rustaceans! We can help!"
date: 2019-08-26 22:49
categories: rust rustlang open source software
---

# Getting Into The Rust Community

## DISCLAIMER

**This blog post is a alpha version of my talk for COGoldRust. If you wish to
watch my talk at COGoldRust you may wish to avoid reading this blog, because it
will touch upon all the same ideas I plan on going over in my talk.**

## Preface: Expectations

Okay so what is this talk about? This talk is essentially a rundown of my
experience getting involved with the rust community. I'm going to cover what
resources had the greatest impact in my becoming involved, what I tried when
first looking to contribute, what worked well, and what did not. I will give
advice that is useful to both new rustaceans and experiened rustaceans alike.
If you are looking to help or get practice and don't know where to start, or
you're maintaining a project and don't know how to get others involved, I'm
here to help you.

The goal of this talk is to help smooth out the experience of existing in the
rust community. The rust community is an amazing group of people with great
values, but there is always room for improvement! What follows are essentially
some of my ideas.

Hopefully by the end of this yall will have come away better prepared to reach
your goals. Please enjoy.

## Chapter 4: A New Rustacean

- A quick summary of my initial experience learning about rust and its
  community

Let's start with a little background about myself. Who am I? My name is Jane
Lusby. I'm a systems software engineer working on distributed storage at a
virtualization company called Scale Computing. For my day job I work mostly in
C++. Rust is a personal passion of mine, and I was introduced to it in my day
job, at one of our hacker nights.

When I first heard about rust I feel in love. As a C++ programmer it was
everything I ever dreamed of. As someone who had always wanted to learn
function programming it seemed like a great way to learn new concepts. And as
a transgender lesbian it seemed like a very safe community.

    - CoC, how awesome and encouraging it was

> I quickly became obsessed, with the language, and took every opportunity I
> could find to learn more, reading the book, finishing that maker project,
> writing what could have been an short awk script in rust for no reason other
> than to play with rust, and completing / mentoring as many projects as I could
> on exercism.io.

I quickly became obsessed with the languge, and I took every opportunity I
could to learn more. My initial strategy was to dive right in. I finished the
original maker project, a button to trigger our test suite at work. I moved
onto another personal maker project for my second foray into rust, a rpi with
an accelerometer taped to my laundry machine to push notifications to my phone.
Quickly I was gaining confidence, and I was desperate for more.

I couldn't get enough information about rust. I started following the reddit
religiously, and from there I found `This Week In Rust`. This was a tipping
point for me. This week in rust has a section titled "Call for Participation".
This was the chance I was looking for, I'd always wanted to get involved in
open source but it had never seemed acceptable. Now, for once, it seemed like
the community was holding the door wide open, and so I walked straight in.

    - TWIR, how it lead me to try to find issues on github to do

## Chapter 2: The Hunt Is On

At the time there was a link to a sorted bug page, which contained recommended
starting points for beginners looking to get involved. This list looked
amazing, but every issue I checked seemed to already have a comment from some
other rustacean asking for permission to handle the implementation. I was off
to a rough start, but I was not discouraged.

My next stop was directly on the github issue pages. I was told that all the
main repos tagged good starter issues with the tag "good-first-issue" so I
enabled the filter and away I went! I started with the rust compiler, not
because that was a good idea, but because it was the first place that was
recommended. I went looking for random issues and picked one that had no
comments on it and looked particularly easy, just a little code to clean up
empty files in the tests directory. The PR was mostly uneventful but I
distinctly remember the turn around time being rather slow. The next issue I
attempted to grab didn't get a response for a few days, I got discouraged, it
was time to try one of the other rust-lang repos.

The next best candidate I landed upon was rustfix. And wonderfully, the
maintainer, killercup, was incredibly quick to respond! I got an issue picked
up and started right away, and when I asked questions about it his replies were
prompt, friendly, and helpful. I remember distinctly how positive the
experience was working with killercup, and I believe it had a lasting impact on
my willingness to approach maintainers on my own.

Now I don't particularly remember why, but at this point I ended up mostly
contributing to clippy instead of rustfix. I believe it had something to do
with wanting to learn more about compilers.

- Start wanting to contribute to help me learn more about rust (#1 priority)
  and to give back to the community (#2 priority).

- Completing various random issues on rust tools projects
    - Mix of positive and negative interactions with maintainers, mainly
      concerns with responsiveness of maintainers.
    - Fun but hard to modivate on and pick new issues, the work I was doing was
      mostly simple and unexciting, main effort went into exploring and
      understanding the various new codebases.


- Going to meetups, getting mentorship and more encouragement from the meetup
  organizer, finding a project that was in active development to work on from
  one of the presenters
    - Mentor helped me motivate and find more interesting tasks to work on,
      better than working with github issue tracker directly.
    - Active project had more opportunities for writing complex rust code and
      was far more informative. Leaned on mentor to help figure out complex
      higher order lifetime compiler errors. Learned far more about rust from
      this project than from random good-first-issues on github.

- Have epiphany to fix bugs that I personally run into or implement features I
  personally want.
    - Easier to motivate, much more exciting to fix things that I have a
      personal stake in.
    - Some tasks ended up being much more involved than initially expected,
      back to hard to motivate, but now its because theres an intimidating
      amount of work to be done rather than boring work to be done.

- Present day, happy with my approach to picking OSS work and excited to
  continue to contribute to all the projects I've become involved in.

