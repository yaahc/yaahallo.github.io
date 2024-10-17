---
layout: post
title: "Life as a Rust Project Contributor"
date: 2024-10-16 14:40
categories: rust rustlang open source software
marp: false
---

# Life as a Rust Project Contributor

Hello everyone, my name is Jane Losare-Lusby. I'm a Principal Open Source Engineer at Futurewei Technologies working on the Rust Project. Today I'm going to be giving a talk about life as a Rust Project Contributor.

## Goals

### Disclaimer

> If we want to connect deeply, we need to be authentic. If we want to be authentic, we have to show up as our whole selves, weak spots and all. If we want to connect deeply we need to be vulnerable with each other. - Ted Rau[^3]

Before I get into the talk itself I would like to describe what kind of talk this is then give a bit of a disclaimer. Starting with this quote: _reads quote_. This is gonna be a pretty vulnerable talk. I'm going to talk about my personal experiences working in the rust project for coming up on five years now, and there have definitely been some rough spots which have taught me valuable lessons I'd like to share. Also, keep in mind that since this comes from personal experience, what I say here won't apply to everyone. Think critically about whether or not any of the advice I give here makes sense for you.

### Previous Talk, Navigating the Rust OSS Community

This isn't my first talk on this general topic, I gave a talk titled "Navigating the Rust OSS community" in 2019 that was much more focused on first time contributors and helping maintainers support and attact those new contributors. You can think of this talk as a sequel to that talk, which I highly recommend watching if you're struggling to figure out how to get started in the project. you can find the talk in blog form and a link to the video here: https://yaah.dev/getting-involved

### Overview

This talk on the other hand is going to focus much more on helping people *stay* involved in the project. I'll do this by first, sharing an overview of the governance of the rust project to help you understand how decisions are made and how work gets done within the project. Then I'm going to share the lessons I've learned along the way on how I maintain my mental health, and finally I'll share the strategies I've learned for making contributions and proposals as painless as possible, using the current project I'm working on, the metrics initiative, as an example.

## How does the rust project work?

Okay so how does the rust project work? I'm going to be borrowing from a blog post by Mara Bos, co-lead of the library team, titled "Rust is not a Company". You're probably familiar with how most companies function. They have directors, shareholders, and other executives at the top of the organizational heirarchy. These groups set goals for the company, they plan how to achieve those goals, setting targetes, milestones, deadlines, and projects, then they assign work to those they manage and track the progress of that work. Most companies function in a top down manner where the people at the top have the authority to make descisions for those under them, primarily what they work on.

Now we do have an organizational heirarchy, and just looking at that team structure (which can be found at rust-lang.org/governance) you might think the rust project operates in a similar way, but there are some key differences and in some ways it's essentially the opposite. Critically, nobody in the rust project has the authority to tell anyone else what they work on. We do not assign each other work. Teams often publish roadmaps, but these are about communicating priorities, not assigning work. Its a way to let our contributors know "hey the lang team is going to be prioritize reviewing and supporting this piece of work" and "we'd love more contributions towards this goal" but it's all about encouragement not command. 

The teams do have authority to make decisions such as what features get merged and stabilized, and who joins the team, but this is done as a group decision, where as long as everyone on the team consents to a proposal it can move forward, but if any team member objects that objection has to be resolved before the proposal can move forward. There's a lot more detail to how this process works that I don't have time to cover, so if you want more information I recommend reviewing the documentation on the council decision making process on forge.rust-lang.org (this is where a majority of the policy for the project is documented): https://forge.rust-lang.org/governance/council.html#the-councils-decision-making-process, this section is general and applies to many different decision making processes used throughout the project, such as the RFC (request for comment), MCP (major change proposal), or ACP (API change proposal) processes.

All contributors within the project work on a volunteer basis and have full consent over what we work on. Many of us are employed by external companies who may assign us goals to achieve, but we still have to go and convince other project members to consent to those goals in order to actually get any of those changes merged.

