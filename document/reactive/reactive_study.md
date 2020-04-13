---
tags : [Study]
title: Reactive_programming.md
created: 2020-04-13 12:01:29
modified: 2020-04-13 12:01:29
---

# Reactive_programming.md

## Interactons

in reactive stream sequences, the source `publish` produces data.
But by default, It does nothing until a `Subscriber` has registered (subscribed), at which point it will `push` data to it

Reactor adds the concept of `operators`, which are chained together to describe what processing to apply at each stage to the data. Applying an operator returns a new intermediate `Publisher`

## Flux

A `Flux<T>` is a Reactive Streams `Publisher`, augmented with a lot of operators that can be used to generate, transform, orchestrate Flux sequences.

It can emit 0 to n `<T>` elements (`onNext` event) then either completes or errors(`onComplete` and `onError` terminal events).
if no terminal event is triggered, the `Flux` is infinite.

* static factories on Flux allow to create sources, or generate them from several callback types.
* Instance methods, the operators, let you build an asynchronous processing pipeline that will produce an asynchronous sequence.
* Each `Flux#subscribe()` or multicating operation such as `Flux#publish` and `Flux#publishNext` will materialize a dedicated instance of the pipeline and trigger the data flow inside it.

```java
package io.pivotal.literx;

//generic imports to help with simpler IDEs (ie tech.io)
import java.util.*;
import java.util.function.*;
import java.time.*;

import reactor.core.publisher.Flux;

/**
 * Learn how to create Flux instances.
 *
 * @author Sebastien Deleuze
 * @see <a href="https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html">Flux Javadoc</a>
 */
public class Part01Flux {

//========================================================================================

	// TODO Return an empty Flux
	Flux<String> emptyFlux() {
		return Flux.empty();
	}

//========================================================================================

	// TODO Return a Flux that contains 2 values "foo" and "bar" without using an array or a collection
	Flux<String> fooBarFluxFromValues() {
		return Flux.just("foo", "bar");
	}

//========================================================================================

	// TODO Create a Flux from a List that contains 2 values "foo" and "bar"
	Flux<String> fooBarFluxFromList() {
		return Flux.fromIterable(Arrays.asList("foo", "bar"));
	}

//========================================================================================

	// TODO Create a Flux that emits an IllegalStateException
	Flux<String> errorFlux() {
		return Flux.just("foo", "bar")
	}

//========================================================================================

		// TODO Create a Flux that emits increasing values from 0 to 9 each 100ms
	Flux<Long> counter() {
		return null;
	}

}
```

## 참고
* https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro
* https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Flux
