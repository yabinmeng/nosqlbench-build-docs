

# BELOW THIS LINE IS TBD

* A configuration model for all initial and updatable activity parameters. This allows for more
  intelligent and useful feedback to users around appropriate parameter usage.

The previous design of NB asked a lot of developers who contributed drivers. This was because
the original internal framework was based on a "box of components" approach, which was
initially good for experimentation and innovation. However, it did require each


## new Docs
javadoc site
developer guide
first-time-user guide
Ideally:
* imported plugins details
* imported addons details
* imported app details
* imported scenario script details
* imported workload details
* imported format details

## Bundled Scenario Scripts
- stepup
- optimo
- findmax redux

## New command line options
```
--docker-prom-retention-days=183d
--list-scripts
--list-apps
```

## New help topics
## New scripting plugins
- globalvars
- fileaccess
- csvoutput
- shutdown
- s3uploader

## URL support
- s3 (any usage of an S3 URL should work effectively like an equivalent http* URL)

## UX Improvements
- op templates can pull from activity params for some fields
- All yaml can be in mapping form (everything is named)
- All elements in a yaml can have a description field
- Uniform Workload Format
- Uniform Error Handling
* Named Scenario subsections
    - `nb5 namedscenario.yaml`
    - `nb5 namedscenario.yaml default.schema`
    - `nb5 namedscenario.yaml [<name>]`
    - `nb5 namedscenario.yaml <scenarioname>.<stepname>`
* Multiple named scenarios can be chained on the command line, just like any command:
    - `nb5 workloadfile scenario scenario2 scenario3.step1`
- Uniform Workload format
* In-Line Bindings
    - `{bindingname}`
    - `{(NumberNameToString())}`
- anything can be an op template, even a command line option
    - `nb5 run driver=cql op="select * from baselines.keyvalue where foo='{{/*ToString()*/}} limit
      10"`
* type-safe parameters
    - extraneous parameters are verboten
    - parameter types are adapted as needed or an error is thrown
    - auto-suggested alternatives based on Levenshtein distance
* open-ended binding structures now support multi-layer scaffolding of generated values with a
  mix of static and dynamic values. This means you can write templates for JSON which look like
  the payload you want to send.
    * supports all combinations of lists and maps and strings, with interspersed dynamic values
      for individual elements or as collection generators, with literals, templated strings, or
      direct binding references, and in-line bindings recipes.
* argsfile allows defaults to be set programmatically or on the command line.
* tag filtering conjugates
* auto-injected statement block and statement name tags.
    - this means: You can now construct filters for specific blocks or statements simply by
      knowing their name:
    - `tags=block:schema` or `tags='main-.*'`
* safe usage of activity params and template vars are compatible, but may not be ambiguous. This
  means that if you have a template variable in myworkload.yaml, it must be distinctly named
  from any valid activity parameters, or an error is thrown. This eliminates a confusing source
  of ambiguity.

### Scripting
- scenario.stop(...) now allows regexes for activity aliases


## Core machinery & API Improvements
- dynamic lib loading via jars
- startup logging now captures basic system hardware details and NB version, to assist in
  troubleshooting
- redesigned developer API around op template, op mapper, op dispenser, and parsed op types.
    - This means:
        - ParsedOp - op template -> op mapping -> op dispensing follows an incremental path of
          construction, allowing functional programming methods to be used *only* when needed.
        - This means that you only pay for what you use or change. Op builder methods are only
          added to the construction logic when you need to digress from the defaults.
    * simpler and more powerful API for driver developers
        - consolidated logic into core machinery
        - thin out and focus the driver APIs
        - pick concepts and API terms which are very responsibility focused
            - Op Mapper
            - Op Dispenser
            - ParsedOp (mapping from op templates to native driver calls)
    * support for op generators, or ops that inject other ops into the activity. (LWT
      statement retry, client-side join, etc)
    * Type-And-Target op template convention which represents special op types and their primary
      payload template as a key and value.

- drivers are specified per-op. (The activity param just sets the default)
- native driver contexts, known as a driver `space` allows for instancing native drivers in a data-driven way

## Driver Improvements
* modular diag driver
* cqld4 driver
    * all statement forms are supported
    * allows file or parameter-based configuration
    * some backward support for classic nb5 cql driver options
    * op templates can support any valid statement builder option

## Bundled Applications

Bundled apps have been part of NoSQLBench for some time, but these were mostly used for
behind-the-scenes work, like generating docs for binding functions. Now, these apps are
discoverable and usable by anybody who runs the `--list-apps` command.
These fall into roughly two categories: 1) NB utilities which can be used to slice and dice
test data and 2) driver-specific utilites which are useful when testing or analyzing a specific
type of system.

- `--list-apps`
- `nb5 <appname> -h`
- `nb5 <appname> ...`

### cqlgen

cqlgen - takes schema.cql tablestats -> workload.yaml
- obfuscation
- weighting by op ratios from tablestats
- point to docs ->

sstablegen

* Dry-run mode for all adapter types

review:
- 7578e91d773a9ea8113250899ef46b7aacf95e70
- 392bbcc5954019ae58956850c980646cef14a1f7
- b6627943e2aed2a80ba1aa13d0a929c1ed04b685
- 394580d66c107127cc68f88cdc64a59e9c481d43
- 0330c18a7ba0904b3b3420b94416b04ee73dd7fb
- 11dd8f62daf3d1603b75cdd85fbb872dbfaac111

* For developers
    * New APIs
        * DriverAdapter
        * SpaceCache
        * ParsedOp
        * OpMapper
        * OpDispenser

* Removal of Phases loop
* Some support for console colors
* Logging subsystem improvements

* Discovery Options

* Project Improvements
* Testing and Documentation Methods
    * Literate test examples as documentation
* Integrated test logs are artifacts
* Auto-generated reference docs are not auto-imported
* Major releases are gated by static analysis audits
* We're adding code coverage
* We've added more developers to the project
* Testing Improvements - stability and low-core build systems (gh actions)

* config
    * options can be mutually exclusive, like the thread-local rate and the per-activity rate

