---
layout: lesson
root: ../..
---

## What is Testing?

<div class="objectives" markdown="1">
#### Objectives

*   Explain what testing is, and why it is useful
*   Identify some tests
*   Write out how you would test something
</div>


Lets start with some questions:

Question: how do you test? (post-its)
* Compile the code ("if it builds, ship it")?
* Run on correct inputs, check results by visual inspection?
* Run on correct inputs, check results automatically?
* Run tests after every change or bug fix?
* Use continuous integration?
* Document manual testing?
* Run on inputs known to cause failures?
* Run to ensure fixed bugs don't reoccur?

Question: if you don't test, why not? (post-its)
* "I don't write buggy code". Almost all code has bugs.
* "It's too hard". This can be a sign that the code is not well designed.
* "My code can't be tested". Why not?
* "It's not interesting"
* "It takes too much time and I've research to do"

Embarassing situations:
* [Geoffrey Chang](http://en.wikipedia.org/wiki/Geoffrey_Chang) had to retract 3 papers from [Science](http://www.sciencemag.org) due to a flipped sign bit.
* McKitrick and Michaels published an [erratum](http://www.int-res.com/articles/cr2004/27/c027p265.pdf) to a [Climate Research 26(2) 2004](http://www.int-res.com/abstracts/cr/v26/n2/p159-173/) paper due to a [problem](http://crookedtimber.org/2004/08/25/mckitrick-mucks-it-up/) caused by degrees and radians.
* Ariane 5 used Ariane 4 software. Ariane 5's new engines caused the code to produce a buffer overflow. Ariane 5 blew up!

## Benefits of Testing

Save money:
* Find a bug on your laptop for free before you submit a job to a charged-for HPC resource.
 
Save time:
* Spot bugs before you analyse data produced by your scripts.
* 1-10-100 rule.

Safety net:
* Fix bugs, optimise and parallelise without introducing (new) bugs.
* EPCC and Colon Cancer Genetics Group (CCGG) of MRC Human Genetics Unit at Western General Hospital Edinburgh optimised and parallelised FORTRAN genetics code.

Documentation:
* How to use, or not to use, scripts and code and what they do.

## A Couple of Terms and an Observation

Verification - "Have we built it correctly?" Is it bug free, precise, accurate, and repeatable?

Validation - "Have we built the right thing?" Is it designed in such a way as to produce the answers we are interested in, data we want, etc?

Finding bugs before testing

Question: what is single most effective way of finding bugs?

Answer: 
* Fagan (1976) Rigorous inspection can remove 60-90% of errors before the first test is run. M.E., Fagan (1976). [Design and Code inspections to reduce errors in program development](http://www.mfagan.com/pdfs/ibmfagan.pdf). IBM Systems Journal 15 (3): pp. 182-211.
* Cohen (2006) The value of a code review comes within the first hour, after which reviewers can become exhausted and the issues they find become ever more trivial. J. Cohen (2006). [Best Kept Secrets of Peer Code Review](http://smartbear.com/SmartBear/media/pdfs/best-kept-secrets-of-peer-code-review.pdf). SmartBear, 2006. ISBN-10: 1599160676. ISBN-13: 978-1599160672.

## What types of Test could we do

* End-to-End - run a program in full and evaluate the output and operation.
* Unit Testing - evaluate and test individual parts of a program code. For example does a function to filter inputs by a criteria work correctly? Defensive Programming

Which is better?

Neither - both help with testing and look at different ways of testing.


##Look at Travelling Salesman Problem

Our example will be a Travelling Salesman Problem - the code is written in Python (based on code by trevlovett (https://github.com/trevlovett)).

The travelling salesman problem can be defined as a mathematical problem in which one tries to find the shortest route that passes through each of a set of points once and only once.

This is an example of an NP-Hard problem (Non-deterministic Polynomial-time Hard) - at least as hard as the hardest problems in NP.

Optimization methods can be used to get a reasonable solution in a feasible time - but is not guarenteed to be the best or most optimal solution.

The example program uses a method called Ant Colony Optimization (http://en.wikipedia.org/wiki/Ant_colony_optimization_algorithms). This attempts to reduce a problem to finding good paths through a graph by using techniques based on ant behaviour.

What could be the problems:

* not guaranteed best answer
* not guaranteed same answer each time

Is this a problem: (ask question)

No - we can still check for the 'shape' of the answer and identify if it is reasonable, we can break up the code and give confidence in its structure.

Is this normal: (ask question)

In a lot of cases yes - any complex code will have a range where its correct operation will fall within.


## Look at The Code

In the cloned Git repository for the workshop, switch to the directory <pre>testing/ants</pre>

Assuming you were in the root of the repository:

~~~
$ cd testing/ants
~~~
{:class="in"}

In that directory you should have the following files:
~~~
./:
README
ant.py
antcolony.py
antgraph.py
anttsp.py
data
util
./data:
cities.csv
citiesAndDistances.pickled
ukcities.csv
./util:
__init__.py
__init__.pyc
plot_city_path.py
plot_city_path.pyc
sample_map.py
write_data_csv.py
write_data_pickle.py
~~~
{:class="out"}

Take a few minutes - look over the code - try to figure out what it is doing.

Before you try running it, you will need to generate some input data.

To do this, we need to use the write_data_pickle.py and this needs the argparse package:
~~~
$ pip install argparse
~~~
{:class="in"}

If the package is not installed this will ask you to confirm the install command, if the package is present it will tell you.

Now run the write_data_pickle:

~~~
$ python write_data_pickle.py <city data csv> <pickle output file>
~~~
{:class="in"}

Now try running the solution program:

~~~
$ python anttsp.py <number of cities> <city data file> <output file>
~~~
{:class="in"}

Now lets discuss the output.

Ask about what code is like - the running of it, the structure and other issues:
* poor structure in places
* comments are not brilliant
* poor naming
* what else?

This is a common scenario, being given a code which is not in the best of shape, having no tests. 
A contrast to where you start writing something and follow a test-driven development approach.

How could we test this:

* End to End - does it use all the points? does it add up correctly?
* Input/Output Testing - creation and consumption
* Breaking Up the Code - we'll come to later

Even if the testing you do is to create a harness which runs the code and checks the output against known values or criteria, 
this is valuable as between that and writing new additions with tests, you can reduce the likelihood of new errors being introducted.

Before moving onto the next part - the last challenge here is to write down a test (in plain language).

Choose something about the program code and write down a test:
* what are you testing
* what data do you need
* what are the pass or fail criteria
* what test steps will be followed
* what known exceptions are present

These do not have to be perfect, indeed testing is an iterative and responsive process which will build up over time.

Is there anyone willing to give a summary of their test?

## What Comes Next

* Writing a test in Python
* Breaking up code
* Choosing Wisely
