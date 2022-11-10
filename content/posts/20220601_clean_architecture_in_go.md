---
title: "Clean Architecture in Go"
date: 2022-06-01T16:23:51-07:00
draft: true
---

I've been using Go for five years now in both professional and personal 
projects. This will be the second post of many where I look back at that 
experience and analyze a particular part of the language, as well as share some 
common patterns and lessons I've learned along the way.

If you want to see any of the previous posts, check them out here:
- [Error Handling in Go](../error-handling-in-go)

---

Clean Architecture[^1], Hexagonal Architecture (aka Ports and Adapters)[^2] -- 
there are many names for the same goal: software that can stand the test of 
time.

The overall goal I see when implementing clean architecture is this:

- I want to write code around our business rules
- I don't want to change that code if our technology changes

Now, this isn't an unbreakable rule, but a very wise guideline. Whether you use
gRPC or REST, or store your data in NoSQL or RDBMS, you should be able to keep
the code about your business rules the same.

I don't want to spend too much time on Clean Architecture here as there's many
an article that covers the topic, as well as Go implementations. What I think is 
more interesting are some of the lessons I've learned working with this 
architecture.

## TL;DR

There are three things I've learned from using this that I'd want as takeaways
from this post:

1. This architecture doesn't slow me down
2. You get used to the _amount_ of code you write
3. Use the 80/20 rule to save you time and reduce code-copy

### This architecture doesn't slow me down

Both working at a startup and working on home projects, you tend to move fast. 
If you take down your home's light system, or you need to pivot an idea based 
off of customer feedback, you want to do it with agility. Using Go and this type
of architecture, I was hesitant at first to use it where I didn't need the 
longevity I usually look for before bringing it in.

What I _actually_ learned once I embraced it was it actually made things 
_easier_ to pivot over time. The architecture keeps technical and business
concerns isolated. Jumping between layers becomes straightforward because of the
interfaces you put between them. I could easly swap out Twirp for gRPC based off
of the needs of other services it needed to work with.

I wouldn't say I gained a net speedup from the Rails APIs I was working with
before, however, I did receive the benefits of performance and resiliency in 
addition to what I built before.

If you could move at the same speed but gain additional benefits, wouldn't you?

### You get used to the _amount_ of code you write

There's been a couple things in Go that you have to get used to if you're going
to stick with it and write code with a clean architecture:

1. You will be writing the same code multiple times
2. You will be working with generated code

Here lies the realization: both of these things increase the lines of code (LoC)
of your program.

However, do not fear! Some might equate lines of code to an increase in 
complexity, but its usually the opposite -- with Go, the code is usually very
easy to reason about.

With Go 1.18 and the introduction of Generics into the Go programming language,
there's a great chance to reduce both of these points, and the LoC you need to
write to accomplish your goal. However, this will not erase the fact that you
will still need to perform the two points above if you're working on anything
that needs to run for years, or requires cooperation with other APIs, services,
or functions.

#### 1. You will always be writing the same code multiple times

Let's start off with a quick example, prevalent in the previous post:

```go
result, err := DoSomethingThatCanFail()
if err != nil {
    // handle the error
}

// handle the result
```

Anytime you see a function returning an error, you need to check it. You'll be
using this pattern a lot! Once you get used to it though, you get used to
throwing a `fmt.Errorf("addtional details about error: %w", err)` in your code
without missing a beat.

With domain objects, DAOs, DTOs, etc., you're bound to write a struct that can
materialize into JSON, SQL, and more. Don't break into the habit of writing a 
single struct that can do it all though!

Usually, you'll have different columns and data types at play at these different
layers. 

For example, storing something between RDBMS and NoSQL might be completely 
different structures, and you'll want to rely on being able to have your 
business domain objects not be changed or cluttered with `sql:""` tags as well.



[^1]: [Uncle Bob's Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
[^2]: [Hexagonal Architecture](https://en.wikipedia.org/wiki/Hexagonal_architecture_(software))