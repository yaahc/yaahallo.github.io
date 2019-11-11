---
layout: post
title: "2020 Rust: Accessibility"
date: 2019-09-12 13:40
categories: rust rustlang open source software accessibility 2020 blog
marp: false
---

For a while I didn't really feel a need to write a blog post for 2020. As far
as I can tell we the Rust Community are doing a great job, and I just want us
to keep doing what we've been doing. My biggest worry is that the people who
contribute the most are dangerously close to burning out and I [made a
tweet](https://twitter.com/yaahc_/status/1189404270010884103?s=20) much to that
effect.

But I was talking with someone recently about what makes Rust great, the
language and the community, and that ultimately boiled down to how inclusive
and accessible we are, and it made me think saying more than just "keep doing
what we're doing and take care of yourselves" is in order.

Rust is fundamentally a language of accessibility. The byline on the main
webpage for the lang is "A language empowering everyone to build reliable and
efficient software." Everything we've done upto today shows a commitment to
this idea.

* Cargo as a toolchain is all about making it easy to share and reuse code and
  setup projects.
* The community was built around a strongly inclusive Code of Conduct and it
  shows.
* Async functions are all about making it easier to write futures and high
  performance async io code.
* Our compiler errors are famous for being incredibly helpful, frequently
  linking directly to guides that help you understand the issue you're running
  into fully.
* The compiler team views cases where you cant figure out whats wrong from a
  compiler error as a bug and it shows in how helpful they are.
    * Just interact with Esteban once and you'll fall in love with opening
      issues for confusing compiler errors.
* It's a meme that our community is run by queers because of how inclusive we
  are of lgbt people.

Every point here has one thing in common, and that's accessibility. I think the
most important thing going forward is to keep focused on what made us great in
the first place. Shiney new tech is great but it's even better when everyone
gets to use it.

I don't really have any examples of people going against accessibility,
thankfully, but that doesn't mean we still don't have room to improve! To get
you started here are some ideas I was able to come up with on what we can all
do to keep working on accessibility.

* Write tutorials and docs that help people use your projects/tools/libraries
  so that more people can jump in and use / contribute.
    * I recommend watching [this wonderful talk by Meike Baumgärtner when the video comes out](https://llvmdevmtg2019.sched.com/event/W2zN/llvm-tutorials-how-to-write-beginner-friendly-inclusive-tutorials).
* Write blog posts so people know what you're working on and can get excited
  about it.
* Focus not just on adding new features, but on making those features easy to
  use.
* Work on accessibility for our webpages and localization so other natural
  language speakers can get involved at the same level as english speakers.
* And many more ideas that I'm sure other wonderful Rustaeceans will come up with.

I want nothing more than to see rust continue to grow and continue to be the
amazing lang/community it is today, and I think the key to this is a continuing
focus on accessibilty.

**PS**: *And of course postfix macros are CRITICAL, someone please, my CLOC is dying.*
