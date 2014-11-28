---
layout: lesson
root: ../..
---

## Breaking up into Smaller Tests

Exercise 3 - propose unit tests
------------------------------------------------------------

In groups come up with some possible unit tests for any of the code files in the problem.

Some example solutions (there are many more)

'etha'

* A known graph is initialised with a given distance matrix
* Check that the output of etha is equal to the value of 1/delta where delta is calculated independently

'average'

* A known graph is initialised with a given distance matrix
* Check that the average values calculated by the function is equal to a pre-calculated value
* Check what happens if a graph is created with a mix of integer and float

'__init__'

* check that a graph is created correctly for a given input
* check that the functions operate correctly?

A unit test for 'average'
----------------------

Create 'test_antgraph.py':

	from antgraph import AntGraph

Python [nose](https://pypi.python.org/pypi/nose/) library includes tests for equality, inequality, boolean values, thrown exceptions etc.
	
	from nose.tools import assert_equal
        import numpy as np

	def test_ones_distance_etha():
		distances = [[1 for col in range(3)] for row in range(3)]
                graph = AntGraph(3, distances)
                assert_equals(graph.etha(0,1), 1)

        def test_general_distance_etha():
                distances = [[0, 5, 4], [5, 0, 2],[4, 2, 0]]
                graph = AntGraph(3, distances)
		np.testing.assert_allclose(0.2, graph.etha(0,1), rtol=0, atol=0.01)

Running tests:

`nosetests` automatically finds, runs and reports on tests.

    nosetests test_cfd.py

`.` denotes successful test function calls.

Uses 'reflection' to find out the test functions - `test_` function, module and file prefixes.

Note - these are very simple tests for something that could get quite complicated, it is good to start with some simple cases and build up.


Exercise - implement unit tests for the Travelling Salesman Problem Code
--------------------------------------------------------------------------

Choose a part of the travelling salesman problem - write some unit tests.

Start by writing out on paper what you are going to do.

Sanity Check it with someone.

Try writing the tests 
	- don't worry if things go wrong 
	- stay calm, try to work through it, ask someone (or more likely tell someone what is going on and you'll figure it out yourself)
	
