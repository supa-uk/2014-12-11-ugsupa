---
layout: lesson
root: ../..
---

When 0.1 + 0.2 == 3.00000000004
-------------------------------

Question: what other problems might `diff` experience with data files?

Answer: floating point values.

    python
    a = 0.1
    b = 0.2
    print a + b
    print a + b == 0.3
    a + b

Simple tests for the equality of two floating point values are problematic due to imprecision in values.

Compare for equality within a given threshold, or delta e.g. *expected* and *actual* to be equal if *expected - actual < 0.000000000001*.

Python [nose](https://pypi.python.org/pypi/nose/) library includes functions for floating point equality.

    python
    from nose.tools import assert_almost_equal
    expected = 2
    expected = 2.000001
    actual = 2.0000000001
    assert_almost_equal(expected, actual, 0)
    assert_almost_equal(expected, actual, 1)
    assert_almost_equal(expected, actual, 2)
    assert_almost_equal(expected, actual, 3)
    assert_almost_equal(expected, actual, 4)
    assert_almost_equal(expected, actual, 5)
    assert_almost_equal(expected, actual, 6)

`nose.testing` uses absolute tolerance: abs(x, y) <= delta.

[Numpy](http://www.numpy.org/) `numpy.testing` uses relative tolerance: abs(x, y) <= delta * (max(abs(x), abs(y)). 

Question: why might they differ?

Answer: they were run on different numbers on processors e.g. 2x1, 4x2 etc.

What is a suitable threshold for equality? That is application-specific - for some domains round to the nearest whole number, for others be far, far more accurate.
