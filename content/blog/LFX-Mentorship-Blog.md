## LFX Mentorship

Hello folks! I'm Jigyasa, a final-year computer science engineering student at Indira Gandhi Delhi Technical University for Women pursuing my bachelor's in Technology. This blog is about my experience contributing to [Tremor](https://www.tremor.rs/) as part of the LFX Mentorship program.

### Learning about Tremor

Tremor is an event processing system for unstructured data with rich support for structural pattern matching, filtering, and transformation. It is built for users that have a high message volume to deal with and want to build pipelines to process, route, or limit this event stream. It has a scripting language called tremor-script and a query language as well called tremor-query or trickle.

I had never worked on an event processing system before this internship. In fact, my first major contribution to open-source was through this mentorship program. To get started with it, my mentor [Darach Ennis](https://www.linkedin.com/in/darach-ennis-789866?lipi=urn%3Ali%3Apage%3Ad_flagship3_search_srp_all%3BnRZIYeLfRAWJpWsDNlzweA%3D%3D), suggested me some documents that helped me learn more about it:

[https://docs.tremor.rs/overview/](https://docs.tremor.rs/overview/)

[https://docs.tremor.rs/course](https://docs.tremor.rs/course/#/2)

Apart from that, learning more about the tremor-query, tremor-script, and going through the workshops in the docs can be really helpful.

The codebase of Tremor is in Rust, and since I had no prior experience with Rust, I started learning the language.

### Learning Rust

Getting started with Rust was very intimidating. It involved working with things like memory management, borrow checker, lifetimes, and the expressive types which was very new to me and hence, confusing

While getting started with a new language, I try to follow along some interactive tutorials/videos. These are some resources that I found helpful: 

[https://serokell.io/blog/learn-rust](https://serokell.io/blog/learn-rust)

[https://fasterthanli.me/articles/a-half-hour-to-learn-rust](https://fasterthanli.me/articles/a-half-hour-to-learn-rust)

[https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)

However, while coding I used to run into a lot of errors. My mentor suggested me a good practice which is to document those errors and always make good notes of anything and everything I learn. So the next time I come across something similar, I can just refer to my notes instead of searching that up on the internet. I used to document the little things like a new `vi` or `git` command that I learned and found helpful. It's also very important to keep those notes organized so that it's faster and easier to find what you're looking for. Compiling notes of all the new things learned in a day can be very helpful.

At times when I used to get stuck with some Rust errors, I used to reach out to my mentor or anyone from the Tremor community and they would help me. Apart from that, these discord servers can be of great help too:

[Rust programming language community server](https://discord.gg/rust-lang-community)

[Firepit](https://discord.gg/4BUXJEB2)

### My project

My task during the internship was to add support for the Google Cloud connectors in Tremor. I worked on adding the Google Cloud Storage and Google Cloud Pub/Sub connectors. A detailed description of it can be found here:

[https://github.com/tremor-rs/tremor-runtime/issues/724](https://github.com/tremor-rs/tremor-runtime/issues/724)

Before explaining more about it, here's what a connector involves:

**Sources:** Ingest data from the outside world and deserialise to events ( onramps )

**Sinks:** Send serialised events to the outside world ( offramps )

**Pipelines:** Business logic compiles to an event flow Directed Acyclic Graph

![Connector](/img/blog/LFX-Mentorship/connector.png?raw=true)

#### Google Cloud Storage connector:

I wrote a GCS sink that can issue the basic GCS operations such as list buckets and objects, create, insert and delete objects from the Google Cloud Platform cloud storage service. The docs can be found here: [GCS offramp docs](https://docs.tremor.rs/artefacts/offramps/#gcs)

#### Google Cloud Pub/Sub connector:

The sink can issue the operation of sending a message to a Google Cloud Pub/Sub topic. It also allows creating a subscription to a topic to receive messages in it, with the option to enable/disable message ordering. The source allows receiving messages via a subscription in batches as well as one after another. The docs can be found here:
[gpub offramp](https://docs.tremor.rs/artefacts/offramps/#gpub) and [gsub onramp](https://docs.tremor.rs/artefacts/onramps/#gsub)

## Walk-Through Guide

To get started, you need a service account on GCP and you will need a GCP pem file for certificates authentication and a service token json file.

The command used to get the service_account `json` file:
```bash
gcloud iam service-accounts keys create key-file.json -iam-account=<iam-account-name>@<project-id>.iam.gserviceaccount.com
```
or 
```bash
gcloud iam service-accounts keys create key-file.json -iam-account=<mail-id-of-service-account>
```

Go to GCP dashboard → IAM & Admin → Service Accounts and get the email-id mentioned `<mail-id-of-service-account> = email-id`

### Google Cloud Storage

The following files are required:

**outbound.trickle**

```trickle
select event from in into out
```

**inbound.trickle**

```trickle
select event from in into out
```
**test.yaml**

```yaml
offramp:
  - id: stdout
    type: stdout
    codec: json
    config:
  - id: gcs
    type: gcs
    codec: json
    postprocessors:
      - gzip    
    preprocessors:
      - gzip
    linked: true
    config:
      pem: <path-to-pem-file>
onramp:
  - id: stdin
    type: file
    codec: json
    config:
      source: /dev/stdin   
binding:
  - id: example
    links:
      '/onramp/stdin/{instance}/out':
        - '/pipeline/outbound/{instance}/in'
      '/pipeline/outbound/{instance}/out':
        - '/offramp/stdout/{instance}/in'
        - '/offramp/gcs/{instance}/in'
      '/offramp/gcs/{instance}/out':
        - '/pipeline/inbound/{instance}/in'
      '/pipeline/inbound/{instance}/out':
        - '/offramp/stdout/{instance}/in'
mapping:
  "/binding/example/passthrough":
    instance: "passthrough"
```
Supported preprocessors, that can be configured in yaml file can be found here: [preprocessors](https://docs.tremor.rs/artefacts/preprocessors/)

Supported postprocessors and more about it: [postprocessors](https://docs.tremor.rs/artefacts/postprocessors/)

Supported codecs, that can be configured in yam file can be found here: [codecs](https://docs.tremor.rs/artefacts/codecs/)

- Set the env variable

```bash
export GOOGLE_APPLICATION_CREDENTIALS="<path-to-service-token-json-file>"
```

- Command used to run tremor:

```bash
 TREMOR_PATH=tremor-script/lib target/debug/tremor server run -f outbound.trickle inbound.trickle test.yaml | jq .
```
For a detailed guide on the operations that can be performed, refer the [docs](https://docs.tremor.rs/artefacts/offramps/#gcs).

### Google Cloud Pub/sub

Google Cloud Pub/Sub guarantees delivery of all messages, whether low throughput or high throughput, so there should be no concern about messages being lost.

Pub/Sub guarantees at-least-once message delivery, which means that occasional duplicates are to be expected since we acknowledge the messages once they are received.

The following files are required:

**outbound trickle:** 

```trickle
select event from in into out
```

**inbound.trickle:**

```trickle
select {"data": event, "meta": $} from in into out;
```

**test.yaml:**

```yaml
offramp:
  - id: stdout
    type: stdout
    codec: json
    config:
  - id: gpub
    type: gpub
    codec: json
    postprocessors:
      - gzip   
    linked: true 
    config:
      pem: <path-to-pem-file>
onramp:
  - id: stdin
    type: file
    codec: json
    config:
      source: /dev/stdin  
  - id: gsub
    type: gsub
    codec: json  
    preprocessors:
      - gzip
    config:
      pem: <path-to-pem-file>
      subscription: '<name-of-subscription>'
binding:
  - id: example
    links:
      '/onramp/stdin/{instance}/out':
        - '/pipeline/outbound/{instance}/in'
      '/pipeline/outbound/{instance}/out':
        - '/offramp/stdout/{instance}/in'
        - '/offramp/gpub/{instance}/in'
      '/offramp/gpub/{instance}/out':
        - '/pipeline/inbound/{instance}/in'
      '/pipeline/inbound/{instance}/out':
        - '/offramp/stdout/{instance}/in'
      '/onramp/gsub/{instance}/out':
        - '/pipeline/inbound/{instance}/in'
mapping:
  "/binding/example/passthrough":
    instance: "passthrough"
```

Supported preprocessors, that can be configured in yam file can be found here: [preprocessors](https://docs.tremor.rs/artefacts/preprocessors/)

Supported postprocessors and more about it: [postprocessors](https://docs.tremor.rs/artefacts/postprocessors/)

Supported codecs, that can be configured in yam file can be found here: [codecs](https://docs.tremor.rs/artefacts/codecs/)

![Tremor Dot Diagrm](/img/blog/LFX-Mentorship/dot-diagram.png)

- Create a topic using the following `gcloud` command:

```bash
gcloud pubsub topics create <`topic-name`>
```

- Set the env variable

```bash
export GOOGLE_APPLICATION_CREDENTIALS="<path-to-service-token-json-file>"
```

- Command used to run tremor:

```bash
TREMOR_PATH=tremor-script/lib target/debug/tremor server run -f outbound.trickle inbound.trickle test.yaml | jq .
```

- After running tremor, create a subscription:

```bash
{"command": "create_subscription", "project": "<project-id>", "topic": "<topic-name>", "subscription": "<name-of-subscription>", "message_ordering": <`message-ordering`>}
```

*where:*

***<`message-ordering`>** - can be set to `true` or `false` . To receive the messages in order, set the message ordering property on the subscription you receive messages from. Receiving messages in order might increase latency.*

***<`project-id`>** - id of the GCP project*

***<`topic-name`>** - Name of the Pub/Sub topic*

- To send a message:

```bash
{"command": "send_message", "project":"<project-id>", "topic":"<topic-name>", "data": <`data`>, "ordering_key": "<ordering-key>"}
```

*where:*

***<`data`>** - `json` message to be sent to the topic*

***<`ordering-key`>** - If non-empty, identifies related messages for which publish order should be respected. If a Subscription has enable_message_ordering set to true, messages published with the same non-empty ordering_key value will be delivered to subscribers in the order in which they are received by the pub/sub system. All PubsubMessages published in a given PublishRequest must specify the same ordering_key value.*

## Validation Testing for Pub/Sub

For validating pub/sub, we need to make sure that there's guaranteed delivery and the messages are ordered when message_ordering is enabled for the subscription ie the sequence of messages received is preserved. So before sending the message to pub/sub, we add a field `count` in the event (json) that increments every time we send a message. This would be done in the outbound trickle file. To validate that all the messages are received in order, we have a validation logic in the inbound trickle file that checks if the difference between the `count` value of the current message and the previous message is one, the order is maintained.

We have 3 different ways for setting up the outbound trickle file the outbound trickle - using a [transient wal](https://docs.tremor.rs/workshop/examples/20_transient_gd/), [persistent wal](https://docs.tremor.rs/workshop/examples/21_persistent_gd/) and no wal.

**No wal**

```trickle
define script counter
script
  let count = match state==null of
    case true =>
      0
    default =>
      state.count + 1
  end;

  let state = {"count": count};
  {"command": "send_message", "project":"<project-id>", "topic":"<topic-name>", "data": merge event of {"count": state.count} end, "ordering_key": "<ordering-key>"}
end;

create script counter;

select event from in into counter;
select event from counter into out;
```

**Transient-wal**

```trickle
define qos::wal operator in_memory_wal
with
  read_count = 20,
  max_elements = 1000, # Capacity limit of 1000 stored events
  max_bytes = 10485760 # Capacity limit of 1MB of events
end;

create operator in_memory_wal;

define script counter
script
  let count = match state==null of
    case true =>
      0
    default =>
      state.count + 1
  end;

  let state = {"count": count};
  {"command": "send_message", "project":"<project-id>", "topic":"<topic-name>", "data": merge event of {"count": state.count} end, "ordering_key": "<ordering-key>"}
end;

create script counter;

select event from in into counter;
select event from counter into in_memory_wal;
select event from in_memory_wal into out;
```

**Persistent-wal**

```trickle
define qos::wal operator on_disk_wal
with
  read_count = 20,
  max_elements = 1000, # Capacity limit of 1000 stored events
  max_bytes = 10485760 # Capacity limit of 1MB of events
end;
create operator on_disk_wal;

define script counter
script
  let count = match state==null of
    case true =>
      0
    default =>
      state.count + 1
  end;

  let state = {"count": count};
  {"command": "send_message", "project":"<project-id>", "topic":"<topic-name>", "data": merge event of {"count": state.count} end, "ordering_key": "<ordering-key>"}
end;

create script counter;

select event from in into counter;
select event from counter into on_disk_wal;
select event from on_disk_wal into out;
```

In the **inbound** trickle, we have the validation logic as follows:

```trickle
define script validate
script
  match state == null of
    case true =>
      let valid = match event.data.count == 0 of 
        case true =>
          true      
        default =>
          false
      end,
      let state = {"prev": event.data.count},
      {"response": event, "valid": valid} 
      
    default =>
      let valid = match state.prev + 1 == event.data.count of 
        case true =>
          true
        default =>
          false
      end,
      let state = {"prev": event.data.count},
      
      {"response":event, "valid": valid}
  end;
end;

create script validate;

select {"data": event, "meta": $} from in into validate;
select event from validate into out;
```

For the validation, we run the source (onramp) and sink (offramp) separately.

## Network Failure Recovery

Testing in poor connectivity to see if guaranteed delivery works

![Network Failure Recovery testing](/img/blog/LFX-Mentorship/network-failure-testing.png)

The pivot point (where it just works) was observed when downlink and uplink packets dropped varies between 47%-50%. 

## Use of Connectors

- Bulk batch rolling event/log storage to GCS
- Distribution of openTelemetry logs/trace/metrics over GCP pub/sub

## The Tremor community

The Tremor community is absolutely great. As I was contributing to it as a part of my internship, I was lucky to have direct access to the Tremor developers working at Wayfair whenever I had any questions. We used to have a lot of meetings in the General Voice channel on discord with the Tremor developers and anyone could join in and ask questions, discuss ideas and share what they are working on. This used to be super helpful.

Apart from that, every Tuesday we used to have office hours where everyone joins in and there used to be discussions on topics like: "Why did tremor go open source", "Good practices for contributing to open-source", and Q/As. They used to be a lot of fun.

I am so grateful to my mentors: Darach, Heinz and Matthias for being super kind and always encouraging me to ask questions and clarifying all my doubts. Also, thanks to Ana for always being so nice and helping with my Rust errors. I thank them all for their time and help.

## Final thoughts

It was an overall great journey contributing to Tremor. I learned so much in these 3 months' time with the support of my mentors. I'm very grateful to CNCF for organizing this mentorship program as it gave me an opportunity to learn about event processing, distributed systems, Rust, Cloud-Native technologies, etc. I wouldn't have learned so much in these 3 months' time had I not been a part of this mentorship program. It was definitely a fun learning experience.
