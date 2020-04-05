---
title: "What's a Functor?"
date: 2020-04-05T12:15:35-04:00
draft: true
---

At the highest level of abstraction, a **functor** is a concept in Category Theory, a branch of mathematics that formalizes relationships between abstract objects via formal rules in any given collection of objects, referred to as **Categories**. A functor is the mapping of one category to another category. That's a pretty loaded sentence, but no worries, we shouldn't get bogged down with abstract theory to continue on our journey in understanding functors.

In programming, a **functor** is a type that can map itself to another version of itself using a mapping function. Ok, what does that mean?

> As an important side note, in the non-abstract programming world, we deal with a specific **category of types and functions**, and so we can only map data types to other data types. Since functors map a language data type to another language data type, this type of functor is called an **endofunctor**. **Endo-** prefix in Greek stands for "inner" or "internal". When I refer to functors in programming, I really mean endofunctors.

An example: say you have a data type, such as a list of things, be they integers, floats, strings, or any other type. What if you wanted to create another list from the original list based on some mapping function. That might sound familiar to some. That's what we canonically refer to as a `map` function. To call `map` on a list, you have to provide a function that maps each element in that list to another form of that element, aggregating a new list at the end.

```typescript
// Simple example in Typescript

const newList = [1,2,3].map((e) => e * 2);
// newList is now [2,4,6]
```

A list is then a functor! It is a functor because you are able to apply a mapping function to the original list, and receive a new list based on the rules of the mapping function.

![](https://i.imgur.com/cNIP2Ui.png)

Let's turn to Haskell, where functors can be expressed very explicitly.

## Functors in Haskell

In Haskell, typeclasses define some behavior of a type, and for any given type to adhere to that behavior, it must implement the typeclass' functions.

For example, this is the canonical functor typeclass in Haskell

```haskell
class Functor f where
    fmap :: (a -> b) -> f a -> f b
```

Another way of reading this definition is:

**Any given type _f_ is a functor if and only if it implements the function fmap, which takes in a mapping function and a functor of some type _a_ and returns a functor of some type _b_**

For a type to be a functor, it must implement **fmap**!

In Haskell, a List is by default of type Functor, and so is a String type (which is a type synonym for an list of Characters). Underneath the hood, both types implement the functor typeclass.

We can take a look at how a List and a String implement the Functor typeclass

{{<highlight haskell>}}
instance Functor [] where
    fmap = map

-- Since a String is really a synonym for list of characters, then the definition for a Functor is the same
-- as the definition for a plain list! In the case of a String, each mapped element
-- is a of Char type
{{</highlight>}}

A way of reading that instantiation is:

**For type [] (i.e. list), fmap function is defined as _map_ function**

It happens that list already has an underlying _map_ function that has the same signature, and so for a list to be a functor is no sweat at all!

Here's an example of mapping over a list and a String.

```haskell
fmap (\e -> e * 2) [1,2,3]
-- > [2,4,6]

fmap (\e -> toUpper e) "Hello World"
-- > "HELLO WORLD"

-- More succinct way of applying fmap is via infix <$> operator,
-- and using point-free style function definitions
(*2) <$> [1,2,3]
toUpper <$> "Hello World"
```

This is read as: **call function fmap where the first parameter is a lambda function and the second parameter is a type that is a functor (i.e. list, string)**

And here it is visually:

![](https://i.imgur.com/OAKJHQl.png)

## In Summation

Functor is a common concept in languages adhering to the functional paradigm. Even though functors come from the world of abstract mathematics, like other concepts that weren't covered here like appplicatives, foldables, monads, and others, they are very useful in creating a contract for defined types. Just as in category theory, there are a certain set of rules that objects have to abide by, we are applying the same rule constraints onto our types to flesh out consistent and predictable behavior of a type system, where functors play a visible role.
