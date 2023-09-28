---
weight: 52584703
title: DoubleArrayCache
---
## DoubleArrayCache

Precompute the interior double\[\] values to use as a LUT.

- `long -> DoubleArrayCache(io.nosqlbench.virtdata.library.basics.shared.vectors.primitive.VectorSequence: function) -> double[]`

## DoubleCache

Precompute the interior double\[\] values to use as a LUT.

- `long -> DoubleCache(io.nosqlbench.virtdata.library.basics.shared.vectors.primitive.DoubleSequence: sequence) -> double`

## DoubleVectorPadLeft

Prefix the incoming array with an empty double\[\] so that it is sized up to at least the given size. If it is already at least that size, pass it through as-is.

- `double[] -> DoubleVectorPadLeft(int: size) -> double[]`

## DoubleVectorPadRight

Suffix the incoming array with an empty double\[\] so that it is sized up to at least the given size. If it is already at least that size, pass it through as-is.

- `double[] -> DoubleVectorPadRight(int: size) -> double[]`

## DoubleVectorPrefix

Prefix the incoming array with an empty double\[\] of the given size.

- `double[] -> DoubleVectorPrefix(int: size) -> double[]`

## DoubleVectorSuffix

Suffix the incoming array with an empty double\[\] of the given size.

- `double[] -> DoubleVectorSuffix(int: size) -> double[]`

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

## FloatVectorPadLeft

Prefix the incoming array with an empty float\[\] so that it is sized up to at least the given size. If it is already at least that size, pass it through as-is.

- `float[] -> FloatVectorPadLeft(int: size) -> float[]`

## FloatVectorPadRight

Suffix the incoming array with an empty float\[\] so that it is sized up to at least the given size. If it is already at least that size, pass it through as-is.

- `float[] -> FloatVectorPadRight(int: size) -> float[]`

## FloatVectorPrefix

Prefix the incoming array with an empty float\[\] of the given size.

- `float[] -> FloatVectorPrefix(int: size) -> float[]`

## FloatVectorSuffix

Suffix the incoming array with an empty double\[\] of the given size.

- `float[] -> FloatVectorSuffix(int: size) -> float[]`

## HashedDoubleVectors

Construct an arbitrarily large vector with hashes. The initial value is assumed to be non-hashed, and is thus hashed on input to ensure that inputs are non-contiguous. Once the starting value is hashed, the sequence of long values is walked and each value added to the vector is hashed from the values in that sequence.

- `long -> HashedDoubleVectors(Object: sizer, Object: valueFunc) -> double[]`
  - *notes:* Build a double[] generator with a given size value or size function, and the given long->double function.
@param sizer Either a numeric type which sets a fixed dimension, or a long->int function to derive it uniquely for each input
@param valueFunc A long->double function


- `long -> HashedDoubleVectors(Object: sizer, double: min, double: max) -> double[]`

- `long -> HashedDoubleVectors(Object: sizer) -> double[]`

## HashedFloatVectors

Construct an arbitrarily large float vector with hashes. The initial value is assumed to be non-hashed, and is thus hashed on input to ensure that inputs are non-contiguous. Once the starting value is hashed, the sequence of long values is walked and each value added to the vector is hashed from the values in that sequence.

- `long -> HashedFloatVectors(Object: sizer, Object: valueFunc) -> float[]`
  - *notes:* Build a double[] generator with a given size value or size function, and the given long->double function.
@param sizer Either a numeric type which sets a fixed dimension, or a long->int function to derive it uniquely for each input
@param valueFunc A long->double function


- `long -> HashedFloatVectors(Object: sizer, double: min, double: max) -> float[]`

- `long -> HashedFloatVectors(Object: sizer) -> float[]`

## HdfFileToFloatArray

This function reads a vector dataset from an HDF5 file. The dataset itself is not read into memory, only the metadata (the "dataset" Java Object). The lambda function reads a single vector from the dataset, based on the long input value. As currently written this class will only work for datasets with 2 dimensions where the 1st dimension specifies the number of vectors and the 2nd dimension specifies the number of elements in each vector. Only datatypes short, int, and float are supported at this time.


This implementation is specific to returning an array of floats

- `long -> HdfFileToFloatArray(String: filename, String: datasetName) -> float[]`

## HdfFileToFloatList

This function reads a vector dataset from an HDF5 file. The dataset itself is not read into memory, only the metadata (the "dataset" Java Object). The lambda function reads a single vector from the dataset, based on the long input value. As currently written this class will only work for datasets with 2 dimensions where the 1st dimension specifies the number of vectors and the 2nd dimension specifies the number of elements in each vector. Only datatypes short, int, and float are supported at this time.


This implementation is specific to returning a List of Floats, so as to work with the
normalization functions e.g. NormalizeListVector and its variants.

- `long -> HdfFileToFloatList(String: filename, String: datasetName) -> List<Float>`

## HdfFileToIntArray

This function reads a vector dataset from an HDF5 file. The dataset itself is not read into memory, only the metadata (the "dataset" Java Object). The lambda function reads a single vector from the dataset, based on the long input value. As currently written this class will only work for datasets with 2 dimensions where the 1st dimension specifies the number of vectors and the 2nd dimension specifies the number of elements in each vector. Only datatypes short, int, and float are supported at this time.


This implementation is specific to returning an array of ints

- `long -> HdfFileToIntArray(String: filename, String: datasetName) -> int[]`

## HdfFileToIntList

This function reads a vector dataset from an HDF5 file. The dataset itself is not read into memory, only the metadata (the "dataset" Java Object). The lambda function reads a single vector from the dataset, based on the long input value. As currently written this class will only work for datasets with 2 dimensions where the 1st dimension specifies the number of vectors and the 2nd dimension specifies the number of elements in each vector. Only datatypes short, int, and float are supported at this time.


