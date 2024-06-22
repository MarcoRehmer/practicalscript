---
title: "messure execution time in Go"
date: 2024-06-22T13:00:00Z
aliases: ["/meassure-execution-time-in-go"]
tags: ["Go", "Metrics"]
author: "Marco Rehmer"
draft: false
summary: Find out how long your functions are running at execution time in Go
cover:
  image: "images/golang-logo.png"
  alt: "Golang Logo"
---

You have problems to figrue out how long your functions in go are running? Or just want to see how much time a query took?

Me too, I swear! But fear not! I will show you how to solve this in a very compact and clean way.

> 💡 At the end of this post you can find this guide on YouTube ✌️

## TL;DR

For all you lazy guys out there, here is the code snippet which you can use to track your execution Time:

```go
    func main() {
      someHeavyCalc(2)
      fmt.Println("done.")
    }

    func timer(name string) func() {
      start := time.Now()
      return func() {
          fmt.Printf("[%s]: %s\n", name, time.Since(start))
      }
    }

    func someHeavyCalc(duration time.Duration) {
      defer timer("someHeavyCalc")()
      time.Sleep(time.Second * duration)
    }
```

## Timer Function

Our timer function will take a name as an argument, so we can see which context is printed. Next, we will grab the starting point with `time.Now()` to get the current timestamp.

```go
    func timer(name string) func() {
      start := time.Now()
      // ...
    }
```

At least our function will return a **new** anonymous function, which will print out the time our process took. To see how this will work, just read further ;-)

```go
    func timer(name string) func() {
      start := time.Now()
        return func() {
          fmt.Printf("[%s]: %s\n", name, time.Since(start))
        }
      }
```

## Measure the time

Okay, now we can use our function to track one of our functions.

Just use the timer function at the beginning of one of your functions (or whereever you want to **start** your measurement) like this:

```go
defer timer("myFunctionName")()
```

You can notice two little things here

1. after we have done our regular function call, we attach another pair of bracelets `()` to call **the returned anonymous function**
2. we use the `defer` keyword in front, to _delay_ the call of our anonymous function at the end of the parent function

With `defer` go will schedule the given function at the end of the wrapping function, and will execute them after go have reached the last line of the parent. That means, that the `fmt.Printf(...)` will be executed **after** everything is finished and can print out the correct timespan.

Easy, isn't it?

## Watch this Guide on YouTube

{{ youtube ThzXpXDJS2M }}
