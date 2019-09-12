---
layout: post
title: "Rustaceans! We can help!"
date: 2019-09-12 13:40
categories: rust rustlang open source software
marp: false
---

# Getting Involved In The Rust Community

## DISCLAIMER

**This blog post is an alpha version of my talk for COGoldRust. If you wish to watch my talk at COGoldRust you may wish to avoid reading this blog because it will touch upon all the same ideas I plan on going over in my talk.**

## Preface: Expectations

Are you a new Rustacean trying to take your first steps into Rust's open source community? Are you trying to meet people in the Rust community but don't know where to start? Are you a maintainer interested in attracting new contributors? Then have I got the talk for you! Together we will explore some approaches to find issues to work on, seek out new projects, structure projects to help new contributors and meet other Rust developers to become happy and succcessful contributors in the Rust open source community.

Okay, Cheesy informercial-shtick aside, why am I up here? I'm here to talk about getting involved in the Rust open source community. My goal is to help new contributors and established maintainers alike. Hopefully by the end of this talk you will have a better idea of how to navigate our community and know how to best help those around you navigate it as well.

One thing this talk is not is a guide on how to learn Rust, the language. I'm not going to talk about how I learned the language or give any tips on how to improve your Rust skills directly. I think that background and personality has a huge impact on what is best for you here, so I won't presume to "The Best Way" to learn Rust.

So A little background about myself. My name is Jane Lusby, I'm a software engineer working on distributed storage at a virtualization company called "Scale Computing". I work professionally on C++ and have for about 5 years now. I was introduced to Rust in the beginning of 2018 at my work's maker night and instantly fell in love, and quickly decided that I wanted to get involved in the community at large, give back, learn more, w/e, rather than just exist as a user of the language. So what made me fall in love and want to contribute, how did the contributions go? I'm glad you asked...

# 1. Code of Conduct

One of the first things I saw when I looked into Rust was its Code of Conduct. Now let me give you a little extra background about myself, I am gay, I'm a woman, and I'm transgender. When I go looking at being social in general dealing with homophobia, transphobia, and misogyny are all things I worry about.

So when I looked at the Rust community's code of conduct and how it banned discrimination on the basis of gender, race, ethnicity, age, disability, etc, it was like a huge weight had lifted off my back. It's wonderful to be able to go into a community and know that if you run into a bigot that pretty much everyone has got your back. I didn't feel afraid to get involved. Thankfully I've never even had a problem in the last 2 years, I'm convinced it's because the CoC keeps the kind of bad actors its designed to protect against from ever joining the community. If you want people like me to get involved in your projects just mentioning that you abide by the Rust community's code of conduct goes a huge way to making us feel comfortable enough to engage.

# 2. This Week In Rust

Incase you haven't heard of it, the Rust community maintains a weekly newsletter of Rustlang news called [`This Week In Rust`](https://this-week-in-rust.org/). In it you'll find sections on recent blog posts, the crate of the week, call for participation, changes merged to core, upcoming meetups, and job postings.

TWIR single handedly drove me to make my first open source contributions in the Rust community. I'd wanted to contribute to open source for years but I had never succeeded in finding an opportunity that actually got me to engage. I think the main issue here is that my ADHD brain makes it hard to dive into the unknown without a strong motivation, "later" it always says.

Then along comes TWIR, just waving the flag of friendship weekly. "Heres something you can do to help" it would say to me. The weekly list of friendly first issues I could get involved in made it trivial to find issues that were available to work on and approachable for someone new to the code base. I didn't have to do anything now to find work, it just walked up to me on its own whenever I went to read the news. And work I did.

## 2.1. First Contributions

