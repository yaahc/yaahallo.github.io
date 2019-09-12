---
layout: post
title: "Rustaceans! We can help!"
date: 2019-08-26 22:49
categories: rust rustlang open source software
---

# Getting Into The Rust Community

## DISCLAIMER

**This blog post is a alpha version of my talk for COGoldRust. If you wish to watch my talk at COGoldRust you may wish to avoid reading this blog, because it will touch upon all the same ideas I plan on going over in my talk.**

## Preface: Expectations

Okay so what is this talk about? This talk is essentially a rundown of my experience getting involved with the rust community. I'm going to cover what resources had the greatest impact in my becoming involved, what I tried when first looking to contribute, what worked well, and what did not. I will give advice that is useful to both new rustaceans and experiened rustaceans alike. If you are looking to help or get practice and don't know where to start, or you're maintaining a project and don't know how to get others involved, I'm here to help you.

The goal of this talk is to help smooth out the experience of participating in the rust community. The rust community is an amazing group of people with great values, but there is always room for improvement!

Hopefully by the end of this yall will have come away better prepared to reach your goals. Please enjoy.

## Chapter 4: A New Rustacean

- A quick summary of my initial experience learning about rust and its community

Let's start with a little background about myself. Who am I? My name is Jane Lusby. I'm a software engineer working on distributed storage at a virtualization company called Scale Computing. For my day job I work mostly in C++. Rust is a personal passion of mine, and I was introduced to it in my day job, at one of our hacker nights.

When I first heard about rust I feel in love. As a C++ programmer it was everything I ever dreamed of. As someone who had always wanted to learn function programming it seemed like a great way to learn new concepts. And as a transgender lesbian it seemed like a very safe community.

    - CoC, how awesome and encouraging it was

> I quickly became obsessed, with the language, and took every opportunity I
> could find to learn more, reading the book, finishing that maker project,
> writing what could have been an short awk script in rust for no reason other
> than to play with rust, and completing / mentoring as many projects as I could
> on exercism.io.

I quickly became obsessed with the languge, and I took every opportunity I could to learn more. My initial strategy was to dive right in. I finished the original maker project, a button to trigger our test suite at work. I moved onto another personal maker project for my second foray into rust, a rpi with an accelerometer taped to my laundry machine to push notifications to my phone. Quickly I was gaining confidence, and I was desperate for more.

I couldn't get enough information about rust. I started following the reddit religiously, and from there I found `This Week In Rust`. This was a tipping point for me. This week in rust has a section titled "Call for Participation". This was the chance I was looking for, I'd always wanted to get involved in open source but it had never seemed acceptable. Now, for once, it seemed like the community was holding the door wide open, and so I walked straight in.

    - TWIR, how it lead me to try to find issues on github to do

## Chapter 2: The Hunt Is On

At the time there was a link to a sorted bug page, which contained recommended starting points for beginners looking to get involved. This list looked amazing, but every issue I checked seemed to already have a comment from some other rustacean asking for permission to handle the implementation. I was off to a rough start, but I was not discouraged.

My next stop was directly on the github issue pages. I was told that all the main repos tagged good starter issues with the tag "good-first-issue" so I enabled the filter and away I went! I started with the rust compiler, not because that was a good idea, but because it was the first place that was recommended. I went looking for random issues and picked one that had no comments on it and looked particularly easy, just a little code to clean up empty files in the tests directory. The PR was mostly uneventful but I distinctly remember the turn around time being rather slow. The next issue I attempted to grab didn't get a response for a few days, I got discouraged, it was time to try one of the other rust-lang repos.

The next best candidate I landed upon was rustfix. And wonderfully, the maintainer, killercup, was incredibly quick to respond! I got an issue picked up and started right away, and when I asked questions about it his replies were prompt, friendly, and helpful. I remember distinctly how positive the experience was working with killercup, and I believe it had a lasting impact on my willingness to approach maintainers on my own.

Now I don't particularly remember why, but at this point I ended up mostly contributing to clippy instead of rustfix. I believe it had something to do with wanting to learn more about compilers.

Actually ended up mostly just following This Week In Rust for the most part for a while. The recommended issues tended to be good starting points that were available. Over time the recommended issues slowly helped me explore the community until I ended up finding maintainers who were responsive in clippy and rustfix.

For the next while I continued self teaching, mentoring problems on exercism.io and working on small issues here and there, mostly in clippy. On June 28th 2018 I went to my first rust meetup. I was intensely shy and didn't do much other than listen to the talks and eat some food, but it was a positive experience and I kept coming back whenever I could.

