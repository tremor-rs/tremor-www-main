+++
date = "2020-08-06T04:02:00+00:00"
title = "To async or not to async"
tags = ["dev", "rust"]
categories = ["general"]
draft = false
description = "Moving from threads to async tasks"
weight = -20200806
+++

With the upcoming tremor release, 0.9.0, we're going to move from threads as building blocks for ramps and pipelines to async tasks.

That's exciting, but let's talk about why what, and how a little.

Before we dive into it, please note this is not a comprehensive explanation of threads and async tasks. Those topics, to cover them comprehensively, deserve a book, not a blog post. We'll take shortcuts and simplifications, trying to stay as close to the truth as possible while making it easy to understand.

## The tremor that was (threads)

Threads are the basic building block of most programs that execute multiple pieces of code in parallel. The operating system is responsible for coordinating between competing demands.

The OS can unschedule, pause, and resume threads. We can use infinite or tight loops without the risk of completely blocking execution. These guarantees make concurrent code more accessible, with great tools like`crossbeam-channels` even more so.

Threads work especially well as long as the system can map them directly to a logical core. Each thread will happily work away on its part of the logic and pass the result on to the next. The one thread per core model is what tremor 0.8 or earlier use. We had a thread for the onramp, a thread for the pipeline, and a thread for the offramp. As the computational cost of decoding, processing, and encoding was often in the same ballpark, this works exceptionally well. We managed to push up to 400MB/s of JSON through the system this way (including parsing, tremor-script logic, and serialization).

This design can break apart if there are more ramps and pipelines than cores. Throughput degrades rapidly (as in up to 2 orders of magnitude worse at 30:1 ratio). We develop tremor very production-driven, and at the time of writing this, the deployment model was one pipeline/ramp group on a four-core system, so it worked out great. However, in the long run, this puts a burden on operators having to think about parallelism because of the possible crass impact on performance.

In SMP systems, we can observe a second complication. The moment two communicating threads don't share the cache, performance can plummet. This scenario exists when threads reside on two different CPUs or CCXs ([thank you AMD for making me learn so much about CPU caches](https://blog.licenser.net/2020/01/multithreaded-rust-on-threadripper/)). As long as two communicating threads share the cache,  data shared between them often avoids the main memory. In contrast, when two threads communicate between two different caches, in the worst case, every read or write will invalidate the other threads cache forcing access to the much slower memory. 

## async/futures (the tremor that will be)

Futures, and in rust async/await (short async from here on), work quite differently. While with threads, the operating system has the ultimate control over who can work when with async that power lies with the programmer. That can have many advantages, but as we all know, "with great power comes great responsibility."

So instead of the operating system being allowed to preempt a thread, tasks require cooperation. In other words, the code includes points at which a task will tell the runtime "hey, I could break here, do you want to do something else?".

The upside of this is that since we control the point where we take a pause, we can ensure that it's in a place where it's "good" for us. An example is async-io, where we allow another task to work whenever we have to wait for some IO.

Another one that we use heavily in tremor is channels. They connect sources, sinks, and pipelines. Every time a channel is empty or full, we have to wait for data being produced/processed. That is a great moment to let other tasks do work.

The cooperative model comes with a potential problem. If we pick the wrong time to let the other tasks work, we can obliterate performance or even break the system. Giving up control in the right moment is especially tricky since it is sometimes happening implicitly.

In rust, while calling `.await` is a good hint that we offer to hand off control here, it isn't a guarantee. Especially when using async_trait, we don't know if an async function ever offers to yield.

There are, however, ways around this. If we want to ensure we yield control, we can use something like yield_now. However, this comes at the cost that we might yield in situations where it is not needed.

When looking at performance, the consideration change compared to threads. For once, tasks are usually cheaper to switch between as we have more control. On the other hand, we do lose a bit of control over where a task runs, while we can pin threads to cores and make use SMP systems tasks may move between cores or executers move freely, and we lose control to the executor/runtime.

As a result, the runtime, in our case smol, matters a lot when it comes to how well an async system works. From what we've seen, smol has two characteristics that help tremor.

It keeps tasks close. So when two tasks can run consecutively on the same executor, it seems, smol will prefer that to schedule them in different executors.
A significant improvement we recently saw is that smol is no longer aggressively stealing work from other schedulers if they're not overloaded. This change prevents the runtime trashing CPU caches on an SMP system, which plays in our favor.

## How does that affect tremor

In the past, tremor focused on situations where our users had a limited number of concurrent pipelines per system. While we did support multiple pipelines, onramps/sources, offramps/sinks, the standard model was to deploy a single group per 4 logical cores.

As mentioned above, this works exceptionally well with threads. We reduced the core footprint by one order of magnitude this way. However, there is a limit to scaling this way, with redundancy, the "minimum" deployment is still 12 cores (3x4).

Over the next few releases, one of the improvements we aim for is clustering tremor, allowing to run multiple pipelines in a set of instances not having to worry too much about one eating up the capacity of the others.

Deploying a higher number of pipelines per instance changes the way we need to think about concurrency. The threaded design we used pre 0.9 doesn't work well anymore once we parallelize beyond the number of cores.

## What we ended up with

For now, the switch has some practical implications, mainly a shift in performance.

In tremor 0.8.0, colocating pipelines was dangerous. In tremor 0.9.0, this can happen without additional performance considerations. Not only that but with the improvements in smol over the last versions, we also broke the 500MB/s mark for the first time, which is quite a nice bonus.

Let's end with some pretty graphs. After all, a picture says more than a thousand words.

As a short explanation, this uses the json-throughput benchmark we have developed for tremor running with deployments of one onramp, one pipeline, and one offramp to 64 onramps, 64 pipelines, and one offramp.

![3 core performance](/img/blog/2020-08-06/async-3-cores.png)

![48 core performance](/img/blog/2020-08-06/async-48-cores.png)