So I got involved, I did a minor issue on [rustc](https://github.com/rust-lang/rust/pull/50793), and all of a sudden the problem space wasn't so scary and I started feeling motivated to look for issues on my own. I started trying to find my own new issues to work on from the issue boards, which unfortunately is where I ran into my first few snags.

As it turns out, those issue boards can be very difficult to navigate, https://www.rustaceans.org/findwork/starters, the recommended starting point in TWIR at the time, was particularly hard to work with. It only shows the issue name, summary, and tags. I would go into issue after issue only to find someone else was already working on it. The friction was discouraging, so I switched to only using the github issues lists themselves.

The next issue I ran into was maintainer responsiveness. Now I don't want to complain here because I think the Rust lang contributors are fantastic people who do great work for little thanks and no pay and I don't think it's appropriate to expect work from them. But... some of the project maintainers were a rather slow to respond to new comments on issues.

I remember very shyly leaving comments like "I'd like to take on this feature" and waiting a day or two for a response and getting nothing then quietly deleting my comment and moving on to find a new issue. I ended up settling on doing lots of work in rustfix and clippy, because the maintainers there were particularly responsive, friendly, and personable. Shoutout to [@killercup](https://twitter.com/killercup), [@ManishEarth](https://twitter.com/ManishEarth), [@oli_obk](https://twitter.com/oli_obk), [@phansch](https://twitter.com/philhansch), [@kennytm](https://github.com/kennytm), [@flip1995](https://github.com/flip1995), [@ehuss](https://github.com/ehuss), and everyone I'm forgetting for making it so easy to get involved and contribute to your projects.

# 3. Meetups: Making Rust Friends

The next biggest breakthrough for me came with the discovery of Rust meetups. I had no idea these existed, but a coworker of mine told me I should check out the meetup app and we immediately signed up for the bay area Rustaeceans group and started going to the meetups.

For a while nothing really came out of these meetups OSS wise. I would go to meetups and listen to the talks, eat the free food, maybe talk to a person here or there, but I'm really shy around people I don't know, so I mostly kept to myself or talked with coworkers I'd managed to drag along. I was enjoying myself, and I kept going, but it wasn't until the meetup at Google's office that things Really Started Happening.

What happened at the Google meetup you ask? Manish, our wonderful meetup organizer, walked up to me, unprompted, and asked "Hey, you're Jane right?". I was shocked, how the heck did Manish know who I was? It didn't feel as though I'd done anything worthy of notice, and yet here he was asking for me by name. I replied that I was and he thanked me for my contributions to clippy, and told me if I wanted to do more to let him know, and that he would help mentor me.

This simple act of walking up to me, thanking me for my contributions, and offering to help me do more has without a doubt had a larger impact on my involvement in the Rust community and my life over the last year than any other event in the last two years. I don't think I'd be here doing this talk, or that I'd have gone to RustConf, or that I'd be giving a talk at COGoldRust, or that I'd even have started using Twitter and met so many more people, if it weren't for meeting Manish that day. I sent him a message on Discord before the meetup was even over that I was absolutely wanted to get more involved and that I wanted him to be my mentor, and he's still my Rust mentor today.

Now that wasn't the only thing that happened that day, nor the only person who had a huge impact on me. Eliza Weisman gave a talk on her library `tracing` which was super fascinating and extremely relevant to my personal and professional codebases, and Adam Perry invited me to sit with him when I was shyly sitting by myself in the corner after the meetup hoping someone would come talk to me and introduced me to Isabelle Muerte and Rain. Together all of these people essentially became my first "Rust Friends" and meeting them was the first time it really felt like I was part of the Rust community.

# 4. Mentor

With a new mentor backing me up I went home from this meetup more inspired than ever to work on open source contributions. Initially I relied on Manish mostly just to help me find more interesting issues in clippy to work on without having to go to the issue board. My idea of a mentor at the time was basically a manager, someone who would externally motivate me and help suggest things to work on, but this was far from how mentorship actually ended up working for me. He did help with motivate and find issues, but he did far more.

One amazing thing he did was encourage me to contribute to projects I was afraid of or talk to people I was intimidated by. We talked about the `tracing` repo and how I wanted to talk to Eliza and get involved, but I was completely intimidated by her and was too afraid to approach her. Manish on the other hand was like, "No, come on, she's great, talk to her, she's super friendly and will be a blast to work with." He wasn't wrong. I've probably learned more about Rust the language from doing PR work on tracing than I have from everything in the rust-lang organization combined.

Another thing he did was encourage me to write blog posts. I would often go to him to complain about features I wanted that didn't exist or talk about the bikeshed of the moment, in this case `await` syntax. He encouraged me to write a little blog post explaining why I was hoping for postfix macro to be the syntax of choice. I felt silly and underqualified doing it at the time but now, having done it, I no longer feel silly or afraid of writing blogs and I am eternally grateful for that.

Speaking of complaining about features I wanted, Manish fundamentally changed how I approach open source work. Initially my strategy was to go to repos with issues open for other people to work on and just do them. Manish on the other hand encouraged me to take those features I wanted, open issues for them, and implement them instead of random issues. Let me tell you, working on features that you personally want rather than random ones other people want is WAY more satisfying. I've found it much easier to be motivated on the features I want rather that ones I ultimately don't care about. It seems like a more sustainable approach to open source and as a result I've contributed to a ton of random repos I use just because I wanted a little feature here or there. It makes me feel warm and fuzzy inside using those features I implemented myself or thinking about those projects and the maintainers I've had the pleasure to work with.

## 4.1. Mistakes!

Unfortunately, with all of this new found motivation I got a bit ahead of myself and suddenly ran into a few snags. I got a little overzealous and opened 3 prs on cargo at once to add "minor" features that I had been wanting for ages. Turns out a few of them weren't so minor! I suddenly had this mountain of work facing me that I felt like I'd committed to and it paralyzed me for a couple of weeks. It didn't seem approachable, and so my ADHD brain went into full avoidance mode. The solution here ended up being to know my limit and to break my tasks up into smaller more approachable tasks. Once I realized, hey I can shelve two of those issues and just work on the one I care most about it was easy to get going again.

Another problem I ran into was organization. I had all of these ideas and features I wanted but I was pretty much just remembering them, talking about them when they came to mind, etc. A fair number of ideas were definitely lost in these early days. Eliza ended up being the one who helped me through this problem. Any time I mentioned a feature I wanted she told me to open an issue, and eventually it became a habit.

Now that I had all these issues laying around that I wanted to work on I needed a way to keep track of them all. The solution from this ended up being inspired by David Tolnay's usage of docs.rs as a blog platform. Instead of docs.rs I asked myself if I could use an empty github repo to track my issues. I initially started with a single tracking issue where I would link every other issue, but ended up settling on a project board as a nicer solution in many ways. Now I have a nicely organized list of every issue that integrates seamlessly all the different repos I'm working with and I can share this list with people if they're looking for things to work on! Bonus points, now people will occasionally pick up issues that I opened for myself and hadn't yet gotten around to doing. With any luck the library crater project idea I had will reach MVP state without me ever having to lift a finger. 

# 5. Twitter

The next big game changer was learning to love Twitter. Manish and Eliza unintentionally got me into it, I think the main leaping off point was when Manish convinced me to write that await blog post and announce it on Twitter so he could retweet it. I'd never been able to "figure out Twitter" before this but suddenly it clicked and I realized I could use Twitter for Rust! I started to follow every Rustacean I could find and quickly learned to navigate Twitter effectively.

Now, I've been an avid Redditor for a solid 10 years, and it has been a great way to keep on top of new emerging features, libraries, and blog posts in the Rust community. Twitter is also a great source for this content, in this I think the platform you pick makes no difference. The real difference is how you interact with people on Twitter vs Reddit. Discussion on Reddit is better for impersonal discussion, where as Twitter tends to encourage more personal discourse. It's much easier to make friends and have consistent interactions with people on Twitter than it is on Reddit, which is exactly what you need if you want to participate in a community. There are a ton of people in the Rust community who I used to knew of, thanks to Reddit, and now I know them personally and consider them friends, thanks to Twitter.

# 6. Direct Encouragement

Now the last thing is something that hasn't fully played itself out, but I expect it will be a major turning point as well. Giving talks! I've never given one other than at work in front of engineers I know personally, and I probably would have continued in that pattern for ages if it wasn't for J Haigh. Manish had been trying to convince me to give a "contribution tour" talk at one of the meetups for a while, and I was interested but I didn't feel ready yet, probably imposter syndrome or some nonsense like that. Then along comes J and they're like, "Hey I see you're active in the Rust OSS community, would you be interested in talking about any of it? COGoldRust currently has a cfp open". Now, I was interested, terrified, but interested. I told them about Manish's idea for a contribution tour and my apprehensions and feelings of being under qualified but they were nothing but encouraging, they helped helped me get over my feelings of imposter syndrome, made the cfp process seem much more approachable, and generally put a friendly face on the COGoldRust conference.

So I said yes, I'm interested, I'll do my best, and I then promptly proceeded to procrastinate. But that did not stop J! They came back and gently encouraged me to keep working on my submission more and helped me motivate when I probably wouldn't have been able to alone. I expect I would have let the deadline pass and beat myself up over it if they hadn't been so encouraging and helpful. I ended up throwing together a vague outline of the current talk, about how I learned Rust, what I contributed to, and what I learned in the process, which evolved into this current talk about how to effectively participate in the open source community.

I don't really know what will end up happening as a result of being a speaker, but I'll make sure to write a follow up blog in a few months to let yall know! Either way though J's friendliness and encouragement is the main reason I'm here currently. If there are any take aways here it is to be like J and go out of your way to find qualified speakers and encourage them to take the leap. That could be the thing that helps them finally take the step they've always wanted to take.

# Key Takeaways
- CoC gives a sense of safety to underrepresented minorities and repels bad actors
- This Week In Rust is an amazing tool for keeping informed on the goings on within the Rust community
    - TWIR is a great avenue for finding issues to work on or advertising issues you'd like help with
    - Navigating the issue boards by themselves was challenging, particularly hard to figure out which issues are available, which are interesting, and which you can learn the most from, organizing your issue list can reduce friction and attract more contributors
- Meetups and by extension conferences are one of the best ways to make friends in the Rust community and get more connected
- Mentors have a huge amplifying effect on how much work you can do and how approachable the work you want to do seems
    - Working on issues that you care about personally is the most rewarding
    - Don't bite off more than you can chew. Max for me is extra open source 1 issue actively being worked on.
    - Keep track of your active work
        - Always open an issue when you have an idea of something that you want to work on or see done
        - Save a link to the issue somewhere, I'm using a random [github project](https://github.com/yaahc/prs/projects/1) board for this.
- Twitter helped immensely with getting to know other Rust community members at a personal level that Reddit doesn't provide
- Reach out to people, it makes a huge difference.

# Bonus: Shameless Plug

So I want to take this opportunity to share a project that was inspired by the mentorship I've received from Manish, [awesome-rust-mentors](https://github.com/RustBeginners/awesome-rust-mentors). If you're familiar with `awesome-rust` or `awesome-vim` or any other such currated awesome list you probably already know what this is. It's a list of rustaceans who are interested in helping new contributors get involved and navigate the community, basically a bunch of Manishes. On it you'll find people's names, contact info, preferred pronouns, spoken languages, and topics of interest. If you're interested in getting a mentor or being a mentor you should definitely go check it out.
