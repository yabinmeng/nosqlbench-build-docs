---
weight: 30
title: conversion functions
---

Conversion functions simply allow values of one type
to be converted to another type in an obvious way.

## ByteBufferSizedHashed

Create a ByteBuffer from a long input based on a provided size function. As a 'Sized' function, the first argument is a function which determines the size of the resulting ByteBuffer. As a 'Hashed' function, the input value is hashed again before being used as value.

- `long -> ByteBufferSizedHashed(int: size) -> java.nio.ByteBuffer`
  - *example:* `ByteBufferSizedHashed(16)`
  - *Functionally identical to HashedToByteBuffer(16) but using dynamic sizing implementation*
  - *example:* `ByteBufferSizedHashed(HashRange(10, 14))`
  - *Create a ByteBuffer with variable limit (10 to 14)*

- `long -> ByteBufferSizedHashed(Object: sizeFunc) -> java.nio.ByteBuffer`

## ByteBufferToHex

Convert the contents of the input ByteBuffer to a String as hexadecimal. This function is retained to avoid breaking previous workload definitions, but you should use {@link ToHexString} instead.

- `java.nio.ByteBuffer -> ByteBufferToHex() -> String`

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


## DecimalFormat

Formats a floating point value to a string using the java.text.DecimalFormat

- `double -> DecimalFormat(String: format) -> String`
  - *example:* `DecimalFormat('.##')`
  - *converts a double value to a string with only two digits after the decimal*

## DigestToByteBuffer

Computes the digest of the ByteBuffer on input and stores it in the output ByteBuffer. The digestTypes available are: MD2 MD5 SHA-1 SHA-224 SHA-256 SHA-384 SHA-512 SHA3-224 SHA3-256 SHA3-384 SHA3-512

- `long -> DigestToByteBuffer(String: digestType) -> java.nio.ByteBuffer`

- `java.nio.ByteBuffer -> DigestToByteBuffer(String: digestType) -> java.nio.ByteBuffer`

## DoubleCombiner

For comprehensive docs on how this works, please see the javadocs for {@link Combiner}\<T\>. This class is merely a primitive specialization.

- `long -> DoubleCombiner(String: spec, function.LongToDoubleFunction: elementFunction) -> double[]`
  - *notes:* Construct a combiner which can compose unique combinations of array data.

@param spec
    The string specifier, as explained in {@link DoubleCombiner} docs.
@param elementFunction
    The function that indexes into a unique population of T elements


## DoubleToFloat

Convert the input double value to the closest float value.

- `double -> DoubleToFloat() -> Float`

## EscapeJSON

Escape all special characters which are required to be escaped when found within JSON content according to the JSON spec

```
{@code
\b  Backspace (ascii code 08)
\f  Form feed (ascii code 0C)
\n  New line
\r  Carriage return
\t  Tab
\"  Double quote
\\  Backslash character
\/  Forward slash
}
```


- `String -> EscapeJSON() -> String`

## Flow

Combine functions into one.

This function allows you to combine multiple other functions into one. This is often useful
for constructing more sophisticated recipes, when you don't have the ability to use
control flow or non-functional forms.

The functions will be stitched together using the same logic that VirtData uses when
combining flows outside functions. That said, if the functions selected are not the right ones,
then it is possible to end up with the wrong data type at the end. To remedy this, be sure
to add input and output qualifiers, like `long->` or `->String` where
appropriate, to ensure that VirtData selects the right functions within the flow.

- `long -> Flow(Object[]...: funcs) -> Object`

## Format