March 13th 2019 was the next major milestone for my rust community involvement. I'd been to quite a few meetups by now and talked to some people here and there, but this meetup was different. During the beginning of the meetup Manish walked up to me and said hi, he knew who I was from my contributions to clippy and came up to thank me and to let me know to contact him if I'm interested in doing more contributions. I desperately needed this, and having someone to help find work that was more interesting and to sidestep the grind of searchin through the issue trackers was exactly what I'd wanted. I immediately resolved to contact him and did so that night. Meanwhile at the meetup itself I listened to Eliza talk about her crate `tracing`, which looked amazing and resonated strongly with me, and I resolved that I wanted to use / help out with that crate as well.

The main thing that was motivating me through this period was a desire to learn rust. Pretty much any project that I could find managed to draw my attention. But underneath that was a desire to give back to the community. I'd grown up with the greatest respect for open source developers, and had always wanted to *be* one. The open source issues that I took on were ultimately motivated by both sources (horrible wording).

- Start wanting to contribute to help me learn more about rust (#1 priority) and to give back to the community (#2 priority).

- Completing various random issues on rust tools projects
    - Mix of positive and negative interactions with maintainers, mainly concerns with responsiveness of maintainers.
    - Fun but hard to modivate on and pick new issues, the work I was doing was mostly simple and unexciting, main effort went into exploring and understanding the various new codebases.

One of the biggest issues that held me back during this period was a lack of motivation. I have ADHD and so it can be difficult to control what I focus on. It was really easy to focus on rust in general because I was passionate about it, but specific issues in rust were harder. Many of the "good first issues" that were recommended ended up being trivial, which makes sense, but it didn't teach me much, which was why I'd decided to do them in the first place, and I didn't get much personal satisfaction out of them, because they were essentially picked at random from a work queue. 

- Going to meetups, getting mentorship and more encouragement from the meetup organizer, finding a project that was in active development to work on from one of the presenters
    - Mentor helped me motivate and find more interesting tasks to work on, better than working with github issue tracker directly.
    - Active project had more opportunities for writing complex rust code and was far more informative. Leaned on mentor to help figure out complex higher order lifetime compiler errors. Learned far more about rust from this project than from random good-first-issues on github.

contributing to `tracing` was something of a breakthrough for me personally. It was different from the other projects I'd worked on because A. it was in very active development, and B. the internals made very heavy use of advanced rust features. The problems I took on with tracing were not nearly as trivial as the ones I worked on for the various rust-lang repositories. In rust lang the main issue is usually exploring the code and understanding where everything is, in tracing I was genuinely stumped by how to express the new constructs I wanted to, which was really what I was looking for, I wanted a challenge that was hard to understand, not something that just took time to understand.

I probably wouldn't have even continued to contribute to tracing if it weren't for eliza and manish. At this point I was beyond the questions that were easily answered by googling or looking at the reference. I had multiple occasions where I had to ask one of them directly to help me understand the code I was working on and why I was getting the errors I was seeing. They gave great detailed answers, and without a doubt I've learned more from working on `tracing` than any other project to date.

- Have epiphany to fix bugs that I personally run into or implement features I personally want.
    - Easier to motivate, much more exciting to fix things that I have a personal stake in.
    - Some tasks ended up being much more involved than initially expected, back to hard to motivate, but now its because theres an intimidating amount of work to be done rather than boring work to be done.

Around this time I came to a crucial realization. The easiest way to get involved isn't to find issues, its to make them. The issues that I log are 10x as satisfying to work on, because I see concrete problems I've run into being solved and I personally benefit from the work I do, rather than it being purely charitable. Its nice to try to focus on giving back to the community but its not a particularly sustainable model. For me personally I need a stake in what I'm working on for it to be truly fun to work on.

After this realization though I ran into a bit of a snag. I got ahead of myself and took on a bunch of projects that I'd wanted to see done forever. Within a week I'd gotten responses on all ov them and suddenly realized that for a few of them the scope was much larger than I'd initially anticipated. This sudden flood of expected work was extremely intimidating, and I became paralized for a couple of weeks on all of the projects I'd taken on as a result. Eventually I realized that I needed to split the tasks up and take them one at a time in order to make it feel manageable which is crucial for me personally to be able to motivate to work on something. Once I started focusing on the most important next task I went back to making steady progress.

- Present day, happy with my approach to picking OSS work and excited to continue to contribute to all the projects I've become involved in.

I'm currently pretty happy with my approach 