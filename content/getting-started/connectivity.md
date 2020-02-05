+++
title = "Talking To Other Systems"
date = "2020-02-05T10:01:00+01:00"
draft = false
weight = 100
description = "Connectivity"
bref= "Connecting different systems is an integral part of tremor."
toc= true
+++

<h3 class="section-head" id="h-concept"><a href="#h-concept"></a>Concept</h3>

Connecting to other systems is essential part or tremors use. We implemented those connectors in two parts, [Onramps](#h-onramps) and [Offramps](#h-oframps).

As the name suggests [Onramps](#h-onramps) are used to ingest data into tremor, either by actively retreiving it (for example by reading from Kafka) or by passively providing an endpoint for data to be send to (for example with a websocket Onramp).

In contrast [Offramps](#h-oframps) are used by tremor to send data it processed to, and where possible to provide a metric on the pressure of upstream systems.

<h3 class="section-head" id="h-onramps"><a href="#h-onramps"></a>Onramps</h3>

Currently tremor supports the following general purpose Onramps:

* [Kafka](https://docs.tremor.rs/artefacts/onramps/#kafka)
* [TCP](https://docs.tremor.rs/artefacts/onramps/#TCP)
* [UDP](https://docs.tremor.rs/artefacts/onramps/#udp)
* [WS](https://docs.tremor.rs/artefacts/onramps/#WS)

In addition the following special purpose Onramps are provided:

* [File](https://docs.tremor.rs/artefacts/onramps/#File) - reads a singular file
* [Metronome](https://docs.tremor.rs/artefacts/onramps/#metronome) - periodic tick events
* [Crononome](https://docs.tremor.rs/artefacts/onramps/#crononome) - crom based tick events
* [Blaster](https://docs.tremor.rs/artefacts/onramps/#blaster) - Benchmarking onramp

Currently the following Onramps are in active development and provided as a 'alpha' version:

* [REST](https://docs.tremor.rs/artefacts/onramps/#REST)

<h3 class="section-head" id="h-oframps"><a href="#h-oframps"></a>Offramps</h3>

Currently tremor supports the following general purpose Offramps:

* [File](https://docs.tremor.rs/artefacts/offramps/#File)
* [Kafka](https://docs.tremor.rs/artefacts/offramps/#Kafka)
* [REST](https://docs.tremor.rs/artefacts/offramps/#REST)
* [stdout](https://docs.tremor.rs/artefacts/offramps/#stdout)
* [TCP](https://docs.tremor.rs/artefacts/offramps/#TCP)
* [UDP](https://docs.tremor.rs/artefacts/offramps/#UDP)
* [WS](https://docs.tremor.rs/artefacts/offramps/#WS)

In addition the following special purpose Offramps are provided:

* [blackhole](https://docs.tremor.rs/artefacts/offramps/#REST) - benchmarking offramp
* [debug](https://docs.tremor.rs/artefacts/offramps/#REST) - benchmarking offramp
* [elastic](https://docs.tremor.rs/artefacts/offramps/#elastic) - elastic search offramps