Apply the Java String.format method to an incoming object. @see [Java 8 String.format(...) javadoc](https://docs.oracle.com/javase/8/docs/api/Formatter.html#syntax) Note: This function can often be quite slow, so more direct methods are generally preferrable.

- `Object -> Format(String: format) -> String`
  - *example:* `Format('Y')`
  - *Yield the formatted year from a Java date object.*

## HTMLEntityDecode

encode HTML Entities

- `String -> HTMLEntityDecode() -> String`
  - *example:* `HTMLEntityEncode()`
  - *Decode/Unescape input from HTML4 valid to text.*

## HTMLEntityEncode

encode HTML Entities

- `String -> HTMLEntityEncode() -> String`
  - *example:* `HTMLEntityEncode()`
  - *Encode/Escape input into HTML4 valid entties.*

## LongToByte

Convert the input long value to a byte, with negative values masked away.

- `long -> LongToByte() -> Byte`

## LongToShort

Convert the input value from long to short.

- `long -> LongToShort() -> Short`

## MirrorToString


- `String -> MirrorToString() -> String`

- `String -> MirrorToString(String: input) -> String`

## ModuloToBigDecimal

Return a {@code BigDecimal} value as the result of modulo division with the specified divisor.

- `long -> ModuloToBigDecimal() -> java.math.BigDecimal`

- `long -> ModuloToBigDecimal(long: modulo) -> java.math.BigDecimal`

## ModuloToBigInt

Return a {@code BigInteger} value as the result of modulo division with the specified divisor.

- `long -> ModuloToBigInt() -> java.math.BigInteger`

- `long -> ModuloToBigInt(long: modulo) -> java.math.BigInteger`

## ModuloToBoolean

Return a boolean value as the result of modulo division with the specified divisor.

- `long -> ModuloToBoolean() -> Boolean`

## ModuloToByte

Return a byte value as the result of modulo division with the specified divisor.

- `long -> ModuloToByte(long: modulo) -> Byte`

## ModuloToShort

Return a boolean value as the result of modulo division with the specified divisor.

- `long -> ModuloToShort(long: modulo) -> Short`

## SnappyComp

Compress the input using snappy compression

- `String -> SnappyComp() -> java.nio.ByteBuffer`

## StringDateWrapper

This function wraps an epoch time in milliseconds into a String as specified in the format. The valid formatters are documented at @see [DateTimeFormat API Docs](https://www.joda.org/joda-time/apidocs/org/joda/time/format/DateTimeFormat.html)

- `long -> StringDateWrapper(String: format) -> String`

## ToBase64String

Computes the Base64 representation of the byte image of the input long.

- `String -> ToBase64String() -> String`
  - *example:* `ToBase64String()`
  - *encode any input as Base64*

- `UUID -> ToBase64String() -> String`
  - *example:* `ToBase64String()`
  - *Encode the bits of a UUID into a Base64 String*

- `long -> ToBase64String() -> String`
  - *example:* `ToBase64String()`
  - *Convert the bytes of a long input into a base64 String*

## ToBigDecimal

Convert values to BigDecimals at configurable scale or precision.


ToBigDecimal(...) functions which take whole-numbered inputs may have
a scale parameter or a custom MathContext, but not both. The scale parameter
is not supported for String or Double input forms.

- `double -> ToBigDecimal() -> java.math.BigDecimal`
  - *example:* `ToBigDecimal()`
  - *Convert all double values to BigDecimal values with no limits (using MathContext.UNLIMITED)*

- `double -> ToBigDecimal(String: context) -> java.math.BigDecimal`
  - *notes:* Convert all input values to BigDecimal values with a specific MathContext.
The value for context can be one of UNLIMITED,
DECIMAL32, DECIMAL64, DECIMAL128, or any valid configuration supported by
{@link MathContext#MathContext(String)}, such as {@code "precision=32 roundingMode=CEILING"}.
In the latter form, roundingMode can be any valid value for {@link RoundingMode}, like
UP, DOWN, CEILING, FLOOR, HALF_UP, HALF_DOWN, HALF_EVEN, or UNNECESSARY.

  - *example:* `ToBigDecimal('DECIMAL32')`
  - *IEEE 754R Decimal32 format, 7 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('DECIMAL64'),`
  - *IEEE 754R Decimal64 format, 16 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('DECIMAL128')`
  - *IEEE 754R Decimal128 format, 34 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('UNLIMITED')`
  - *unlimited precision, HALF_UP*
  - *example:* `ToBigDecimal('precision=17 roundingMode=UNNECESSARY')`
  - *Custom precision with no rounding performed*

- `long -> ToBigDecimal() -> java.math.BigDecimal`
  - *example:* `ToBigDecimal()`
  - *Convert all long values to whole-numbered BigDecimal values*

- `long -> ToBigDecimal(int: scale) -> java.math.BigDecimal`
  - *example:* `ToBigDecimal(0)`
  - *Convert all long values to whole-numbered BigDecimal values*
  - *example:* `ToBigDecimal(2)`
  - *Convert long 'pennies' BigDecimal with 2 digits after decimal point*

- `long -> ToBigDecimal(String: context) -> java.math.BigDecimal`
  - *notes:* Convert all input values to BigDecimal values with a specific MathContext. This form is only
supported for scale=0, meaning whole numbers. The value for context can be one of UNLIMITED,
DECIMAL32, DECIMAL64, DECIMAL128, or any valid configuration supported by
{@link MathContext#MathContext(String)}, such as {@code "precision=32 roundingMode=CEILING"}.
In the latter form, roundingMode can be any valid value for {@link RoundingMode}, like
UP, DOWN, CEILING, FLOOR, HALF_UP, HALF_DOWN, HALF_EVEN, or UNNECESSARY.

  - *example:* `ToBigDecimal('DECIMAL32')`
  - *IEEE 754R Decimal32 format, 7 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('DECIMAL64'),`
  - *IEEE 754R Decimal64 format, 16 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('DECIMAL128')`
  - *IEEE 754R Decimal128 format, 34 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('UNLIMITED')`
  - *unlimited precision, HALF_UP*
  - *example:* `ToBigDecimal('precision=17 roundingMode=UNNECESSARY')`
  - *Custom precision with no rounding performed*

- `int -> ToBigDecimal() -> java.math.BigDecimal`
  - *example:* `ToBigDecimal()`
  - *Convert all int values to BigDecimal values with no limits (using MathContext.UNLIMITED)*

- `int -> ToBigDecimal(String: context) -> java.math.BigDecimal`
  - *notes:* Convert all input values to BigDecimal values with a specific MathContext.
The value for context can be one of UNLIMITED,
DECIMAL32, DECIMAL64, DECIMAL128, or any valid configuration supported by
{@link MathContext#MathContext(String)}, such as {@code "precision=32 roundingMode=CEILING"}.
In the latter form, roundingMode can be any valid value for {@link RoundingMode}, like
UP, DOWN, CEILING, FLOOR, HALF_UP, HALF_DOWN, HALF_EVEN, or UNNECESSARY.

  - *example:* `ToBigDecimal('DECIMAL32')`
  - *IEEE 754R Decimal32 format, 7 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('DECIMAL64'),`
  - *IEEE 754R Decimal64 format, 16 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('DECIMAL128')`
  - *IEEE 754R Decimal128 format, 34 digits, HALF_EVEN*
  - *example:* `ToBigDecimal('UNLIMITED')`
  - *unlimited precision, HALF_UP*
  - *example:* `ToBigDecimal('precision=17 roundingMode=UNNECESSARY')`
  - *Custom precision with no rounding performed*

- `String -> ToBigDecimal() -> java.math.BigDecimal`
  - *example:* `Convert strings to BigDecimal according to default precision (unlimited) and rounding (HALF_UP)`

- `String -> ToBigDecimal(String: context) -> java.math.BigDecimal`
  - *notes:* Convert all input values to BigDecimal values with a specific MathContext. This form is only
supported for scale=0, meaning whole numbers. The value for context can be one of UNLIMITED,
DECIMAL32, DECIMAL64, DECIMAL128, or any valid configuration supported by
{@link MathContext#MathContext(String)}, such as {@code "precision=32 roundingMode=CEILING"}.
In the latter form, roundingMode can be any valid value for {@link RoundingMode}, like
UP, DOWN, CEILING, FLOOR, HALF_UP, HALF_DOWN, HALF_EVEN, or UNNECESSARY.


## ToBigInt

Convert the input value to a {@code BigInteger}

- `long -> ToBigInt() -> java.math.BigInteger`

## ToBoolean

Convert the input value to a {@code boolean}

- `Float -> ToBoolean() -> Boolean`

- `Double -> ToBoolean() -> Boolean`

- `long -> ToBoolean() -> Boolean`

- `Integer -> ToBoolean() -> Boolean`

## ToByte

Convert the input value to a {@code Byte}.

- `Float -> ToByte() -> Byte`

- `Float -> ToByte(int: modulo) -> Byte`

- `Short -> ToByte(int: scale) -> Byte`

- `Short -> ToByte() -> Byte`

- `String -> ToByte() -> Byte`

- `long -> ToByte() -> Byte`

- `long -> ToByte(int: modulo) -> Byte`

- `int -> ToByte() -> Byte`

- `int -> ToByte(int: modulo) -> Byte`

- `double -> ToByte() -> Byte`

- `double -> ToByte(int: modulo) -> Byte`

## ToByteBuffer

Convert the input value to a {@code ByteBuffer}

- `String -> ToByteBuffer() -> java.nio.ByteBuffer`

- `java.nio.CharBuffer -> ToByteBuffer() -> java.nio.ByteBuffer`

- `int -> ToByteBuffer() -> java.nio.ByteBuffer`

- `long -> ToByteBuffer() -> java.nio.ByteBuffer`

- `long -> ToByteBuffer(int: size) -> java.nio.ByteBuffer`
  - *example:* `ToByteBuffer(13)`
  - *Repeat the input long value to make a 13byte buffer*

- `Float -> ToByteBuffer() -> java.nio.ByteBuffer`

- `Short -> ToByteBuffer() -> java.nio.ByteBuffer`

- `double -> ToByteBuffer() -> java.nio.ByteBuffer`

## ToCharBuffer

Convert the input string to a character buffer

- `String -> ToCharBuffer() -> java.nio.CharBuffer`

## ToCqlDurationNanos

Convert the input value into a {@link CqlDuration} by reading the input as total nanoseconds, assuming 30-month days.

- `long -> ToCqlDurationNanos() -> com.datastax.oss.driver.api.core.data.CqlDuration`

## ToDouble

Create a double by converting values. This function works in the following modes:

* If you pass a function that produces a numeric type, then the result of this function is evaluated for the input cycle and converted to a double.
* If you pass any numeric value which is not a function, then this value is converted and returned for every cycle as a fixed value.

- `long -> ToDouble(Object: func) -> double`

## ToFloat

Convert the input value into a float. Create a float by converting values. This function works in the following modes:

* If a float is provided, then the long input is scaled to be between 0.0f and the value provided, as a fraction of the highest possible long.
* If any other type of number is provided, then this function returns the equivalent float value.
* Otherwise, the input is assumed to be a function which takes a long input, and, if necessary, adapts to return a float with an appropriate type conversion.

- `long -> ToFloat(Object: func) -> Float`

- `double -> ToFloat(double: scale) -> Float`

- `double -> ToFloat() -> Float`

- `int -> ToFloat(int: scale) -> Float`

- `int -> ToFloat() -> Float`

- `String -> ToFloat() -> Float`

- `Short -> ToFloat() -> Float`

## ToHexString

Converts the input ByteBuffer to a hexadecimal String.

- `long -> ToHexString() -> String`

- `java.nio.ByteBuffer -> ToHexString() -> String`
  - *notes:* Convert the ByteBuffer's contents to a hex string using upper case by default.


- `java.nio.ByteBuffer -> ToHexString(boolean: useUpperCase) -> String`
  - *notes:* Convert the ByteBuffer's contents to a hex string upper or lower case.


## ToInetAddress

Convert the input value to a {@code InetAddress}

- `long -> ToInetAddress() -> InetAddress`

## ToInt

Convert the input value to an int with long modulus remainder. If the scale is chosen, then the value is wrapped at this value. Otherwise, {@link Integer#MAX_VALUE} is used.

- `Object -> ToInt() -> Integer`

- `Double -> ToInt(int: scale) -> Integer`

- `Double -> ToInt() -> Integer`

- `double -> ToInt(int: scale) -> int`

- `double -> ToInt() -> int`

- `long -> ToInt() -> int`

- `String -> ToInt() -> Integer`

- `long -> ToInt(int: scale) -> int`
  - *example:* `ToInt(1000)`
  - *converts a long input value to an int between 0 and 999, inclusive*

- `long -> ToInt() -> int`
  - *example:* `ToInt()`
  - *converts a long input value to an int between 0 and 2147483647, inclusive *

## ToJSON

Convert the input object to a JSON string with Gson.

- `Object -> ToJSON() -> String`

## ToJSONPretty

Convert the input object to a JSON string with Gson, with pretty printing enabled.

- `Object -> ToJSONPretty() -> String`

## ToLong

Convert the input value to a long.

- `Float -> ToLong(long: scale) -> Long`

- `Float -> ToLong() -> Long`

- `String -> ToLong() -> Long`

- `double -> ToLong(long: scale) -> long`

- `double -> ToLong() -> long`

## ToMD5ByteBuffer

Converts the byte image of the input long to a MD5 digest in ByteBuffer form. Deprecated usage due to unsafe MD5 digest. Replaced with DigestToByteBuffer with MD5 when absolutely needed for existing NB tests. However, stronger encryption algorithms (e.g. SHA-256) are recommended due to MD5's limitations.

- `long -> ToMD5ByteBuffer() -> java.nio.ByteBuffer`
  - *notes:* Deprecated usage due to unsafe MD5 digest.
Use the DigestToByteBuffer with alternatives other than MD5.

  - *example:* `MD5ByteBuffer()`
  - *convert the a input to an md5 digest of its bytes*

## ToShort

Convert the input value to a short.

- `String -> ToShort() -> Short`

- `Float -> ToShort() -> Short`

- `Float -> ToShort(int: modulo) -> Short`

- `long -> ToShort() -> Short`

- `long -> ToShort(int: wrapat) -> Short`
  - *notes:* This form allows for limiting the short values at a lower limit than Short.MAX_VALUE.
@param wrapat The maximum value to return.


- `int -> ToShort() -> Short`

- `int -> ToShort(int: scale) -> Short`

- `double -> ToShort() -> Short`

- `double -> ToShort(int: modulo) -> Short`

## ToString

Converts the input to the most obvious string representation with String.valueOf(...). Forms which accept a function will evaluate that function first and then apply String.valueOf() to the result.

- `Float -> ToString() -> String`

- `long -> ToString() -> String`

- `long -> ToString(function.LongUnaryOperator: f) -> String`

- `long -> ToString(function.LongFunction<?>: f) -> String`

- `long -> ToString(function.Function<Long,?>: f) -> String`

- `long -> ToString(function.LongToIntFunction: f) -> String`

- `long -> ToString(function.LongToDoubleFunction: f) -> String`

- `long -> ToString(io.nosqlbench.virtdata.library.basics.shared.from_long.to_byte.LongToByte: f) -> String`

- `long -> ToString(io.nosqlbench.virtdata.library.basics.shared.from_long.to_short.LongToShort: f) -> String`

- `double -> ToString() -> String`
  - *example:* `ToString()`
  - *map the double input value to a String*

- `double -> ToString(function.DoubleUnaryOperator: df) -> String`
  - *example:* `ToString(Add(5.7D))`
  - *map the double input value X to X+5.7D and then to a String*

- `double -> ToString(function.DoubleFunction<Double>: df) -> String`

- `double -> ToString(function.Function<Double,Double>: df) -> String`

- `int -> ToString() -> String`

- `Object -> ToString() -> String`

## ToUUID

This function creates a non-random UUID in the type 4 version (Random). It always puts the same value in the MSB position of the UUID format. The input value is put in the LSB position. This function is suitable for deterministic testing of scenarios which depend on type 4 UUIDs, but without the mandated randomness that makes testing difficult. Just be aware that the MSB will always contain value 0x0123456789ABCDEFL unless you specify a different long value to pre-fill it with.

- `long -> ToUUID() -> UUID`

- `long -> ToUUID(long: msbs) -> UUID`

## URLDecode

URLDecode string data

- `String -> URLDecode(String: charset) -> String`
  - *notes:* URLDecode any incoming string using the specified charset.

@param charset A valid character set name from {@link Charset}

  - *example:* `URLDecode('UTF-16')`
  - *URLDecode using the UTF-16 charset.*

- `String -> URLDecode() -> String`
  - *example:* `URLDecode()`
  - *URLDecode using the default UTF-8 charset.*

## URLEncode

URLEncode string data

- `String -> URLEncode(String: charset) -> String`
  - *notes:* UrlEncode any incoming string using the specified charset.

@param charset A valid character set name from {@link Charset}

  - *example:* `URLEncode('UTF-16')`
  - *URLEncode using the UTF-16 charset.*

- `String -> URLEncode() -> String`
  - *example:* `URLEncode()`
  - *URLEncode using the default UTF-8 charset.*

