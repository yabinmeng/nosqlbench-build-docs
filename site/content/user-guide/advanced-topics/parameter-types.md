---
title: "Parameter Types"
weight: 15
---

To configure a NoSQLBench Scenario to do something useful, you have to provide parameters to it.
This can occur in one of several ways. This section is a guide on parameter types and how 
they layer together.

Casual users do not need to understand all the ways you can parameterize nb5. 

# Global Options

The command line is used to configure both the overall runtime (logging, etc.) and
individual scenarios or activities. Global options can be distinguished from scenario commands and
their parameters because global options always start with a `-single` or `--double-hyphen`.

You can co-mingle global options with scenario or activity params. They will be parsed out 
without disturbing the sequence of those other options.

## Script Params

When you run a scenario script with nb5, you can provide params to it, just as you can with an 
activity. These params are provided to the scripting environment in a service object called 
`params`.

## Scenario Commands

Any command line argument which is not a global option (starting with a `-` or `--`), is a 
scenario command. These are all described in [CLI Scripting](../cli-scripting). All scenario 
commands follow this form: `<command-word> [<param>=<value>] ...` A scenario is all about 
running activities and manipulating and/or measuring them, so it follows that these commands 
generally have _activity params_

For example, you may run an nb5 scenario with a single activity like this 
`nb5 run driver=... workload=...`, which runs an activity. But we don't make you type activity 
everywhere. Having to say `nb5 run-activity ...`, and `nb5 wait-for-activity ...` everywhere 
would not be fun. So, we can just say that the `nb5 run driver=stdout ...` is short for running an 
activity in the scenario. So, really, the `driver=stdout` is an activity param, even 
though it's part of a scenario command.

## Activity Params

When you run an activity in a scenario, every named parameter after the `run` or `start` (or 
whatever) command is an activity param. `driver=stdout` is an activity param, but it is uniquely 
important. Setting this value tells nb5 what kind driver will interpret op templates, and thus 
what op templates really mean. Think of the driver as an op template interpreter. This also 
affects what additional activity params can be used. This is really important for first time users:

👉 **Depending on the driver param, additional activity params may become available.**

👉 **Depending on the driver param, op templates will be interpreted a certain way.**

So, you could say that the driver _activity param_ is the most important, but they are all 
important when you need to customize nb5.

The driver param is also not the last word on what driver will interpret an op template. In fact,
the driver is something that can be specified **per op template**. So, the driver param is 
really just the _default driver_ to be used for any op template that doesn't have one 
specified. This is pretty handy when you need to have a variety of different operations from 
different drivers in the same activity.

## Named Scenario Params

It is common to use the [Named Scenarios](../../workloads_101/11-named-scenarios) feature to 
bundle up multiple activity workflows into push-button scenarios. When you do this, it is 
possible to apply params on the command line to the named scenario. This means you are applying 
a single set of parameters to possibly multiple activities, so there is one-to-many relationship.
For this reason, the named scenarios have a form of parameter locking that allows you to drop 
your param overrides from the command line into exactly the places which are intended to be 
changeable. Double equals is a soft (quiet) lock, and triple equals is a hard (verbose) lock. 
Any parameter provided after a workflow will replace the same named parameters in the named 
scenario steps which are not locked.

## Op Template Fields

Activities, *all activities*, are based on a set of op templates. These are the
[YAML](https://yaml.org/), [json](https://www.json.org/), [jsonnet](https://jsonnet.org/), or direct 
data structures which follow the [workload definition](../workload_definition)
standard. These schematic values are provided to the selected driver to be mapped to native 
operations. So why are we referring to them here, in the parameters section?

Well, because op template fields can sometimes take their defaults from the activity params! For 
example, if a driver developer wants to allow it, fields like `consistency_level` can be set for 
all operations for an activity. However, this is elective per-driver per-field, and thus 
up to the driver developer to enable if they so choose. If a given op template does not care about 
the value, then it will not be used, but if it does, and a same-named activity param is provided
it will be used as if it were specified in the op template directly.

Apart from this mechanism, op template fields live in a different layer and are used for 
different purposes. Users will see these in driver documentation as examples and available op 
template fields. 

### Standard Activity Params

Some parameters that can be specified for an activity are standardized in the NoSQLBench design.
These include parameters like `driver`, `alias`, and `threads`. Find more info on standard 
params at [Standard Activity Params](../standard-activity-parameters).

### Dynamic Activity Params

Some driver params are instrumented in the runtime to be dynamically changed during a scenario's 
execution. This means that a scenario script can assign a value to an activity parameter after 
the activity is started. Further, these assignments are treated like events which force the 
activity to observe any changes and modify its behavior in real time.

This is accomplished with a two-layer _configuration_ model. The initial configuration for an 
activity is gated by a type model that knows what parameters are required, what the default 
values are, and so on. This configuration model is an aggregate of all the active drivers in 
an activity.

A second configuration model, called the _reconfiguration_ model, can expose a set of the 
original config params for modification. In this way, driver developers can allow for a variety 
of dynamic behaviors which allow for advanced analysis of workloads without restarts. These 
parameters are not meant for casual use. They are generally used in advanced scripting scenarios.

Parameters that are dynamic should be documented as such where they are described.

### Template Variables

If you need to provide general-purpose overrides to a in a workload template, then you
may use a mechanism called _template variables_. These are just like activity parameters, but they
are set via macro and can have defaults. This allows you to easily template workload properties 
in a way that is easy to override on the command line or via scripting.

