---
title: "Showcase"
description: "A description of what makes NoSQLBench Unique"
date: "2021-11-09T15:35:37"
updated: "2021-11-09T15:35:37"
sort_by: "weight"
draft: false
weight: 3
---

NoSQLBench has enjoyed a history of unique innovation, driven by the vision of its users and
builders, forged by the need for practical methods to test modern systems. This section covers a
sampling of what makes NoSQLBench unique. Many of these features simply could not be found in other
testing systems when they were needed. Thus, NoSQLBench took form as we solved them one after
another in the same tool space. The result is a powerful runtime and system of components and
concepts which can be adapted to a variety of testing needs.

## Virtual Data Set

The _Virtual Dataset_ capabilities within NoSQLBench allow you to generate data on the fly. There
are many reasons for using this technique in testing, but it is often a topic that is overlooked or
taken for granted.

This has multiple positive effects on the fidelity of a test:

* It is very much more efficient than interacting with storage systems and piping data around. Even
  loading data from lightweight storage like NVMe will be more time intensive than simply generating
  it in most cases.
* As such, it leaves significant headroom on the table for introducing other valuable capabilities
  into the test system, like advanced rate metering, coordinated omission awareness, etc.
* Changing the data which is generated is as easy as changing the recipe.
* The efficiency of the client is often high enough to support single-client test setups without
  appreciable loss of capacity.
* Because of modern procedural generation techniques, the variety and shape of data available is
  significant. Increasing the space of possibilities is a matter of adding new algorithms. There is
  no data bulk to manage.
* Sophisticated test setups that are highly data dependent are portable. All you need is the test
  client. The building blocks for data generation are included, and many pre-built testing scenarios
  are already wired to use them.
* It is straight-forward to design incremental data generation schemes which produce monotonic
  identifiers, pseudo-random traversal over the values, or even statistically-shaped versions of
  incremental or pseudo-random values.

Additional details of this approach are explained below.

##### Industrial Strength

The algorithms used to generate data are based on advanced techniques in the realm of variate
sampling. The authors have gone to great lengths to ensure that data generation is efficient and as
much O(1) in processing time as possible.

For example, one technique that is used to achieve this is to initialize and cache data in high
resolution look-up tables for distributions which may otherwise perform differently depending on
their respective density functions. The existing Apache Commons Math libraries have been adapted
into a set of interpolated Inverse Cumulative Distribution sampling functions. This means that you
can use them all in the same place as you would a Uniform distribution, and once initialized, they
sample with identical overhead. This means that by changing your test definition, you don't
accidentally change the behavior of your test client, only the data as intended.

##### A Purpose-Built Tool

Many other testing systems avoid building a dataset generation component. It's a tough problem to
solve, so it's often just avoided. Instead, they use libraries like "faker" or other sources of data
which weren't designed for testing at scale. Faker is well named, no pun intended. It was meant as a
vignette and wire-framing library, not a source of test data for realistic results. If you are using
a testing tool for scale testing and relying on a faker variant, then you will almost certainly get
invalid results that do not represent how a system would perform in production.

The virtual dataset component of NoSQLBench is a library that was designed for high scale and
realistic data streams. It uses the limits of the data types in the JVM to simulate high cardinality
datasets which approximate production data distributions for realistic and reproducible results.

##### Deterministic

The data that is generated by the virtual dataset libraries is deterministic. This means that for a
given cycle in a test, the operation that is synthesized for that cycle will be the same from one
session to the next. This is intentional. If you want to perturb the test data from one session to
the next, then you can most easily do it by simply selecting a different set of cycles as your
basis.

This means that if you find something interesting in a test run, you can go back to it just by
specifying the cycles in question. It also means that you aren't losing comparative value between
tests with additional randomness thrown in. The data you generate will still look random to the
human eye, but that doesn't mean that it can't be reproducible.

##### Statistically Shaped

