---
title: "Labeling Results"
weight: 25
---

👉 NOTE: This is a very new facility within NoSQLBench which has been added to tame the parameter 
space of more advanced testing methods. It will be changing as needed until it stable and reliable.

## What are Labels? 

NoSQLBench allows for your metrics and accompanying annotations to be organized in a
self-consistent way with a set of labels and tags. The labeling facility is built-in to 
NoSQLBench -- Labeling is not something you add to existing tests results after using it.
Labels are innate, self-consistent, and easily leveraged to apply appropriate metadata to your 
results so that they are well-defined and easily repurposed in other systems.

For example, when you name your activity, you are setting a label, even if you didn't know it 
already. You are setting a label of `activity=myactivityname`. This build on top of the _name 
everything_ strategy so that everything from your session down to your op level has a name and 
accompanying label. This system is implemented efficiently as well, with all labeling 
mechanisms being applied explicitly during initialization phases, rather than lazily during an 
activity.

Labels are defensive in nature. If you try to define another value for a label which has already 
been provided for a given runtime scope, an error will be thrown. This ensures that labeling 
semantics are protected and that users are informed when there would be a labeling conflict, and 
likely a structural or logical error in their test setup.

Labels represent a implied hierarchic structure, and this is how they are assembled. However, 
when presented, they are simply a map. They are retained in the ordered map form where possible 
for the sake of easy configuration and usage.

## Label Sources

Labels can come from multiple sources on top of those already provided.

### Automatic Labels

The labels which are part of the runtime scaffolding of NoSQLBench are provided for you 
automatically. These are represented in a nesting structure here to show which context the 
values are expected to be present and also where they will be more variant.
annotations at the workload level, but you can expect each label set to include details on all 
of its surrounding runtime context. 

Automatic labels with examples:

* `appname:nosqlbench` (always provided)
  * `node:1.2.3.4` The ip address of the first publicly-routable interface. When combined with
    the session identifier, this tuple is usually sufficient as a proxy for a GUID. If your
    needs for session identification are finer-grained, then generate a GUID and provide it
    with `--add-labels` for each run.
    * `session:nEXHI88` An identifier for each session, basically each time you run 
      NoSQLBench. This identifier is a compactified version of the millisecond epoch timestamp.
      * `workload:myworkload`  The name of your workload template if you are using named scenarios.
        * `scenario:myscenario` The name of the scenario if using named scenarios, otherwise the 
          same as session.
          * `activity:myactivity` The name of your activity.
            * `op:anopname` The name of your operation, only provided for metrics which are tied to 
              a specific op template, such as when using the op field `instrument:true`.
              * `name:metricname` provided directly for each metric which is registered at runtime.

### User-Provided

Users can inject additional labels in different places:

* Session labels are added with the `--add-labels ...` option.
* Activity labels are added with the `labels` activity parameter.
* Op template labels are added with the core `labels` op field. 
* Additional labels are added with scripting extensions supporting labels.

## Label Structure

The example label set above would be created at runtime as a (ordered when possible) map:
```json
{
  "appname": "nosqlbench",
  "node": "1.2.3.4",
  "session":"nEXHI88",
  "workload":"myworkload",
  "scenario":"myscenario",
  "activity": "myactivity",
  "op": "anopname",
  "name": "metricname"
}
```

It would be rendered (serialized) in whatever form is idiomatic where it is used.

**examples for metric family name and label set in openmetrics**
```
metricname{appname="nosqlbench",node="1.2.3.4",session="nEXHI88",workload="myworkload",scenario="myscenario",activity="myactivity",op="anopname"}
```
OR
```
{__name__="metricname",appname="nosqlbench",node="1.2.3.4",session="nEXHI88",workload="myworkload",scenario="myscenario",activity="myactivity",op="anopname"}
```

**example labels as tags in a grafana annotations API call**
```json
{
  "dashboardUID":"...",
  "panelId":1,
  "time":12345,
  "timeEnd":678910,
  "tags":[
    "appname:nosqlbench",
    "node:1.2.3.4",
    "session:nEXHI88",
    "workload:myworkload",
    "scenario:myscenario",
    "activity:myactivity",
    "op:anopname"
  ],
  "text":"Annotation Description"
}
```

## Where are label used?

### Metrics

