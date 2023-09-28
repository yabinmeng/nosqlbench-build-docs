---
weight: 52584703
title: DoubleVectorPadLeft
---
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

## FloatVectors

This is the float version of the DoubleVector function. To facilitate a direct relationship between the double values from that function and the float values from this, this one defers to the double version for primary data. This function essentially wraps and converts the results from the DoubleVector function.

- `long -> FloatVectors(String: spec) -> float[]`

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

## ToFloatVector


- `double[] -> ToFloatVector() -> float[]`

