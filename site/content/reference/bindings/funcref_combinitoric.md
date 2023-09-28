---
weight: 158140750
title: Combiner
---
## Combiner

Combiner - a [combinatoric](https://en.wikipedia.org/wiki/Combinatorics) toolkit for NoSQLBench
===============================================================================================

*** ** * ** ***

Synopsis
--------

Combiner is the core implementation of a combinatoric toolkit which is used
by other NoSQLBench functions in a more type-specific way. It allows for a common approach
to encoding unique values across a range of dimensions (which can be non-uniform)
with an affine mapping between different forms of data.

*** ** * ** ***

Specifier
---------

The specifier required by the constructor is a way to specify a range of character sets, each representing both
the per-value labeling as well as the radix of each position in the associated index, value, or character position.
Each position is delimited from the others with commas or semicolons. Each position can be either a single printable
character or a range of characters separated by
'-'.
Optionally, you can repeat a position with a multiplier in the form of '\*n' where n is any valid number.

Examples:

* "0-9A-F" - hexadecimal characters, one digit only ; 0123456789ABCDEF
* "0-9\*12" - characters 0-9 in 12 digits, symbolic of values 000000000000 (0) .. 999999999999
* "5;5;5;-;8;6;7;-;5;3;0;9" - 12 digits with one character each, effectively 555-867-5309, a single value
* "0-9;\*2_=24j36\*5\*1" - a somewhat random pattern with char sets \[0123456789\] and \[\*2_=24j36\*5\], showing how '\*1' at the end can be used to escape '\*5'

The specifier is parsed into a non-uniform radix model, where the characters for each position represent a numerical encoding. As such, the cardinalities of each position are multiplied together to determine the total cardinality of the specified pattern. Any total cardinality below Long.MAX_VALUE, or 9,223,372,036,854,775,807 is allowed, and any combinations which would overflow this value will throw an error.

<br />

*** ** * ** ***

Value Function
--------------


The function provided in the constructor is used to symbolically map the characters in the encoding string to a value
of any type. The value function will be called with number of distinct values up the the cardinality of the largest
position in the radix model. For example, a specifier of \`A-Za-z0-9\` would provide an input range from 0 to 61
inclusive to the value function. It is the combination of positions and unique values which provides the overall
cardinality, although the value function itself is responsible for the relatively lower cardinality elements which
are combined together to create higher-cardinality value arrays.

*** ** * ** ***

Types and Forms
---------------

Each form represents one way of seeing the data for a given cycle:

1. **ordinal** (long) - also known as the cycle, or input. This is an enumeration of all distinct combinations.
2. **indexes** (int\[\]) - an array of indexes, one for each position in the specifier and thus each element in the array or character in the encoding.
3. **encoding** (String) - a string which encodes the ordinal and the indexes in a convenient label which is unique within the range of possible values.
4. **(values) array (T\[\])** - An array of the type T which can be provided via a mapping function. This is a mapping from the indexes through the provided value function.

<br />

*** ** * ** ***

Mapping between forms
---------------------

The array value can be derived with {@link #apply(long)}, {@link #getArray(int\[\])} (int\[\])}, and
{@link #getArray(String)},
given ordinal, indexes, or encoding as a starting point, respectively. This all ultimately use the one-way
function which you provide, thus you can't go from array form to the others.

Mapping between the other three is fairly trivial:

* You can get indexes from ordinal and encoding with {@link #getIndexes(long)} and {@link #getArray(String)}.
* You can get encoding from ordinal and indexes with {@link #getEncoding(long)} and {@link #getEncoding(int\[\])}.
* You can get ordinal from indexes or encoding with {@link #getOrdinal(int\[\])} and {@link #getOrdinal(String)}.

<br />


This makes it easy to derive textual identifiers for specific combinations of elements such as a vector, use them
for
cross-checks such as with correctness testing, and represent specific test values in a very convenient form within
deterministic testing harnesses like NoSQLBench.

@param The generic type of the value which is mapped into each array position

- `long -> Combiner(String: spec, function.LongFunction<T>: elementFunction, Class<? extends T>: elementClazz) -> T[]`
  - *notes:* Construct a combiner which can compose unique combinations of array data.

@param spec
    The string specifier, as explained in {@link Combiner} docs.
@param elementFunction
    The function that indexes into a unique population of T elements
@param elementClazz
    The component type for the values array which are produced by {@link #apply(long)}


## DoubleCombiner

For comprehensive docs on how this works, please see the javadocs for {@link Combiner}\<T\>. This class is merely a primitive specialization.

- `long -> DoubleCombiner(String: spec, function.LongToDoubleFunction: elementFunction) -> double[]`
  - *notes:* Construct a combiner which can compose unique combinations of array data.

@param spec
    The string specifier, as explained in {@link DoubleCombiner} docs.
@param elementFunction
    The function that indexes into a unique population of T elements


