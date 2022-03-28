---
title: "My Guidelines on Product Development"
date: 2022-03-28T10:00:00-07:00
draft: false
---
 
_This is by no means a novel approach; however, I wanted to share my 2¢ and
reflect the process._
 
![The different interpretations when a customer requests a feature](/img/product_development_swing.jpg)
<p style="text-align: center;font-size: 1.3rem;"><i>
      Copyright © <a href="www.projectcartoon.com">www.projectcartoon.com</a> 
      using the Creative Commons Attribution 3.0 Unported License
</i></p>

Variations of this image have circulated since the 1960's[^1] around the "Tree 
Swing Picture". When developing software, a common misconception can be thinking 
that what _you_ know mixed with what requirements are given to you are known by 
everyone.
 
A popular parallel to this process is the game of telephone, where by the end of
the chain of people, you can likely get a completely different result from the
first person. Software product development can feel much like that.
 
One thing that can easily make this process smoother is by actively listening
and asking questions. The more information you have at your disposal, the better
you can develop a solution to the problem.
 
## What to listen for
 
When listening to the details of what is being requested, its important to
listen for trigger phrases. These are phrases that should cause you to actively
listen to each word they say right before or after that phrase. Some examples of
this are:
 
- _"Something that keeps me from ..."_
- _"All I really need is ..."_
- _"Once we can ...."_
 
These phrases help you identify *__pain points__* in the current product.
 
## What to do with that information
 
Don't just run away with those pain points just yet! You have heard them _in
their first pass_.
 
This means you have heard it once, and have internalized <u>your</u>
interpretation of what was said. The very next thing you want to do is get on
the same level of understanding with them.
 
Repeat back what you heard in your own words. Listen to how they respond, and
ask for clarification wherever there might be a misalignment in your 
understanding. These clarifications and levels of understanding help solidify 
that you not only heard what the customer wants, but also have a level of 
understanding about it.
 
_Note: This is also a great way to level up your communication when defending a
point or persuading another. Reaching common ground that both sides can agree on
gives you a foundation to step off from._
 
## Is one customer enough?
 
You received feedback from one customer. Sometimes, especially if
they're a cash cow or your primary customer, that could be enough. If you have
some leeway with the deadline on addressing the concern, you'll want to gather
more feedback on similar issues with other customers.
 
The more people that are interested in a solution to the same or similar 
problem(s) can help you prioritize your work. Not just what is easy or hard to 
implement, but also by the impact a change can have on your customer base.
 
## Development is underway!
 
As you develop the solution, there's two principles to instill in the process to
help customers receive what they actually need:
 
1. Continuous Feedback
2. Iterative Development
 
### Continuous Feedback
 
__Always__ include your customers along the journey of development. If they
provided feedback and have a vested interest in the solution being made, then
they likely _want_ to provide feedback throughout the process.
 
I've grown into using three-pass process for this when the problem is not
trivial.
 
#### 1. Prototype around the problem
 
Typically this is where you get your hands dirty with the problem itself. The
understanding in words is brought to your software. If a solution is already in
mind, you could start planning for that, and while you do, you might hit
different roadblocks and try out different ways of approaching the problem.
 
By the end of this prototype, you should have a solid grasp on the problem,
identified solutions that do and don't solve the problem, and be able to start
building the solution.
 
Your roadblocks and your conclusions here are two great areas in which
communication with the customer is important. You don't need to share all the
details, but keeping them up-to-date on your findings as well as making sure the
solution you end up with is in line with their needs.
 
 
#### 2. Build your Tracer Bullet[^2]
 
At this point in the process, we understand the problem conceptually and
technically, and have identified a solution that should work and meets the
customer's needs.
 
Development always then starts with a "Tracer Bullet", where we make an MVP of
the feature that hits all layers of the application -- front-end, back-end, etc.
Once this is completed, it allows you to gather customer feedback iteratively
as you flesh out the feature after this initial end-to-end implementation.
 
---
 
At this point, you've built an MVP for the problem initially discussed! You have
it implemented end-to-end so customers can start using it and providing feedback
as you add additional features and requirements to it.
 
### Iterative Development
 
When developing products and features, it's important to do it iteratively. As
you likely ascertained during the previous section on feedback, iterative
development lets you take small, conscious steps towards an end-goal.
 
Imagine it like walking from one point in a forest to another, without paths or
a map. If you do it iteratively, checking your progress against a compass, the
sun, etc., you'll likely divert a little but reach your goal.
 
On the other hand, if you try to beeline to the goal without checking your
progress, you might find yourself at the end of the day way off course, on the
other side of the forest _further_ from your goal!
 
#### Stable Ground
 
In college I had a professor in my Intro courses (shoutout Prof. Thorton!) who
always talked about reaching "stable ground". Each time your program compiles
successfully and passes all tests, you commit your code. It was in each
assignment in bold letters, usually accompanied by a first-step that could be
taken when starting the assignment.
 
This advice was extremely effective in outlining a process that all software
developers should use, but also in outlining what an effective "step" could be
when solving problems in software development.
 
With product development, you want the same thing. After launching a new
incremental feature, you should gather feedback and _listen_. This practice will
save you a lot of time in the long run when you are building solutions to your
customers' problems that actually solve the problem for them. You can use each
iteration as a way to check your progress to your goal.
 
Listening to your customers directly is the best type of feedback you can
receive. However, you can also look at metrics -- product adoption, obstacles 
that keep users from adopting the new feature, and so on. Use the tools at your 
disposal to listen as much as you can to your customers' signals!
 
## Product Development is a Journey
 
Product development can be a daunting process, especially when starting a new
product or finding those customers initially that can give you effective
feedback. If you take small steps, develop end-to-end as quickly as possible,
and continuously iterate on that feedback, you're bound to build products that
your customers want and keep coming back to use!
 
[^1]: _From [The "Project Cartoon" Root Cause](https://medium.com/@thx2001r/the-project-cartoon-root-cause-5e82e404ec8a)'s
      article on the same cartoon. (Historical Link: https://archive.ph/FgxPK)

[^2]: _Often these implementations are a 'quick shot' through all layers of an
      application, such as connecting a single form's input field to the
      back-end, to prove the layers connect as expected._
      ([Wikipedia](https://en.wikipedia.org/wiki/Scrum_(software_development)))