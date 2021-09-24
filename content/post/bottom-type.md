---
title: "Bottom Type and The Halting Problem"
date: 2019-12-16T20:50:45-05:00
draft: false
description: "A look into the concept of a bottom type and related Halting Problem."
tags: ["type theory", "data-types", "bottom-type", "halting-problem"]
---

In my learnings on Category Theory, I've come across a concept that I have not
heard of before but have been using almost daily. It is the concept of the
**bottom type**.

Given a mathematical function, for example:

$$
f(x) = 2x + 3
$$

This function maps an integer to an integer. Some examples of this function in action are

$$
f(2) = 2(2) + 3 = 7
$$
$$
f(5) = 2(5) + 3 = 13
$$
$$
f(6) = 2(6) + 3 = 15
$$

There is no concept of an execution environment so there is no case for when this function may not return a value, in effect, never completing execution. We can say that this mathematical function is deterministic -- it will always, given an integer, provide an integer as its output.

But can the same be said for a function in a computer program?

If we translate the same mathematical function into a function in a computer program, it may look like something like this:

```haskell
f :: Int -> Int
f x = 2 * x + 3
```

In this example, the function maps an integer type to an integer type, as per Haskell's type signature on the first line. Some examples of this function's invocation are:

{{<highlight bash>}}
Prelude> f 2
7
Prelude> f 4
11
Prelude> f 5
13
{{</highlight>}}

In a computer program however, there are some dimensions that have to be taken into account. What if the function, given some input, fails to compute in finite time? In other words, can we really be sure a function will always execute successfully and provide the type we are expecting?

If a function loops forever, or if it throws an exception, or a runtime error occurs in a general sense, a value is never returned, but what is?

One of the problems described above is known as the $Halting Problem$: given a computer program and some input, will it terminate in a finite amount of time or run forever? This statement can only be proven undecidable by contradiction.

Programming languages have come up with a solution (or a glorious hack) to The Halting Problem in the form of a type that extends a special value called `bottom` expressed as a `falsum` (⊥), a concept from propositional logic.

`Bottom` type allows languages to define a sink for a function that can potentially never return or throw a runtime error. Basically, anything that prevents it from returning a value of the type in its signature.

Some languages allow the `bottom` type to be returned explicitly.

The example in Haskell:

{{<highlight haskell>}}
f :: Int -> Int
f x = 2 * x + 3
{{</highlight>}}

can also be defined as:

{{<highlight haskell>}}
f x = undefined
{{</highlight>}}

In Javascript, it may look familiar as an internal `bottom` type of `undefined`:

{{<highlight haskell>}}
const f = x => undefined
{{</highlight>}}

In Kotlin, the `Nothing` type is the `bottom`. It signifies that a function never returns, in effect, only throws an exception. This type has to be explicit, which puts the onus on the writer to explicitly state the function does not map one type to another.

{{<highlight kotlin>}}
fun f(x: Int): Nothing {
  throw IllegalStateException()
}
{{</highlight>}}

`Bottom` type is a fascinating concept that does not exist in the mathematical world of functions, but is part of the typing system of most
modern languages to account for dimensions that computational systems introduce.