As a result of these differences the rust project operates essentially from the bottom up, instead of the top down. Our leaders act more like shepherds, cooardinating work, making sure it all fits together nicely, and helping unblock discussions. The actual direction of the project is the sum of the goals and work done by all the active contributors. Our leaders are quite limited in how much they can actually nudge the direction of that work.
  
## Initial Contributions & Clippy Team

Okay so that should hopefully be enough to give you a vague idea of the environment of the rust project, and should help you understand the next section of the talk, my story working within the rust project.

My involvement with the Rust project began with Clippy contributions for self-enrichment. I came from a C++ background and had recently fallen in love with rust and wanted to become a better Rust programmer, so I sought out an environment of experts I could learn from. 

This is when I discovered and started going to Rust meetups. I had no idea these existed, but a coworker of mine told me I should check out the meetup app and we immediately signed up for the bay area Rustaeceans group and started going to all of the meetups.

For a while nothing really came out of these meetups. I would go to meetups and listen to the talks, eat the free food, maybe talk to a person here or there, but I’m really shy around people I don’t know, so I mostly kept to myself or talked with coworkers I’d managed to drag along. I was enjoying myself, and I kept going, but it wasn’t until the meetup at Google’s office that things Really Started Happening.

What happened at the Google meetup you ask? Manish, our wonderful meetup organizer and lead of the Clippy team, walked up to me, unprompted, and asked “Hey, you’re Jane right?”. I was shocked, how the heck did Manish know who I was? It didn’t feel as though I’d done anything worthy of notice, and yet here he was asking for me by name. I replied that I was and he thanked me for my contributions to clippy, and told me if I wanted to do more to let him know, and that he would help mentor me.

This simple act of walking up to me, thanking me for my contributions, and offering to help has had a profound impact on my life. I don't know if I'd have this job, if I'd be here today, if it weren't for Manish's simple act of outreach that day.

In retrospect I realize this was me struggling with Imposter syndrome. I had built up these big expectations of what it meant to be an open source contributor, and how much work you had to do to be acknowledged as one. I thought my contributions were trivial, I'd done things like implementing new lints, cleaning up the repo, and improving the test framework. What I didn't realize is that this is just part of the process, it's pretty common to start with small contributions and get bigger over time. This helps you focus initially on how the process of contributing works before you get into making larger changes. Manish's outreach helped me get through this mental block that I didn't even realize I had. I cannot emphasize enough how important finding community and making friends is for being successful in open source and the Rust project. Working in open source can be isolating, and you have to actively work to counteract that dynamic to not lose your mind.

Now meeting Manish wasn’t the only thing that happened that day, nor the only person who had a huge impact on me. Eliza Weisman gave a talk on her library tracing which was super fascinating and extremely relevant to my personal and professional codebases, and Adam Perry invited me to sit with him when I was shyly sitting by myself in the corner after the meetup hoping someone would come talk to me and introduced me to Isabelle Muerte and Rain. Together all of these people became my first “Rust Friends” and meeting them was the first time it really felt like I was part of the Rust community.

I ended up tracking Manish down online and sent him a message before the meetup was even over that I absolutely wanted to get more involved and that I wanted him to be my mentor, and he’s still my Rust mentor and close friend to this day.

Thanks to Manish's support I ended up getting further involved in my contributions to clippy and ended up adding support for `cargo clippy --fix` and eventually joined the clippy team as a full team member, marking the official start of my membership of the rust project.

The key take away here is that meetup and conferences like this are an amazing way to get involved and to connect with other members of the rust community. There are so many amazing rustaceans that go to all these meetups and conferences, so they’re a great opportunity to meet and learn from amazing people. The friends you make at events like this will help you achieve far more than you could ever do going alone, and may make all the difference between staying involved or giving up.

## Going Deeper | Error Handling Project Group

Now, clippy wasn't the only rust I was writing during this period. A majority of my rust coding was spent writing CLI programs to help me with my day job (hypervisor storage virtualization written in c++). This is where ended up became particularly passionate about error handling and improving it in Rust, and this work eventually inspired me to establish the error handling project group under the library team.