If you want a normal distribution, you can have it simply by specifying `Normal(50,10)`. The values
drawn from this sampling function are deterministic *AND* normal. If you want another distribution,
you can have it. All the distributions provided by the Apache Commons math libraries are supported.
You can ask for a stream of floating point values 1 trillion values long, in any order. You can use
discrete or continuous distributions, with whatever distribution parameters you need.

##### Best of Both Worlds

Some might worry that fully synthetic testing data is not realistic enough. The devil is in the
details on these arguments, but suffice it to say that you can pick the level of real data you use
as seed data with NoSQLBench. You don't have to choose between realism and agility. The procedural
data generation approach allows you to have all the benefits of testing agility of low-entropy
testing tools while retaining nearly all the benefits of real testing data.

For example, using the alias sampling method and a published US census (public domain) list of names
and surnames tha occurred more than 100x, we can provide extremely accurate samples of names
according to the published labels and weights. The alias method allows us to sample accurately
in *O(1)* time from the entire dataset by turning a large number of weights into two uniform
samples. You will simply not find a better way to sample realistic (US) names than this. (If you do,
please file an issue!) Actually, any data set that you have in CSV form with a weight column can
also be used this way, so you're not strictly limited to US census data.

##### Java Idiomatic Extension

The way that the virtual dataset component works allows Java developers to write any extension to
the data generation functions simply in the form of Java Functional interfaces. As long
as they include the annotation processor and annotate their classes, they will show up in the
runtime and be available to any workload by their class name.

Additionally, annotation based examples and annotation processing is used to hoist function docs
directly into the published docs that go along with any version of NoSQLBench.

##### Binding Recipes

It is possible to stitch data generation functions together directly in a workload YAML. These are
data-flow sketches of functions that can be copied and pasted between workload descriptions to share
or remix data streams. This allows for the adventurous to build sophisticated virtual datasets that
emulate nuances of real datasets, but in a form that takes up less space on the screen than this
paragraph!

## Portable Workloads

All the workloads that you can build with NoSQLBench are self-contained in a workload file. This
is a statement-oriented configuration file that contains templates for the operations you want to
run in a workload.

This defines part of an activity - the iterative flywheel part that is run directly within an
activity type. This file contains everything needed to run a basic activity -- A set of statements
in some ratio. It can be used to start an activity, or as part of several activities within a
scenario.

### Standard YAML Format

The format for describing statements in NoSQLBench is generic, but in a particular way that is
specialized around describing statements for a workload. That means that you can use the same YAML
format to describe a workload for kafka as you can for Apache Cassandra or DSE.

The YAML structure has been tailored to describing statements, their data generation bindings, how
they are grouped and selected, and the parameters needed by drivers, like whether they should be
prepared statements or not.

Further, the YAML format allows for defaults and overrides with a very simple mechanism that reduces
editing fatigue for frequent users.

You can also templatize document-wide macro parameters which are taken from the command line just
like any other parameter. This is a way of templating a workload and make it multi-purpose or
adjustable on the fly.

### Experimentation Friendly

Because the workload YAML format is generic across driver types, it is possible to ask one driver
type to interpret the statements that are meant for another. This isn't generally a good idea, but
it becomes extremely handy when you want to have a high level driver type like `stdout`
interpret the syntax of another driver like `cql`. When you do this, the stdout activity type _
plays_ the statements to your console as they would be executed in CQL, data bindings and all.

This means you can empirically and directly demonstrate and verify access patterns, data skew, and
other dataset details before you change back to cql mode and turn up the settings for a higher scale
test. It takes away the guess work about what your test is actually doing, and it works for all
drivers.

## Scripting Environment

The ability to write open-ended testing simulations is provided in NoSQLBench by means of a scripted
runtime, where each scenario is driven from a control script that can do anything the user wants.

##### Dynamic Parameters

Some configuration parameters of activities are designed to be assignable while a workload is
running. This makes things like threads, rates, and other workload dynamics in real-time. The
internal APIs work with the scripting environment to expose these parameters directly to scenario
scripts. Drivers that are provided to NoSQLBench can also expose dynamic parameters in the same way
so that anything can be scripted dynamically when needed.

