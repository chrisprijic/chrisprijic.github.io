---
title: "Boring Software is a Virtue"
date: 2022-04-19T05:00:00-07:00
draft: false
---
 
This past month, I've had the chance to dive back in to pair-programming and 
code reviews at a higher frequency than usual. This has given me the chance to 
reflect on my software development process and how it's changed over time. This 
post covers some of the insights discovered during that reflection, and why much
of the software I write today leans towards "boring".
 
## Granularity
 
I don't think I can word this much better than Casey Muratori did in his own
article on granularity and complexity[^1]:
 
> "This is so easy to use, and yet it never gets in my way when I need to do
> something tricky!"
 
Ever since I read this post back in college, I've found myself trying to provide
granular layers to software I build. This means that as we add new features,
we can (and often do) support the feature at a few different levels of
granularity where it fits in well; other times we support the feature directly
in its specific areas when it doesn't generalize to other levels of convenience.
 
Imagine an ORM that doesn't let you also run raw SQL directly when you need to,
or a car that doesn't let you control the speed of your vehicle except through
your cruise-control interface. These systems grant additional layers of control
that interface with each other in ways that let those "convenient" solutions get
out of your way when you need to do something more specific or complex.
 
_TL;DR: support multiple layers of granularity in your software, so that you can
help developers at multiple levels over an opt-in/out status._
 
## Libraries and Abstractions
 
In the same way, libraries and abstractions are an interesting topic. SOLID and
DRY are very often prevalent in software development circles, but they also have
downsides.
 
As you try to write software to support a variety of use-cases, there's a good
chance that not _every_ use case or feature should be supported. I've leaned
towards two specific rules in the past few years I try to adhere to:
 
- Libraries, not Frameworks
- The smaller and more specific a library is, the better
 
Let's break those down.
 
### Libraries, not Frameworks
 
This one is pretty easy for me. Frameworks typically force you to adhere to
conventions over configuration. The unfortunate side-effect of this is that they
then typically _get in your way_ when you need to do something different than
how they'd like you to accomplish that. Learning how to efficiently tune a rails
API is not exactly the greatest experience I've had. It's gotten much easier
over the years, but frameworks still force you to bend to _their_ will.
 
Libraries, on the other hand, are entirely optional. Don't want to use it? Then
don't! Want to use just a part of it and not the whole thing? Go right ahead!
Many times, I've found myself supporting my team with features in libraries, so
they can feel free to build their products and services how they choose.
 
_Disclaimer: This doesn't mean it's the Wild West on the team! It just means 
that people aren't forced to adhere to a specific, restrictive way of developing
software. Each team and service has their own set of criteria and can build as
fits best for their goals._
 
### The smaller and more specific, the better
 
We have a UUID library we use internally. Outside of it largely being a wrapper
of an open-source library, it only contains a couple extra helper functions
we've seen written in _every service we have_.
 
For example, the following:
 
```go
func PUuid2PStr(input *uuid.UUID) (*string) {
    if input == nil {
        return nil
    }
 
    s := input.String()
    return &s
}
```
 
Since we translate UUIDs to Strings and vice-versa at different layers of our
services' stacks, adding these functions helped considerably. These are also
technical concerns and not business ones[^2], so sharing them across services is
likely fine. If a service has other rules in mind, they can easily just not use
this function and make one of their own.
 
Since its a good goal to keep the dependencies low on the software you
write[^3], I also find it important that the dependencies are small and specific
to make the process of keeping them up-to-date as simple as possible as well.
 
_TL;DR: small composable libraries reduce the drawbacks of using those
dependencies in other software._
 
# Comments and Reviews
 
The more code reviews I do, and the more software I maintain, I've found myself
leaning on adding any comments I make in a PR or review to the code itself.
 
Comments are a may to more easily come back to code that can speak to what the
code doesn't already tell you. For example, I needed to install docker-compose
inside an already dockerized environment. I could have done this:
 
```bash
# install dependencies
apk add --no-cache bash curl git python3-dev py-pip libffi-dev openssl-dev \
    gcc libc-dev rust cargo make openssh
```
 
However, coming back to this CI pipeline script later, I might wonder why we
need all of these libraries installed in the first place. Removing/changing them
only to realize they're all needed. But *_why_*?
 
What I did instead, is cover what the code doesn't already tell you:
 
```bash
# Re: https://docs.docker.com/compose/install/
# under "Alternative install options" there is an "Install as a container"
# section that did not work on Bitbucket Pipelines.
# They do list the alpine packages we need, however; so I set that up here.
#
# added git and openssh for use by Golang
apk add --no-cache bash curl git python3-dev py-pip libffi-dev openssl-dev \
    gcc libc-dev rust cargo make openssh
```
 
This points them to:
 
- Where I discovered what I need to install
- What _didn't_ work if they tried this from scratch themselves
- What additional packages I've added to support other use-cases
- Any additional comments I have on the process of getting here
 
This is all in hopes that this makes the life easier for any developer that
needs to read/modify/remove this script for any reason.
 
Most of the time, software development is iterative. If you did iterate and know
what has worked and what has not, documenting that in the code could help future
developers (most likely yourself too!) from questioning or re-creating those
same mistakes. Leaving these in the code (rather than a PR or separate document)
makes it easier to find when working on that same code.
 
_TL;DR: focus on the *why* over the *what* and *how*._
 
# Performance
 
Lastly I wanted to touch on performance briefly. Performance _can_ contradict
everything else in this post; however, I do want to mention that these ideas can
instead be used to _support_ performance constraints instead.
 
_Use comments to explain what performance-constrained code does_. This is one
area where explaining what is happening with a comment can be useful, because
the code is no longer written for readability.
 
_Granularity can be used so your performance-centric code is out-of-the-way of
the more convenient areas of code._ If libraries and other parts of your
software can get out of your way when you need to dive down into performance,
then you can easily do so.
 
_Don't move performance-specific code into an abstraction or library_. More
often than not, performance tweaks will diverge based off of different
scenarios, and likely without much forbearing ahead of time.
 
_TL;DR: You can still program for performance and adhere to these guidelines;
they are guidelines after-all, and not rules._
 
## Boring Software is a Virtue
 
So there it is! The most recent project I've worked on, I've been applying these
principles. I've noticed that the code comes out boring! Why is that?
 
Well, the fun is in the process. When things _just work_, and the process is
_just smooth_, and the details are _documented and figured out_, then the
software is more straightforward and easier to understand.
 
We've isolated terrible APIs from our business logic. We've broken down a
complex business requirement into a well-documented process that a fresh hire
was able to add new features to it in his first week. most of our code boils 
down to checks (if/switch) and loops (for/while) to accomplish a vast majority 
of its tasks.
 
You don't _have_ to use that "Next Framework". I highly suggest trying these out
in your next project.
 
[^1]: [Complexity and Granularity](https://caseymuratori.com/blog_0016),
      Part 2 in a series of articles on Casey Muratori's approach to software
      development.
 
[^2]: [Don't Share Libraries among Microservices](https://phauer.com/2016/dont-share-libraries-among-microservices/)
 
[^3]: Check out [this](https://snyk.io/blog/peacenotwar-malicious-npm-node-ipc-package-vulnerability/)
      and [this](https://snyk.io/blog/open-source-npm-packages-colors-faker/);
      both vulnerabilities occured _just this year_ in very common packages on
      NPM.