This project group was my first experience in a leadership role, and I view it as something of a mixed bag. Initially I had quite a few contributors show up to the meetings who were interested in participating. The initiative also seemed to lend me a new sense of credibility within the project and I think substantially reduced how often I had to deal with the assumption of incompetence as a woman working within the project. But fairly quickly participation wained and the project group became essentially just a zulip stream (chat channel) and github repo for organizing discussions and tracking issues. 

I attribute the decline of the error handling project group partially to my own inexperience in leadership roles, I worry I may have centered my own vision for what I wanted to see in error handling too much, and stiffled other contributors who wanted to bring their own ideas to the project group. I wonder if people would have stayed involved had I focused more on making space for other peoples contributions and less on centering mine, but I don't really know, all I know is my subsequent attempts have been more successful, with eyre and the governance working group. 

Long term, the project group, though no longer actively meeting, did help me complete the move of the error trait into core. Project groups are one of those ways teams can encourage work and nudge the direction of the project, and the existence of the group and my involvement with it kept me engaged. Changes like removing the backtrace fn and adding the provider API as a replacement were made easier by the official support for the work that the library team gave me.

My position as lead of the error handling project group also lead fairly directly to my further involvement within the project. 

## My Burnout in the Rust Project

Before I move onto more of my story I want to take a moment to talk about the main focus of the next section of the talk, which will be focusing on burnout. Working in the Rust Project burned me out, and I'm definitely not the only one who has burned out in the Rust Project. 

Preventing burnout is far easier than recovering from it.  It's critically important that you're aware of this issue. Burnout is a painfully common story across the Rust project and open source in general. You need to be able to protect yourself from burnout and be an advocate for yourself and the people you work most closely with. Our current leadership structures in the rust project are not effective at preventing burnout in our contributors, and in fact our leadership roles are often the ones most prone to burnout.

### What do I mean by burnout?

By the time I recognized I was burned out, I found myself dreading many of the responsibilities I'd taken on. Over a couple of years, I went from excited to exhausted. I'd feel tired instantly just thinking about work and push through it repeatedly. Every time I drove through that feeling and had a negative experience, the feeling of emotional exhaustion would get stronger until, eventually, I couldn't push through it anymore. I think of it as my brain building a wall brick by brick to protect me from my work until, eventually, the wall was too tall to climb. I had to learn to say no to the work I didn't enjoy, when possible, and instead focus on the work that interested and energized me.

There's a book called "Burnout: The Secret to Unlocking the Stress Cycle" by Emily Nagoski and Amelia Nagoski that I highly recommend reading[^1][^6] on this topic. In it, they define burnout as follows:

1. *emotional exhaustion* - the fatigue that comes from caring too much, for too long;
2. *depersonalization* - the depletion of empathy, caring, and compassion; and
3. *decreased sense of accomplishment* - an unconquerable sense of futility: feeling that nothing you do makes any difference.

### How did this happen?

No single event caused my burnout; there is no single issue that I can confidently say, "If you took this away, I wouldn't have burned out." Instead, the buildup of frustrations and negative experiences eventually exceeded my emotional capacity to tolerate them. My progression into burnout looked approximately like this:

* Frustration with RFC and PR review processes, prolonged reviews
* Taking on additional responsibilities, not because I wanted to or enjoyed the work, but because I felt it was necessary, that someone had to do it, and that I was capable
* Conflict between the project and the foundation
* Conflict with a member of the library team
* Being overwhelmed by the context-switching demands of maintenance and team membership
* Feeling disconnected from the work, dwindling motivation
* Social isolation

## Foundation + Libs + Governance Working Group

Almost one year after opening the RFC, Futurewei Technologies hired me to contribute to Rust full-time, paying me for work I'd already been doing as a volunteer. Turning the project into my day job enabled me to engage in the project more deeply than I had before. I clearly remember being frustrated with slow reviews on my work, and I'd previously declined an invite to the library team due to lack of time. Now I had plenty of time, so I asked to join, hoping that by joining the team and participating in reviews, I'd lighten the load and get faster reviews on my contributions. Unfortunately, my strategy was not effective.

