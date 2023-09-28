---
weight: 20
title: pre-made functions
---

Functions in this category are meant to provide easy grab-and-go functions that are tailored for real-world simulation.
This library will grow over time. These functions are often built directly on top of other functions in the core
libraries. However, they are provided here for simplicity in workload construction. They perform exactly the same as
their longer-form equivalents.


## Cities

Return a valid city name.

- `long -> Cities() -> String`
  - *example:* `Cities()`

## CitiesByDensity

Return a city name, weighted by population density.

- `long -> CitiesByDensity() -> String`
  - *example:* `CitiesByDensity()`

## CitiesByPopulation

Return a city name, weighted by total population.

- `long -> CitiesByPopulation() -> String`
  - *example:* `CitiesByPopulation()`

## Counties

Return a valid county name.

- `long -> Counties() -> String`
  - *example:* `Counties()`

## CountiesByDensity

Return a county name weighted by population density.

- `long -> CountiesByDensity() -> String`
  - *example:* `CountiesByDensity()`

## CountiesByPopulation

Return a county name weighted by total population.

- `long -> CountiesByPopulation() -> String`
  - *example:* `CountiesByPopulation()`

## CountryCodes

Return a valid country code.

- `long -> CountryCodes() -> String`
  - *example:* `CountryCodes()`

## CountryNames

Return a valid country name.

- `long -> CountryNames() -> String`
  - *example:* `CountryNames()`

## DoubleVectors

This is a version of the NoSQLBench {@link io.nosqlbench.virtdata.library.basics.shared.util.Combiner} which is especially suited to constructing unique sequences of doubles. This can be to create arbitrarily long vectors in double\[\] form, where each vector corresponds to a specific character encoding. Based on the maximum cardinality of symbol values in each position, a step function on the unit interval is created for you and used as a source of magnitudes.


For example, with a combiner spec of "{@code a-yA-Y\*1024}", the "{@code }a-yA-Y" part creates a character set
mapping for 50 distinct indexed character values with the letter acting as a code, and then the "{@code \*1024}"
repeats ths mapping over 1024 *digits* of values, which are then concatenated into an array of values as a
uniquely encoded vector. In actuality, the internal model is computed separately from the character encoding, so is
efficient, although the character encoding can be used to uniquely identify each vector.

Note that as with other combiner forms, you can specify a different cardinality for each position, although
the automatically computed step function for unit-interval will be based on the largest cardinality. It is not
computed separately for each position. Thus, a specifier like "{@code a-z\*5;0-9\*2}"
will only see the last two positions using a fraction of the possible magnitudes, as the a-z element has the most steps at 26 between 0.0 and 1.0.

- `long -> DoubleVectors(String: spec) -> double[]`
  - *notes:* Create a radix-mapped vector function based on a spec of character ranges and combinations.
@param spec - The string specifier for a symbolic cardinality and symbol model that represents the vector values

  - *example:* `DoubleVector('0-9*12')`
  - *Create a sequence of vectors encoding a 10-valued step function over 12 dimensions*
  - *example:* `DoubleVector('01*1024')`
  - *Create a sequence of vectors encoding a 2-valued step function over 1024 dimensions*
  - *example:* `DoubleVector('a-yA-Y0-9!@#$%^&*()*512')`
  - *Create a sequence of vectors encoding a 70-valued step function over 512 dimensions*

## FirstNames

Return a pseudo-randomly sampled first name from the last US census data on first names occurring more than 100 times. Both male and female names are combined in this function.

- `long -> FirstNames() -> String`
  - *example:* `FirstNames()`
  - *select a random first name based on the chance of seeing it in the census data*

- `long -> FirstNames(String: modifier) -> String`
  - *example:* `FirstNames('map')`
  - *select over the first names by probability as input varies from 1L to Long.MAX_VALUE*

## FullNames

Combines the FirstNames and LastNames functions into one that simply concatenates them with a space between. This function is a shorthand equivalent of {@code Template('{} {}', FirstNames(), LastNames())}

- `long -> FullNames() -> String`

## LastNames

Return a pseudo-randomly sampled last name from the last US census data on last names occurring more than 100 times.

- `long -> LastNames() -> String`
  - *example:* `LastNames()`
  - *select a random last name based on the chance of seeing it in the census data*

- `long -> LastNames(String: modifier) -> String`
  - *example:* `LastNames('map')`
  - *select over the last names by probability as input varies from 1L to Long.MAX_VALUE*

## NumberNameToString

Provides the spelled-out name of a number. For example, an input of 7 would yield "seven". An input of 4234 yields the value "four thousand thirty four". The maximum value is limited at 999,999,999.

- `long -> NumberNameToString() -> String`

## StateCodes

Return a valid state code. (abbreviation)

- `long -> StateCodes() -> String`
  - *example:* `StateCodes()`

## StateCodesByDensity

Return a state code (abbreviation), weighted by population density.

- `long -> StateCodesByDensity() -> String`
  - *example:* `StateCodesByDensity()`

## StateCodesByPopulation

Return a state code (abbreviation), weighted by population.

- `long -> StateCodesByPopulation() -> String`
  - *example:* `StateCodesByPopulation()`

## StateNames

Return a valid state name.

- `long -> StateNames() -> String`
  - *example:* `StateNames()`

## StateNamesByDensity

Return a state name, weighted by population density.

- `long -> StateNamesByDensity() -> String`
  - *example:* `StateNamesByDensity()`

## StateNamesByPopulation

Return a state name, weighted by total population.

- `long -> StateNamesByPopulation() -> String`
  - *example:* `StateNamesByPopulation()`

## TimeZones

Return a state name, weighted by population density.

- `long -> TimeZones() -> String`
  - *example:* `Timezones()`

## TimeZonesByDensity

Return a state name, weighted by population density.

- `long -> TimeZonesByDensity() -> String`
  - *example:* `TimezonesByDensity`

## TimeZonesByPopulation

Return a state name, weighted by population.

- `long -> TimeZonesByPopulation() -> String`
  - *example:* `TimezonesByPopulation()`

## ToMD5ByteBuffer

Converts the byte image of the input long to a MD5 digest in ByteBuffer form. Deprecated usage due to unsafe MD5 digest. Replaced with DigestToByteBuffer with MD5 when absolutely needed for existing NB tests. However, stronger encryption algorithms (e.g. SHA-256) are recommended due to MD5's limitations.

- `long -> ToMD5ByteBuffer() -> java.nio.ByteBuffer`
  - *notes:* Deprecated usage due to unsafe MD5 digest.
Use the DigestToByteBuffer with alternatives other than MD5.

  - *example:* `MD5ByteBuffer()`
  - *convert the a input to an md5 digest of its bytes*

## ZipCodes

Return a valid zip code.

- `long -> ZipCodes() -> String`
  - *example:* `ZipCodes()`

## ZipCodesByDensity

Return a zip code, weighted by population density.

- `long -> ZipCodesByDensity() -> String`
  - *example:* `ZipCodesByDensity`

## ZipCodesByPopulation

Return a zip code, weighted by population.

- `long -> ZipCodesByPopulation() -> String`
  - *example:* `ZipCodesByPopulation()`

