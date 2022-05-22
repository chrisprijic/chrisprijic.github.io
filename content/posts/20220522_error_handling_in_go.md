---
title: "Error Handling in Go"
date: 2022-05-22T02:14:54-07:00
draft: false
---

I've been using Go for five years now in both professional and personal 
projects. This will be the first post of many where I look back at that 
experience and analyze a particular part of the language, as well as share some 
common patterns and lessons I've learned along the way.

---

In Go, errors are _values_. This means they can be returned, operated on, and 
passed around like any other value. Errors as values provides some great 
benefits:

* explicit control flow throughout your application
* the ability to enrich errors with additional, human-readable information
* there's no question if a method can error out or not

They are a core language type that only have one requirement. TO be an error, 
you must satisfy the following interface:

```go
type error interface {
    Error() string
}
```

You have a couple of ways to easily create errors:

```go
err = errors.New("this is a string --> error")
err = fmt.Errorf("this does string formatting into an error: %v", err)
```

There's also error wrapping, introduced in Go 1.13[^2]. This lets you add 
additional context while also letting callers detect lower-level error types 
easily and effectively.

### How I handle errors in Go

Let's check out a practical (though simplified) example of copying a file 
(lifted and modified from Russ Cox's Go 2 proposal[^3]):

```go
func CopyFile(src, dst string) error {
	r, err := os.Open(src)
	if err != nil {
		return fmt.Errorf("error opening src file %s: %w", src, err)
	}
	defer r.Close()

	w, err := os.Create(dst)
	if err != nil {
		return fmt.Errorf("error creating dst file %s: %w", dst, err)
	}

	if _, err := io.Copy(w, r); err != nil {
		w.Close()
		os.Remove(dst)
		return fmt.Errorf(
            "error copying file contents from %s to %s: %w", src, dst, err)
	}

	if err := w.Close(); err != nil {
		os.Remove(dst)
		return fmt.Errorf("error closing dst file %s: %w", dst, err)
	}

	return nil
}
```
Here I modified the original code example with two changes:

* wrapping errors in `fmt.Errorf` using `%w`
* modifying the error messages to include context about _what_ failed to happen

If you're hacking or scripting, you can just log the error, but still get any
context you might want from the printed error:

```go
err := CopyFile(src, dst)
if err != nil {
    log.Fatalf("Error: %v", err)
}
```

Here's what a possible error prints out:

```bash
error opening src file file.txt: open file.txt: no such file or directory
```

Not only does it log that it failed to find such a file or directory, but also 
that it is the _source_ file that couldn't be opened. What if you don't know if
the file was the source or destination file? The additional context added to the
error message clears that up for you.

Alternatively, in automated or production use-cases, you can handle specific 
error types automatically with a little extra code:

```go
err := CopyFile("source.txt", "dest.txt")
if err != nil {
    switch {
    case errors.Is(err, os.ErrNotExist):
        // handle missing file error directly
    // ... other cases
    }
}
```

### Re: All the discourse around errors

Go error handling is a somewhat devisive part of the language. You can easily 
discover a variety of material online already that speak to the stark contrast 
of those that like and dislike this feature of the language[^1].

Many developers have issues with Go's error handling patterns. Common complaints
include the following (non-exhaustive) list:

* _writing too much code for error handling_: To this, I say that we all spend 
  more time reading and understanding code, not writing it.
* _you keep copy/pasting the pattern `if err != nil {return err}`_ everywhere: 
  To this, I find explicit code flow to be easier to reason about and understand
  than it automatically returning these errors instead.

It might seem daunting to have a bunch of error checking and mapping throughout 
your code; however, in practice I've seen the opposite effect. What little 
amount of additional code you have to write, you make up in the clarity of your 
code's flow control, while also reducing your cognitive load on keeping track of 
what the code is doing.

Let's look at Rust, which supports `Result<T, E>`, the `?` operator, and so on.
Though this still denotes areas of your code that return errors, it still adds
additional cognitive load to understand and model these concepts in your head, 
rather than letting the code explicitly and plainly tell you what it is doing.

Go2's design proposal[^4] for error handling is interesting, but I still digress 
that it focuses on reducing the effort tied to writing code, rather than the 
overall cost of reading, understanding, and maintaining code. Go's error 
handling helps you improve the base quality of the code, at the expense of a 
little extra written code to accomplish that, and that's ok.

---

## TL;DR:

Overall, I apply the following guidelines for error handling in Go:

* **always** handle errors. Never use `_`. Use `panic` if it isn't production or 
  mission critical.
* expand if possible on what failed due to the error. Use `fmt.Errorf` with `%w`
* use 1.13 features of `Is`, `As`, and `Unwrap` to support more robust error
  handling use-cases
* focus more on the ability for your code to tell others what it does plainly,
  without ambiguity. Writing a couple more lines, or copying a couple of lines
  won't drastically derail a timeline to complete a project

[^1]: [Go's Error Handling Sucks](https://www.boramalper.org/blog/go-s-error-handling-sucks-a-quantitative-analysis/),
      [Why Go's Error Handling is Awesome](https://rauljordan.com/2020/07/06/why-go-error-handling-is-awesome.html),
      [Go is a terrible language](https://debugged.it/blog/go-is-terrible/)
[^2]: [Go 1.13 Release Notes (Error Wrapping)](https://go.dev/doc/go1.13#error_wrapping)
[^3]: [Go 2 Drafts Announcement](https://www.youtube.com/watch?v=6wIP3rO6On8&t=131s)
[^4]: [Error Handling (Go2 Draft)](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md)