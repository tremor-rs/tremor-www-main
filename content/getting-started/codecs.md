+++
title = "Understanding Data"
date = "2020-02-05T10:01:00+01:00"
draft = false
weight = 110
description = "Codecs"
bref= "De- and encode data from wire formats"
toc= true
+++

### Concept

At the edges of On- and Offraps tremor uses codecs to transform the wire format into a structured form that tremor can understand. This structured format loosely follows the restrictions of JSON with additional restrictions based on different codes.

Codes share some comcepts with [extractors](https://docs.tremor.rs/tremor-script/#extractors) but are written to be highly performant in what they do and are always mirrored in the way that they provide Decodiung and Encoding.

In addition to codecs, that work on structured data, pre and postprocessors allow dealing with binary data streas as an interface between the ramp and the codec. Preprocessors are executed on the binary data the onramp recives prior to being handed to a codec. Postprocessors are executed after the data leaves the codec and before they'the data is send out of the offramp. 

### Data Format

As in internal representation we follow a JSONesque structure. The value types boil down to:

* String
* Numeric (float and integer)
* Boolean
* Null
* Array
* Record (string keys)

### Codecs

Tremor supports en and decoding the following structured wire formats:

* [json](https://docs.tremor.rs/artefacts/codecs#json)
* [msgpack](https://docs.tremor.rs/artefacts/codecs#msgpack)
* [influx](https://docs.tremor.rs/artefacts/codecs#influx)
* [binflux](https://docs.tremor.rs/artefacts/codecs#binflux) - (binary representation of the influx wire protocol)
* [statsd](https://docs.tremor.rs/artefacts/codecs#statsd)
* [yaml](https://docs.tremor.rs/artefacts/codecs#yaml)

In addition the [string](https://docs.tremor.rs/artefacts/codecs#string) codec is supported for any unstructured, valid UTF8, string.

<h3 class="section-head" id="h-concept"><a href="#h-codecs"></a>Pre- and Postprocessors</h3>

As preprocessors tremor supports:

* [lines](https://docs.tremor.rs/artefacts/preprocessors/#lines) - split by newline
* [lines-null](https://docs.tremor.rs/artefacts/preprocessors/#lines-null) - split by null byte
* [lines-pipe](https://docs.tremor.rs/artefacts/preprocessors/#lines-pipe) - split by `|`
* [base64](https://docs.tremor.rs/artefacts/preprocessors/#base64) - base64 decoding
* [decompress](https://docs.tremor.rs/artefacts/preprocessors/#decompress) - auto detecting decompress
* [gzip](https://docs.tremor.rs/artefacts/preprocessors/#gzip) - gzip decompress
* [zlib](https://docs.tremor.rs/artefacts/preprocessors/#zlib) - zlib decompress
* [xz](https://docs.tremor.rs/artefacts/preprocessors/#xz) - xz decompress
* [snappy](https://docs.tremor.rs/artefacts/preprocessors/#snappy) - snappy decompress
* [lz4](https://docs.tremor.rs/artefacts/preprocessors/#lz4) - zl4 decompress
* [gelf-chunking](https://docs.tremor.rs/artefacts/preprocessors/#gelf-chunking) - GELF chunking support
* [remove-empty](https://docs.tremor.rs/artefacts/preprocessors/#remove-empty) - remove emtpy (0 len) messages
* [length-prefixerd](https://docs.tremor.rs/artefacts/preprocessors/#length-prefixerd) - length prefixed splitting for streams

As postprocessors tremor supports:

* [lines](https://docs.tremor.rs/artefacts/postprocessors/#lines)
* [base64](https://docs.tremor.rs/artefacts/postprocessors/#base64)
* [length-prefixerd](https://docs.tremor.rs/artefacts/postprocessors/#length-prefixerd)
* [compression](https://docs.tremor.rs/artefacts/postprocessors/#compression)