All metrics are labeled according to the label set provided. This supports
metric systems which use the
[Open Metrics](https://github.com/OpenObservability/OpenMetrics/blob/main/specification/OpenMetrics.md)
exposition format. The recently added push reporter builds this format out of the labels provided
where metrics are instanced.

For the current (and soon to be deprecated) graphite support, the underlying labeling system 
retains control of naming semantics, and graphite names are expanded from a naming template 
using the extant labels. However, the uniqueness of these names still governs metrics instancing,
and this will be resolved shortly. 

### Annotations

Annotations are built-in to NoSQLBench, with the most prominent form being the Grafana-specific 
annotator. When using grafana with another metrics store like
[Prometheus](https://prometheus.io/)
, or 
[Victoria Metrics](https://victoriametrics.com/)
, you can have NoSQLBench send metrics 
to the metrics collector and concurrently send annotations directly to Grafana. This allows 
populations of metrics data to have markers for each session, scenario, and activity. These 
annotations embed execution details, timelines, and labeling data so that the metrics from those 
lifetimes are directly addressable using the self-same label sets.

Grafana annotations have a set of tags which uniquely identify each annotation for lookup. (NOT 
to be confused with op tags, which allow control of your workload variations.) The labels are 
provided as values in these tags in conjugate form, such as 'scenario:myscenario234', whereas 
simple annotation tags would just have a value like 'scenario'. This may cause you to pollute 
your grafana annotation tag space. For now, we are allowing it to work as-is, to see if 
time-based filtering and recent views are sufficient to limit the user-facing noise and 
server-side response. 

### Logging

Logging events sometimes use the labels to identify errors, unique measurements, or 
configuration details. Even when the label isn't shown as such, there is a direct relationship 
between a labeled element and it's name. For example, a named scenario "scenario234" would be 
logged as such, and also annotated with an explicit label set that includes 
'scenario=scenario234' via the annotator system.

### APIs

Labels are inventory addressing systems for runtime scripting. More details on this TBD.

## Labeling Standards

In order to make sure that your metrics and annotations have the necessary labels
so that the data is traceable, identifiable, and contextual, you may want to set
some label validation rules. These rules simply say which label names are required
and which are not allowed. If you have a shared system of record for your metrics with
other engineers or teams, then some discipline about how these metrics are reported is
always required, but not often adhered to, even if there is a shared idea of what the
metrics should look like. The net effect is that some of your most useful data gets lost
among and indistinguishable from a trove of other metrics data which is extraneous,
or worse, your data has this effect on others'.

It is recommended that you have a labeling standard, and that is is somewhere that is easy
to share, verify, and enforce. For lack of this, NoSQLBench provides a client-side
mechanism to make this possible in practice, although it is opt-in and not enforced from
any consuming system. It is expected that NoSQLBench will support loading any provided labeling
standards from consuming system automatically in the future.

Some specific examples for why this is important:

* You want to look the results of a test which were run 6 months ago, there are 37 other sets of
  metrics data from the same time frame. Either your data is labeled so you can distinguish the
  studies, or you're just out of luck. You could have simply required a single label with a
  unique identifier in your standard to avoid this.
* You want to aggregate results across a set of nodes. Either your data is distinguished by node,
  or you have bad data. You could simply require a node label to be provided, within the context
  of your other labels. The validity of your aggregating metrics depends directly on this being
  stable and distinctly addressable.


### Filtering and Validation

example label:

    alpha=one,beta=two,gamma=three,delta=four

#### Labeling Specifications

There are two ways to ensure that labels you send are valid:
1) Adjust the provided labels to conform to the standard before sending.
2) Validate that the labels provided already conform to the standard before sending.

These two are distinct methods which are complimentary, and thus NB does both. This is because
there are label sets which can be aligned to a standard simply by filtering, and there are other
cases where this is not possible due to missing data.

### Label Specs

These two method are combined in practice into one configuration parameter (for now at least),
since the expression of validation and pre-filtering for that validation is effectively one and the
same. This is known as the `labelspec`, and it is simply a one-line pattern which specifies which
labels are required, which labels are disallowed, and so on.

### Label Filtering

- Included labels are indicated as `+<labelname>` or simply `<labelname>`.
- Excluded labels are indicated as `-<labelname>`.
- The labelname can be any valid regular expression, and is always treated as such. Bare words
  are simply literal expressions.
- If a provided label set includes label names which are not indicated as either, then they are
  Included by default.

So why would you have a bare label name in lieu of `+` or vice-versa if they mean the same thing?
The reason is that the labelname filter operates as a tri-state filter, going from left to right
over the labelspec. You can include, or exclude any label name, and you can also use _wildcard_
patterns simply by using regular expressions. Thus, a spec of `lioness,-lion.*` indicates that you
want to allow `lioness` labels but disallow any other labels which start with `lion`. However,
this does not say that you _require_ a `lioness` label, which looks like this `+lioness,-lion.*`.
In both cases, any label which is not mentioned will pass through the filter unchanged. If you
wanted to reject all others, you can always end the labelspec with a `-.*`, such as `+lioness,
-lion.*,-.*`, which is also duplicitous and can simply be reduced to `+lioness,-.*`.

#### Label Filtering Effects

The label filter is applied at the point of label usage on outbound signals. Metrics and
Annotations both have the filter applied. The underlying label sources are not affected.
When a label set is passed through the label filter, some fields may be removed. Otherwise label
filtering is entirely passive. The order of labels is preserved if they came from an ordered map.

### Label Validation

Label validation occurs on a label set immediately after filtering. The specification is identical,
although it weighs a little more heavily in terms of side-effects for validation.

- Required label names are indicated with `+<labelname>`
- Disallowed label names are indicated with `-<labelname>`
- Bare words are disregarded by validation.
- Patterns are still applied with the expected behavior.

#### Label Validation Effects

Label validation occurs immediately after label filtering, and will throw an error in your test,
with an error that indicates why the label set could not be validated.

This is because invalid labels will cause pain downstream. In most cases, this will manifest
early in a test so that you don't have any wasted bake time. It is recommended that you always run
a full end-to-end functional validation of all your test workflows before running the more
intensive versions. This helps you shake out any issues with late-validation, such as a bad
label set in a later stage of testing.

## TBD: conditional validation

There is a very clear need to make the validation work a bit more reasonably with regard to
labeling context. For example, requiring an `activity` label at the top level does not work well 
for the session annotation, nor does requiring and `activity` label when the `scenario` label is 
not present. There are some simple forms which can support these coherently and these will be 
added shortly. 