A few months before joining the library team, I accepted the role of Project Director of Collaboration, representing the project on the board of the Rust Foundation. This change quickly catapulted me from a part-time contributor to a reasonably central leadership role within the project. During my three-year tenure as a project director, there were a series of conflicts between the foundation and the project. As Project Director of Collaboration, I felt it was my responsibility to prevent these conflicts and that I was failing, which ate away at me emotionally.

While on the library team, I had a series of conflicts with another team member. We'd frequently end up frustrated and confused by each other. We brought this conflict to the restorative justice mediator (Andy Izenson), whom the foundation keeps on retainer to help project members navigate conflicts like this. He was incredibly insightful and helped me learn valuable communication skills and repair trust with my teammate. Unfortunately, however, we also concluded that we could not work together due to a fundamental difference in our collaboration methods.

Participating in the library review queue and meetings pushed my ability to context switch to the absolute limit. I'd jump around looking at dozens of PRs and issues in a day, reviewing massive comment threads and chains of issues to put together a picture of the current state of any contribution so that I could understand and address blockers, voice concerns, etc. I didn't appreciate how exhausting this would be for me in particular. The [flavor of the burnout](https://en.wikipedia.org/wiki/Autistic_burnout) this particular pressure gave me ended up leading me to recognize that I'm not ADHD but autistic as well and that I have a high affinity [monotropic focus](https://en.wikipedia.org/wiki/Monotropism). It takes me far less energy to focus intensely on one topic for an extended period than to jump between many different topics. I'm still figuring out how to meet that need while participating in a maintenance, review, or team membership role.

Another factor in this is the state of my motivation to work on the Rust Project at all. I got into using Rust because it solved problems I dealt with daily in C++. I got into the project to become a better Rust programmer. Problems I encountered in personal projects, those little CLI tools written to assist me with my day job, served as the inspiration for many of my early contributions. For my first few years at Futurewei, however, I exclusively worked on the Rust Project, and I quickly lost interest in doing Rust in my free time; that was starting to feel like work. Maintenance and governance work quickly ate up almost all of my time, and my personal contribution rate dwindled to nearly nothing. This lack of personal frustration with the language created a disconnect that widened over time. My motivation to fix issues within the language dwindled as the time since I was last bitten by them grew. It became more challenging for me to motivate myself to support those actively doing that work. Luckily, Futurewei has been extremely accommodating. I was able to take a break from Rust project work to work on contributing to other projects[^2] they were funding, which has helped me reconnect with my love of writing Rust and, maybe even more importantly, my frustrations with the language and library and my motivation to address those issues.

Social isolation also contributed to my feelings of stress and burnout. For me, it seems that a few hours of video calls a week are not a sufficient substitute for in-person socializing. I miss feeling surrounded by people energized about the project we're working on[^5]. I miss being able to take a break when I'm stuck or overstimulated, walking over to a coworker's desk and asking if they could take a break and want to play pool or grab some food. This relates to my ADHD, specifically my need for Body Doubling. There are aspects of remote work that I'm very hesitant to give up, and I can't be sure how much of this is rosy retrospection, but I can't help but believe that the last time I felt genuinely healthy was pre-pandemic lockdown. My mental health went downhill fast when I switched to remote work, and I'm not sure I've ever fully recovered. I'm planning on moving closer to the office to help address this.

Now, not everything in this period of time was a source of stress. I also participated in the governance working group which drafted the governance RFC establishing the leadership council, the successor to the core team after its implosion. This group was easily the most effective working group I've participated in. Through this work I got introduced to and fell inlove with sociocracy and it's model of dynamic self governance and it's similarities to the existing governance model of the rust project. We applied many of these principles to our meetings, including establishing process roles such as a secretary and meeting facilitator (this role is easily my favorite personally). I've applied this to the governance of some of my own personal open source projects like the `eyre` library which now has a lively team that is still holding meetings without me while I prepare for this conference. During this period I often got disillusioned that there was nothing I could do to improve the governance dysfunction and conflict I was seeing throughout the project, but the reality is that we have made major improvements, and I've grown a great deal in the process.

## Taking a Break

Ultimately, I ended up taking a break from the Rust project. I realized that I needed a change of environment and to reconstruct my relationship with the rust project in order to get back to a place where the work was sustainable and enriching for me.  I stepped down from all the teams I was in, and I did not run for re-election to the board of the foundation. I shifted my focus to recovery, and for that I leaned upon the support of my good friend Sophia Turner, who was contracting with Futurewei at the time working on some experimental programming languages. 

The first language we worked on together was Truffle. Truffle is an experimental embeddable scripting language for Rust that offers high performance, ease of embedding, lightweight requirements (no dependencies by default and low compile times), and a familiar syntax (a subset of the Rust syntax).

After a few months we moved onto working on June-lang, which is an experimental safe systems language in the vein of rust focused principally on Teachability, learnability, and readability, which it achieved by experimenting with a novel lifetime model.

By working on these projects I was able to get some distance from some of the negative feelings I'd built up around my work in the rust project and start to properly heal. I got back to what I love, actually writing rust code. I got to work with a close friend. Many of our brainstorming sessions would have a casual feeling that helped me feel less socially isolated. It was in this time that I started to feel like myself again. I reconnected with the reasons I got involved in the rust project in the first place. I got to utilize a lot of the collaborative skills I'd built up over the last few years of occupying leadership positions within the project and working on establishing the new leadership council.

## Compiler Contributions

Eventually, that contract ended and I moved back to working on the rust project, but now with my feet under me and a better understanding of what kind of environment I needed to be healthy and successful. I'm now engaging with the rust project very differently. I'm much more hesitant to take on responsibilities, particularly those I now know I'm not well suited for, at least in their current state (such as leadership roles). I'm trying to stick to fewer projects and focus intensely on them. For me, that ideal number of projects to focus on seems to be one.

Once I came back I had a bit of a rough patch in the gap between stopping working on June and properly figuring out what I wanted to focus on in the rust project. I knew I wanted to focus on actually writing code, rather than the more social and context-switching heavy work of unblocking discussions and reviewing proposals. I also knew I wanted to work closely with someone I have a good relationship with, like I had with Sophia, so I wouldn't feel as isolated as I had before, and so I could bypass some of the frustrations I've had in the past with slow review queues. I ended up talking to my friend Esteban about my problem and he suggested I work with him on the metrics initiative, an idea he's been nursing for years. He'd recently written a blog post on the topic and submitted a Major Change Proposal to begin the initial work which was approved by the compiler team.


## Example: Metrics Initiative

So that's where I am today. I decided I wanted to work on the metrics initiative. But how do I go about that? What is my strategy for taking on a new project as a battle hardened rust contributor? Particularly a project like the metrics initiative which is easily misunderstood. When people hear Metrics initiative they often hear "telemetry" and it conjurs images of TCP connections and personal information flying away from their machine without their knowledge. The last thing I want to deal with a steady stream of users worried about this nightmare scenario.

Well, first I get organized, I establish and clearly communicate the foundation for the project, and in this case that means the motivation and guiding aims, all organized within a tracking issue.

Here's the motivation section for the metrics initiative:

* Supporting feature development, e.g. answering specific questions such as when the old and new trait solvers diverge, or helping identify and resolve bugs.
* Guiding improvements to User Experience, e.g. knowing which compiler errors are causing the most confusion or are hit the most frequently, focusing on improving those first, and verifying that the improvements help.
* Improving perf feedback loops and insight, e.g. helping identify pathological edge cases, similar to work @nnethercote has done manually in the past

Next, we define our core aims: 

Our top aim is Trust. We do not violate the trust of our users. This means a few things. It means no telemetry, no network connections. It means we emit metrics locally. It means that user information should never leave their machine in an automated manner. Sharing their metrics should always be opt in, clear, and manual. And it means we have a minimal retention policy to avoid wasting disk space.

Our next aim is Feedback. We want to provide additional feedback loops to assist in the iterative improvement of the project. This means answering questions about the compiler from real world production environments in a privacy preserving way. Improving the legibility of rare or intermittent issues, or gaining earlier warnings of internal compiler errors users encounter on nightly, so we can catch more issues before they land on stable.

Third, we're focused on performance impact. We seek to leave no trace. Enabling metrics should not result in a noticable impact on compilation times. This is particularly important for any metrics that we may wish to enable by default in the future.

Next, we want the metrics to be extensible. It should be easy to add new metrics when necessary, though we should only be adding metrics with specific questions in mind, with well documented rationals.

Finally, we aim to improve user experience. This means both the user experience of using the compiler and language and the experience of reporting issues. 

The aims are important because they are the foundation on which we want people to base any objections upon. If any part of the metrics initiative is working against our professed aims we want to know about it so we can fix the problem. The aims are organized in order of priority, whenever aims come into conflict the higher priority aim wins. For example we wouldn't want to accept a change that has the benefit of improving user experience if it does so in a way that endangers privacy and trust.

Then finally, we pick an example usecase to start building the whole project around. For this I ended up talking to a bunch of other project members to ask them what they would end up wanting to use metrics for, then I just picked the idea that I was most excited about. In this case it ended up being Amaneiu's suggestion that we make it possible to track unstable feature usage statistics. This will then help teams like libs / lang / and compiler understand which features are the most in demand and prioritize their stabilization efforts accordingly, it will help them understand which unstable features are unused and may not be worth the maintenance burden and remove those accordingly. It will help people see adoption curves of the features they contribute to be able to better understand and feel the impact of their work. This idea felt like a slam dunk to me that wouldn't be difficult to advocate for, so I expect getting consent on our proposals to be a relatively easy and painless process. 

And this is where I am now. I've finished the basic implementation of dumping feature usage and feature status information, which are currently open as draft PRs. I took a break from that work to prepare this talk and come speak here, and when I get back ill finish and merge those then move onto the next step of building a backend to store that information and then work to make it easy for users to send us those metrics, starting with gathering that information ourselves for all the code that is publicly available on crates.io.

  
## Key Takeaways

So that's the talk, before I end and move onto questions I want to just loop it all back with the key takeaways I hope you get from this talk:

* do not lose sight of why you contribute to the rust project. That motivation is the fuel for your work, without it you will burn yourself up instead.
* Remember how the project works, you won't have a manager to assign and approve your work, keep you accountable, or plan how to complete your work. You'll need to be able to do this yourself, or ideally, with your friends.
* And on that note, do not work alone. Make friends, find a mentor, and eventually be a mentor. Working in the project can be stressful, having people to talk to to help navigate these situations or even just blow off steam can make all the difference between pushing through and landing a change and giving up leaving work incomplete to gather dust. The project is full of some of the most brilliant people I've ever met, and despite my frustrations is also one of the greatest communities I've ever joined and I do not regret it for a second.


Thank you

Fin

[^1]: repeatedly
[^2]: June programming language: https://www.youtube.com/watch?v=c1isq1Bdmic
[^3]: I thiiiink its from Ted, but I couldn't find the source so I'm not sure.
[^4]: Particularly organizing the stories people share so we don't lose them and I can find them all in one place despite not being particularly online these days.
[^5]: And by that I mean body doubling, this might be specific to ADHD people: https://www.medicalnewstoday.com/articles/body-doubling-adhd
[^6]: I started a reread to prepare for this blog post and rediscovered this quote from the intro: "We thrive when we have a positive goal to move *toward*, not just a negative state we're trying to move away from."... I might have to reframe my burnout goal :sweat_smile: 