##### Scripting Automatons

When a NoSQLBench scenario is running, it is under the control of a single-threaded script. Each
activity that is started by this script is run within its own thread pool, simultaneously and
asynchronously.

The control script has executive control of the activities, as well as full visibility into the
metrics that are provided by each activity. The way these two parts of the runtime meet is through
the service objects which are installed into the scripting runtime. These service objects provide a
named access point for each running activity and its metrics.

This means that the scenario script can do something simple, like start activities and wait for them
to complete, OR, it can do something more sophisticated like dynamically and iteratively scrutinize
the metrics and make real-time adjustments to the workload while it runs.

##### Analysis Methods

Scripting automatons that do feedback-oriented analysis of a target system are called analysis
methods in NoSQLBench. These are used for advanced testing scenarios. Advanced testers or 
researchers can build their own in a way that interacts with a live system with feedback and 
sampling times measured in seconds.

##### Command Line Scripting

The command line has the form of basic test commands and parameters. These command get converted
directly into scenario control script in the order they appear. The user can choose whether to stay
in high level executive mode, with simple commands like `nb test-scenario ...`, or to drop
directly into script design. They can look at the equivalent script for any command line by running
`--show-script`. If you take the script that is dumped to console and run it, it will do exactly the
same thing as if you hadn't even looked at it and just ran basic commands on the command line.

There are even ways to combine script fragments, full commands, and calls to scripts on the command
line. Since each variant is merely a way of constructing scenario script, they all get composited
together before the scenario script is run.

New introductions to NoSQLBench should focus on the command line. Once a user is familiar with this,
it is up to them whether to tap into the deeper functionality. If they don't need to know about
scenario scripting, then they shouldn't have to learn about it to be effective. This is what we are
calling a _scalable user experience_.

##### Compared to DSLs

Other tools may claim that their DSL makes scenario "simulation" easier. In practice, any DSL is
generally dependent on a development tool to lay the language out in front of a user in a fluent
way. This means that DSLs are almost always developer-targeted tools, and mostly useless for casual
users who don't want to break out an IDE.

One of the things a DSL proponent may tell you is that it tells you "all the things you can do!".
This is de-facto the same thing as it telling you
"all the things you can't do" because it's not part of the DSL. This is not a win-win for the user.
For DSL-based systems, the user is required to use the DSL, even when it interferes with the 
user's creative control. Most DSLs aren't rich enough to do much that is interesting from a 
simulation perspective.

In NoSQLBench, we don't force the user to use the programming abstractions except at a very surface
level -- the CLI. It is up to the user whether to open the secret access panel for the more
advanced functionality. If they decide to do this, we give them a commodity language (ECMAScript),
and we wire it into all the things they were already using. We don't take away their creative
freedom by telling them what they can't do. This way, users can pick their level of investment and
reward as best fits their individual needs, as it should be.

##### Scripting Extensions

Also mentioned under the section on modularity, it is relatively easy for a developer to add their
own scripting extensions into NoSQLBench as named service objects.

## Modular Architecture

The internal architecture of NoSQLBench is modular throughout. Everything from the scripting
extensions to data generation is enumerated at compile time into a service descriptor, and then
discovered at runtime by the SPI mechanism in Java.

This means that extending and customizing bundles and features is quite manageable.

It also means that it is relatively easy to provide a suitable API for multi-protocol support. In
fact, there are several drivers available in the current NoSQLBench distribution. You can list them
out with `nb --list-drivers`, and you can get help on how to use each of them
with `nb help <driver name>`.

This also is a way for us to encourage and empower other contributors to help develop the
capabilities and reach of NoSQLBench. By encouraging others to help us build NoSQLBench modules and
extensions, we can help more users in the NoSQL community at large.

## High Fidelity Metrics

Since NoSQLBench has been built as a serious testing tool for all users, some attention was
necessary on the way metric are used. More details follow...

##### Discrete Reservoirs

In NoSQLBench, we avoid the use of time-decaying metrics reservoirs. Internally, we use HDR
reservoirs with discrete time boundaries. This is so that you can look at the min and max values and
know that they apply accurately to the whole sampling window.

