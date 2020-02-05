+++
title = "Customizing Tremors Behaviour"
date = "2020-02-05T13:11:00+01:00"
draft = false
weight = 101
description = "Scripting"
bref= "Tremor's behaviour is scriptable to allow making the most out of the engine"
toc= true

+++

<h3 class="section-head" id="h-concept"><a href="#h-concept"></a>Concept</h3>

At the core of tremors pipeline are two scripting languages. Tremor Script, a ETL focused language that optimises on extraction, transformation and inspection of events and Tremor Query, a SQL-like language build round tremor script to describe the graphs that form tremor pipelines.

<h3 class="section-head" id="h-script"><a href="#h-script"></a>Tremor Script</h3>

The tremor script language is build to effectively and intuitively inspect events, extract required information from them, and transform them. It is the main facility business logic in tremor is build and replaces most of the 'chain of operator' model commonly found in other platforms with a more flexible and easier to extend meachanism.

[Structural pattern matching](https://docs.tremor.rs/tremor-script/#match) makes it extremely powerful when  interacting with structured data like JSON, Influx Line protocl, or any protocol that can be represented in a JSONeque structured fashion.

On the other hand [extractors](https://docs.tremor.rs/tremor-script/#extractors) allow to quickly turn unstrucuted data in structured data and extract important infrmation from string like fields without having to write a line of rust code.

```tremor
define script extract                                # define the script that parses our apache logs
script
  match {"raw": event} of                            # we user the dissect extractor to parse the apache log
    case r = %{ raw ~= dissect|%{ip} %{} %{} [%{timestamp}] "%{method} %{path} %{proto}" %{code:int} %{cost:int}\\n| }
            => r.raw                                 # this first case is hit if the log includes an execution time (cost) for the request
    case r = %{ raw ~= dissect|%{ip} %{} %{} [%{timestamp}] "%{method} %{path} %{proto}" %{code:int} %{}\\n| }
            => r.raw                                 # the second case is hit if the log does not includes an execution time (cost) for the request
    default => emit => "bad"
  end
end;
```

The full documentation  [of the language](https://docs.tremor.rs/tremor-script) and its [standard library](https://docs.tremor.rs/tremor-script/functions) can be found in the [docs](https://docs.tremor.rs).

<h3 class="section-head" id="h-query"><a href="#h-query"></a>Tremor Query</h3>

Tremor query builds around [Tremor Script](#h-script) and extends tremors capability to not only define scripts but turn pipeline configuration into development rather then YAML wrestling. In addition to describing pipelines Tremor Query adds the ability to group and aggregate events.

<nav class="tabs" data-component="tabs">
  <ul>
    <li class="active">
      <a href="#before">Before (YAML)</a>
    </li>
    <li>
      <a href="#after">After (Tremor Script)</a>
    </li>
    <li>
      <a href="#logstash">Logstash</a>
    </li>
  </ul>
</nav>

<div id="before">

The YAML based secription is unwieldy and easy to get wrong.

```yaml
pipeline:
  - id: main
    interface:
      inputs:
        - in
      outputs:
        - out
    nodes:
      - id: runtime
        op: runtime::tremor
        config:
          script: |
            match {"message": event} of
              case r = %{ message ~= grok|%{IPORHOST:clientip}·%{USER:ident}·%{USER:auth}·[%{HTTPDATE:timestamp}]·"%{WORD:verb}·%{DATA:request}·HTTP/%{NUMBER:httpversion}"·%{NUMBER:response:int}·(?:-\|%{NUMBER:bytes:int})·%{QS:referrer}·%{QS:agent}| } => r.message
              default => drop
            end
    links:
      in: [runtime]
      runtime: [out]
```


</div>

<div id="after">

In trickle script the configuration becomes a query description based on a `select` statement to transofrm the data and a `having` clause to filter events we do not wish to keep.

```trickle
select
  match {"message": event} of
    case r = %{ message ~= grok|%{IPORHOST:clientip}·%{USER:ident}·%{USER:auth}·[%{HTTPDATE:timestamp}]·"%{WORD:verb}·%{DATA:request}·HTTP/%{NUMBER:httpversion}"·%{NUMBER:response:int}·(?:-\|%{NUMBER:bytes:int})·%{QS:referrer}·%{QS:agent}| } => r.message
    default => null
from in into out
having event != null
```

</div>

<div id="logstash">

```logstash
filter {
  grok {
    match => {
      "message" => '%{IPORHOST:clientip} %{USER:ident} %{USER:auth} \[%{HTTPDATE:timestamp}\] "%{WORD:verb} %{DATA:request} HTTP/%{NUMBER:httpversion}" %{NUMBER:response:int} (?:-|%{NUMBER:bytes:int}) %{QS:referrer} %{QS:agent}'
    }
  }
}
```

</div>

The full documentation  [of the language](https://docs.tremor.rs/tremor-query), the [special operators](https://docs.tremor.rs/artefacts/operators), and [aggregation functions](https://docs.tremor.rs/tremor-query/functions) can be found in the [docs](https://docs.tremor.rs) .
