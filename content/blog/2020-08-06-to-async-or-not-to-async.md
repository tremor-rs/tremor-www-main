+++
date = "2020-08-06T04:02:00+00:00"
title = "To async or not to async"
tags = ["dev", "rust"]
categories = ["general"]
draft = true
description = "Moving from threads to async tasks"
weight = -20200806
+++

The short: with the upcoming tremor release we're going to move from threads to async tasks.

That however, isn't the interesting part, so lets talk about the why, what and how a little.

Before we dive into it, please not this is not a comprehensive explenation of threads and async
tasks. Those topics deserve a book not a blog post to cover completely so we'll take a lot of
shortcuts and simplifcations, trying to stay as close to the truth as possible while making it easy
to understand.


## A short thread

* short explenation of threads
* advantages (+ when do they work well)
* disadvantages (+ when do they not work well)

## And async?

* short explenation of async, futures and asyuc runtimes (hah short ...)
* advantages (+ where does it work well)
* disadvantages (+ when do they not work well)
* bit of rust history
* SMP? (lets see about the space )

## How does that affect tremor

* move from single deployment to tremor-cloud
* old threadded design
* more paralell processing
* circuite breakers / GD
* changes in rust (async/await)

## What we ended up with

* new design
* what does it enable
* benchmarks

