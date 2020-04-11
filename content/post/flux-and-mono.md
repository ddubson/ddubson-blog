---
title: "An introduction to Flux and Mono"
date: 2018-12-30T10:00:00-04:00
draft: false
tags: ["Reactive-Programming", "Project-Reactor", "Reactive-Streams"]
---

To understand what a `Flux` and `Mono` is, context is important. This context has layers, and in our case, there are four.

Layer 1: The Reactive Manifesto
---

The Flux and Mono concepts all echo back to the Reactive Manifesto. The manifesto has a goal rooted in creating more resilient, robust, flexible systems which it calls *Reactive Systems*.

Reactive Systems are defined by four tenets:

- Responsive
- Resilient
- Elastic
- Message Driven

To read more, here is [The Reactive Manifesto](https://www.reactivemanifesto.org/)

Layer 2: Reactive Streams API and JDK 9 Flow API
---

The Reactive Manifesto addresses the reasons *why* systems should be Reactive, and the benefits that come with being Reactive. Reactive Streams API is the *how*, but so is JDK 9 Flow API. How will the system become Reactive? The first steps are to create a shared understanding, a contract on how a system can adhere to the four tenets of the Reactive Manifesto.

Reactive Streams API lays out contracts for a framework, in four parts:

{{<highlight java>}}
Publisher<T>
Subscriber<T>
Subscription
Processor<T, R>
{{</highlight>}}

The JDK 9 Flow API lays out very similar (read: identical) contracts, also in four parts:

{{<highlight java>}}
Flow.Publisher<T>
Flow.Subscriber<T>
Flow.Subscription
Flow.Processor<T, R>
{{</highlight>}}

JDK 9 Flow API, as the name suggests, is only available in Java 9 and above, whereas Reactive Streams JVM API is supported by Java 6 and above, which allows much wider adoption.

To read more,
- [Reactive Streams Initiative](http://www.reactive-streams.org/)
- [Reactive Streams JVM API Specification](https://github.com/reactive-streams/reactive-streams-jvm#specification)
- [JDK 9 Flow API](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/Flow.html)

Layer 3: Reactive Streams Implementations
---

An API by itself is just a contract, who fulfills the contract? There are a few in the JVM ecosystem.

There is **RxJava**, short for Reactive Extensions for Java. RxJava is part of the ReactiveX API set of projects that focus on creating asynchronous observable streams. RxJava adopts Reactive Streams API, but ReactiveX has a rich API definition itself which RxJava adheres to. Since version 2 of RxJava, the maintainers boast of a single dependency in the project: Reactive Streams.

There is also **Project Reactor**. Project Reactor is an implementation of the Reactive Streams API for the JVM. Maintained by Pivotal as an open source project, it is now integrated into the Spring 5 ecosystem with projects such as Spring Webflux, Spring Cloud Stream, and others.

`Flux` and `Mono` concepts were created in Project Reactor implementation of Reactive Streams API. But what are they?

To read more,

- [ReactiveX - Intro](http://reactivex.io/intro.html)
- [RxJava GitHub](https://github.com/ReactiveX/RxJava)
- [Project Reactor](https://projectreactor.io/)

Layer 4: Flux and Mono are Publishers
---

Rewinding to Reactive Streams API, one of the contracts that it defines is the concept of a `Publisher`, of a defined type. To put it simply, a `Publisher` is a stream of objects that can potentially go on forever (continuous) or it can be limited to a certain number of objects (discrete). A publisher is inherently lazy, and that means you have to subscribe to the stream for the stream to push objects to the subscriber. Under the hood, a `Subscription` is created when a publisher is subscribed to.

## Mono

A `Mono` is a type of a Publisher that can publish either one object or no objects.

##### Examples

A Mono of one integer

> The examples that follow are written in Kotlin

{{<highlight kotlin>}}
import reactor.core.publisher.Mono

val monoOfInteger: Mono<Int> = Mono.just(123)

// subscribe to Mono since it does not push unless subscribed to
monoOfInteger.subscribe { intInStream -> println(intInStream) }
{{</highlight>}}

A Mono of no integers (no objects in the stream)

{{<highlight kotlin>}}
import reactor.core.publisher.Mono

val streamOfNoIntegers: Mono<Int> = Mono.empty()

// subscribe to the Mono but it will not do anything since it is empty
streamOfNoIntegers.subscribe { intInStream -> println(intInStream) }
{{</highlight>}}

## Flux

A `Flux` is a type of Publisher that can publish either many objects (potentially endless) or no objects.

###### Examples

A Flux of a discrete amount of integers

{{<highlight kotlin>}}
import reactor.core.publisher.Flux

val fluxOfIntegers: Flux<Int> = Flux.just(9,8,7,6,5,4,3,2,1)
fluxOfIntegers.subscribe { intInStream -> println(intInStream)}
{{</highlight>}}

A Flux of no integers (no objects in the stream)

{{<highlight kotlin>}}
import reactor.core.publisher.Flux

val fluxOfNoIntegers: Flux<Int> = Flux.empty()

// subscribe to the Flux but it will not do anything since it is empty
fluxOfNoIntegers.subscribe { intInStream -> println(intInStream)}
{{</highlight>}}

A Flux of continuous data (endless), with a delay of 300ms between each publish
event

{{<highlight kotlin>}}
import reactor.core.publisher.Flux
import java.time.Duration
import kotlin.random.Random.Default.nextInt

val fluxOfIntegers: Flux<Int> = Flux.generate { sink -> sink.next(nextInt()) }

fluxOfIntegers
    .delayElements(Duration.ofMillis(300))
    .subscribe { intInStream -> println(intInStream)}
{{</highlight>}}

`Flux` and `Mono` are publishers equipped with the ability to react to how subscribers accept the data. The concept of backpressure is introduced in Reactive Streams API that allows subscribers to dictate the amount of traffic they can handle and at what pace, so the subscriber is never overloaded by the amount of objects the publisher produces. When a `Subscription` is established between a `Publisher` and `Subscriber`, the subscriber demands data through the subscription which the publisher honors. Via the `Subscription` contract, control of the flow of data is established.
