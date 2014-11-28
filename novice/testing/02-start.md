---
layout: lesson
root: ../..
---

## Introducing tests from the outside-in

* Unit tests test small individual functions.
* Researchers inherit large codes, which may not have any tests. 
* Where to start with a unit test?
* Evolve tests from the outside-in.

End-to-end tests
----------------

Summarise an end to end test. 

Determine a flow pattern:

    python write_data_pickle.py scottish_city_distances.csv scottish_city_distances.pickled
    head scottish_city_distances.pickled
    python anttsp.py 7 data/scottish_city_distances.pickled scottish_path.pickled
    head scottish_path.pickled

Question: what is possibly the simplest test we could do? 

Answer: check there is an output file produced for a valid input file

Use Python to write end-to-end tests. Program being tested does not have to be Python. Can use the same approach using shell scripts.

Create `test_anttsp_end_to_end.py`:

    import os
    import os.path

    def file_exists(filename):
      if (os.path.isfile(filename)):
        print "OK ", filename, "exists"
      else:
        print "FAIL ", filename, "does not exist"

    for f in os.listdir("."):
      if f.endswith(".dat"):
        os.remove(f)

    print "Test Scottish Cities 7 node path"
    os.system("python anttsp.py 7 data/scottish_city_distances.pickled scottish_path.pickled")
    file_exists("scottish_path.pickled")

Use functions as these commands will be called more than once - anticipate reuse.

    python test_anttsp_end_to_end.py

<p/>

    print "Test Scottish Cities 4 node path"
    os.system("python anttsp.py 7 data/scottish_city_distances.pickled scottish_path_4.pickled")
    file_exists("scottish_path_4.pickled")

Tests that code, or functions, work with valid inputs are complemented by tests that they fail with invalid inputs.

Exercise - write a test for no file
-------------------------------------

Solution:

    def file_not_exists(filename):
      if (os.path.isfile(filename)):
        print "FAIL ", filename, "exists"
      else:
        print "OK ", filename, "does not exist"

    print "Test none"
    os.system("python anttsp.py 7 data/none.pickled no_path.pickled")
    file_not_exists("none.dat")


Question - is this enough to check the program is working?

Answers?

No - haven't checked that the form/shape of the result is correct.

Exercise - propose and implement a test for checking the form of the data
-------------------------------------------------------------

Remember we can't check the result against a known value for this program in most cases.

How can we check it?

Solution:
   import pickle
   import sys

   from nose.tools import assert_equal

   def confirm_path_form(filename, path_length):
	    result = pickle.load(open(filename, "r"))
            best_path_nodes = result[0]
            best_path_names = result[1]
            best_path = result[2]
            assert_equal(len(best_path_nodes), path_length)
            assert_equal(len(best_path_names), path_length)
            assert_equal(len(set(best_path_nodes)), path_length)	


Writing it all together
-----------------------

Now we need to write the tests into a coherent format for the end to end work.

A problem - how do we specify the data files to work with, both input data and expected data?

We can't just run once and say things worked - we have to write checks across a range of possible input types and failure targets.

We could put all our source test data into a test_data directory and in this case we don't have expected data but we do know the form.

We can use our confirm_path_form function to check the outputs where an output file is created and have seperate tests for cases where output
was not created.

Reduce duplicated code and loop over files using a configuration file (note we use a config file because one of the inputs to the program is
dependent on the number of cities represented in the test data):

    file = open(<test_file>, 'r')
    for line in file:
        test_config = line.split()
        output = test_config[0] + ".out"
        prepare = "python anttsp.py " + test_config[1] + test_config[0] + " " + output
        os.system(cmd)
        file_exists(output)        
        confirm_path_form(output,path_length)

<test_file> is replaced either with a literal value or by an variable.

Walkthrough of putting the end-to-end test file together for the overall travelling salesman problem.