This implementation is specific to returning a List of Integers

- `long -> HdfFileToIntList(String: filename, String: datasetName) -> List<Integer>`

## HdfFileToLongArray

This function reads a vector dataset from an HDF5 file. The dataset itself is not read into memory, only the metadata (the "dataset" Java Object). The lambda function reads a single vector from the dataset, based on the long input value. As currently written this class will only work for datasets with 2 dimensions where the 1st dimension specifies the number of vectors and the 2nd dimension specifies the number of elements in each vector. Only datatypes short, int, and float are supported at this time.


This implementation is specific to returning an array of longs

- `long -> HdfFileToLongArray(String: filename, String: datasetName) -> long[]`

## HdfFileToLongList

This function reads a vector dataset from an HDF5 file. The dataset itself is not read into memory, only the metadata (the "dataset" Java Object). The lambda function reads a single vector from the dataset, based on the long input value. As currently written this class will only work for datasets with 2 dimensions where the 1st dimension specifies the number of vectors and the 2nd dimension specifies the number of elements in each vector. Only datatypes short, int, and float are supported at this time.


This implementation is specific to returning a List of Longs

- `long -> HdfFileToLongList(String: filename, String: datasetName) -> List<Long>`

## NormalizeCqlVector

Normalize a vector in List form, calling the appropriate conversion function depending on the component (Class) type of the incoming List values.

- `com.datastax.oss.driver.api.core.data.CqlVector -> NormalizeCqlVector() -> com.datastax.oss.driver.api.core.data.CqlVector`

## NormalizeDoubleListVector

Normalize a vector.

- `List<Double> -> NormalizeDoubleListVector() -> List<Double>`

## NormalizeDoubleVector


- `double[] -> NormalizeDoubleVector() -> double[]`

## NormalizeFloatListVector

Normalize a vector.

- `List<Float> -> NormalizeFloatListVector() -> List<Float>`

## NormalizeFloatVector


- `float[] -> NormalizeFloatVector() -> float[]`

## NormalizeListVector

Normalize a vector in List form, calling the appropriate conversion function depending on the component (Class) type of the incoming List values.

- `List -> NormalizeListVector() -> List`

## RepeatList

Repeat the incoming list into a new list, filling it to the given size.

- `List -> RepeatList(int: size) -> List`
  - *notes:* Create a list repeater to build up a list from a smaller list.
@param size - the total size of the new list

  - *example:* `RepeatList(50)`
  - *repeat the incoming values into a new List of size 50*

## ToCqlVector


- `double[] -> ToCqlVector() -> com.datastax.oss.driver.api.core.data.CqlVector`

- `float[] -> ToCqlVector() -> com.datastax.oss.driver.api.core.data.CqlVector`

- `List -> ToCqlVector() -> com.datastax.oss.driver.api.core.data.CqlVector`

## ToFloatVector


- `double[] -> ToFloatVector() -> float[]`

## TokenMapFileCycle

Utility function used for advanced data generation experiments.

- `int -> TokenMapFileCycle(String: filename, boolean: loopdata, boolean: ascending) -> long`

## TokenMapFileNextCycle

Utility function used for advanced data generation experiments.

- `int -> TokenMapFileNextCycle(String: filename, boolean: loopdata, boolean: ascending) -> long`

## TokenMapFileNextToken

Utility function used for advanced data generation experiments.

- `int -> TokenMapFileNextToken(String: filename, boolean: loopdata, boolean: ascending) -> long`

## TokenMapFileToken

Utility function used for advanced data generation experiments.

- `int -> TokenMapFileToken(String: filename, boolean: loopdata, boolean: ascending) -> long`

## TriangularStep

Compute a value which increases monotonically with respect to the cycle value.
All values for f(X+(m\>=0)) will be equal or greater than f(X). In effect, this
means that with a sequence of monotonic inputs, the results will be monotonic as
well as clustered. The values will approximate input/average, but will vary in frequency
around a simple binomial distribution.

The practical effect of this is to be able to compute a sequence of values
over inputs which can act as foreign keys, but which are effectively ordered.

### Call for Ideas

Due to the complexity of generalizing this as a pure function over other distributions,
this is the only function of this type for now. If you are interested in this problem
domain and have some suggestions for how to extend it to other distributions, please
join the project or let us know.

- `long -> TriangularStep(long: average, long: variance) -> long`
  - *example:* `TriangularStep(100,20)`
  - *Create a sequence of values where the average is 100, but the range of values is between 80 and 120.*
  - *example:* `TriangularStep(80,10)`
  - *Create a sequence of values where the average is 80, but the range of values is between 70 and 90.*

- `long -> TriangularStep(long: average) -> long`

## UniformVectorSizedStepped

Create a vector which consists of a number of uniform vector ranges. Each range is set as \[min,max\] inclusive by a pair of double values such as 3.0d, 5.0d, ... You may provide an initial integer to set the number of components in the vector. After the initial (optional) size integer, you may provide odd, even pairs of min, max. If a range is not specified for a component which is expected from the size, the it is automatically replaced with a unit interval double variate.

- `long -> UniformVectorSizedStepped(Number[]...: dims) -> List<Double>`
  - *example:* `UniformVectorSizedStepped(3)`
  - *create a 3-component vector from unit interval variates*
  - *example:* `UniformVectorSizedStepped(1.0d,100.0d,5.0d,6.0d)`
  - *create a 2-component vector from the specified uniform ranges [1.0d,100.0d] and [5.0d,6.0d]*
  - *example:* `UniformVectorSizedStepped(2,3.0d,6.0d)`
  - *create a 2-component vector from ranges [3.0d,6.0d] and [0.0d,1.0d]*