##### Metric Naming

All running activities have a symbolic alias that identifies them for the purposes of automation and
metrics. If you have multiple activities running concurrently, they will have different names and
will be represented distinctly in the metrics flow.

##### Precision and Units

By default, the internal HDR histogram reservoirs are kept at 4 digits of precision. All timers are
kept at nanosecond resolution.

##### Metrics Reporting

Metrics can be reported via graphite as well as CSV, logs, HDR logs, and HDR stats summary CSV
files.

##### Coordinated Omission

The metrics naming and semantics in NoSQLBench are set up so that you can have coordinated omission
metrics when they are appropriate, but there are no there changes when they are not. This means that
the metric names and meanings remain stable in any case.

Particularly, NoSQLBench tries to avoid the term "latency" altogether as it is often overused and 
thus prone to confusing people.

Instead, the terms `service time`, `wait time`, and `response time` are used. These are abbreviated
in metrics as `servicetime`, `waittime`, and
`responsetime`.

The `servicetime` metric is the only one which is always present. When a rate limiter is used, then
additionally `waittime` and `responsetime` are reported.

## Advanced Testing Features

**NOTE:**
Some features discussed here are only for advanced testing scenarios. First-time users should
become familiar with the basic options first.

##### Hybrid Rate Limiting

Rate limiting is a complicated endeavor, if you want to do it well. The basic rub is that going fast
means you have to be less accurate, and vice-versa. As such, rate limiting is a parasitic drain on
any system. The act of rate limiting itself poses a limit to the maximum rate, regardless of the
settings you pick. This occurs as a side-effect of forcing your system to interact with some
hardware notion of time passing, which takes CPU cycles that could be going to the thing you are
limiting.

This means that in practice, rate limiters are often very featureless. It's daunting enough to need
rate limiting, and asking for anything more than that is often wishful thinking. Not so in
NoSQLBench.

The rate limiter in NoSQLBench provides a comparable degree of performance and accuracy to others
found in the Java ecosystem, but it *also* has advanced features:

- It allows a sliding scale between average rate limiting and strict rate limiting, called _
  bursting_.
- It internally accumulates delay time, for C.O. friendly metrics which are separately tracked for
  each and every operation.
- It is resettable and reconfigurable on the fly, including the bursting rate.
- It provides its configured values in addition to performance data in metrics, capturing your rate
  limiter settings as a simple matter of metrics collection.
- It comes with advanced scripting helpers which allow you to read data directly from histogram
  reservoirs, or control the reservoir window programmatically.

##### Flexible Error Handling

An emergent facility in NoSQLBench is the way that error are handled within an activity. For
example, with the CQL activity type, you are able to route error handling for any of the known
exception types. You can count errors, you can log them. You can cause errored operations to
auto-retry if possible, up to a configurable number of tries.

This means, that as a user, you get to decide what your test is about. Is it about measuring some
nominal but anticipated level of errors due to intentional over-saturation? If so, then count the
errors, and look at their histogram data for timing details within the available timeout.

Are you doing a basic stability test, where you want the test to error out for even the slightest
error? You can configure for that if you need.

##### Cycle Logging

It is possible to record the result status of each and every cycle in a NoSQLBench test run. If the
results are mostly homogeneous, the RLE encoding of the results will reduce the output file down to
a small fraction of the number of cycles. The errors are mapped to ordinals by error type, and these
ordinals are stored into a direct RLE-encoded log file. For most testing where most of the results
are simply success, this file will be tiny. You can also convert the cycle log into textual form for
other testing and post-processing and vice-versa.

##### Op Sequencing

The way that operations are planned for execution in NoSQLBench is based on a stable ordering that
is configurable. The statement forms are mixed together based on their relative ratios. The three
schemes currently supported are round-robin with exhaustion (bucket), duplicate in order
(concat), and a way to spread each statement out over the unit interval
(interval). These account for most configuration scenarios without users having to micromanage
their statement templates.