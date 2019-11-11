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

To me, Rust is fundamentally a language of accessibility. I mean, the byline on
the main webpage for the lang is literally "A language empowering everyone to
build reliable and efficient software." And if you look, you'll notice that
everything we've done up to today shows a commitment to this idea.

* Cargo is all about making it easy to share and reuse code and setup projects.
* The community was built around a strongly inclusive Code of Conduct and it
  shows.
* Async functions are all about making it easier to write futures and high
  performance async io code.
* Our compiler errors are famous for being incredibly helpful, frequently
  linking directly to guides that help you understand the issue you're running
  into fully.
* The compiler team views confusing compiler errors as a bug and it shows in
  how helpful they are.
    * I mean this very seriously, just interact with Esteban once and you'll
      fall in love with opening issues for confusing compiler errors, he and
      everyone else involved are SO NICE AND HELPFUL omg I love them.
* It's a meme that our community is run by queers because of how inclusive we
  are of lgbt people.

Every point here has one thing in common, and that's accessibility. I think the
most important thing going forward is to keep focused on what made us great in
the first place. Shiney new tech is great but it's even better when everyone
gets to use it.

Now, I don't really have any examples of people going against accessibility,
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
    * The replies to [this tweet](https://twitter.com/yaahc_/status/1193973822635905024?s=20) have helpful suggestions on how to make your webpages accessible.
* Think about mentoring people or getting a mentor.
    * Insert Shameless Plug of [awesome-rust-mentors](https://rustbeginners.github.io/awesome-rust-mentors/)
* And many more ideas that I'm sure other wonderful Rustaeceans will come up with.
    * Seriously though, if you have any more ideas on accessibility please @ me
      on twitter and tell me, I'll add your ideas to this list.
* [From [@Sunjay03](https://twitter.com/Sunjay03)] Add a code of conduct to your project, [heres why.](https://help.github.com/en/github/building-a-strong-community/adding-a-code-of-conduct-to-your-project)
* [From [@Sunjay03](https://twitter.com/Sunjay03)] Contribute to other projects to help them be more
  accessible, rather than putting all the burden on the maintainer.

I want nothing more than to see rust continue to grow and continue to be the
amazing lang/community it is today, and I think the key to this is a continuing
focus on accessibilty. Be kind, be helpful, stay accessibilty.

**PS**: *And of course postfix macros are CRITICAL, someone please, my CLOC is dying.*
