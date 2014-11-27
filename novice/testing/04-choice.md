---
layout: lesson
root: ../..
---

## Making Choices about What and How Much

How far do we break things up?

Do we test each individual thing exhaustively?

When to implement tests?

- Discuss this

Get feedback from the room and come up with what people think is a reasonable and feasible approach to testing

Introduce automation

Automated testing jobs
----------------------

Automated tests can be run:

* Manually.
* At regular intervals.
* Every time code is commited to revision control.

A simple automatic triggering of automated tests is via a Unix `cron` job.

A more advanced approach is via a continuous integration server. These trigger automated test runs and publish the results.

* [Muon Ion Cooling Experiment](http://www.mice.iit.edu/) (MICE) have a large number of tests written in Python. They use [Jenkins](http://jenkins-ci.org/) to build their code and trigger the running of the tests which are then [published online](http://test.mice.rl.ac.uk/).
* [Apache Hadoop Common Jenkins dashboard](https://builds.apache.org/job/Hadoop-Common-trunk/)

Tests are code
--------------

Review tests and avoid tests that:

* Pass when they should fail, false positives.
* Fail when they should pass, false negatives.
* Don't test anything. 

<p/>

    def test_critical_correctness():
      # TODO - will complete this tomorrow!
      pass

Conclusion
----------

Tests:

* Set-up expected outputs given known inputs.
* Run component on known inputs.
* Check if actual outputs match expected outputs - values if possible, form otherwise.

When to test:

* Always!
* Early. Don't wait till after the code's been used to generate data for an important paper, or been given to someone else.
* Often. So any bugs can be identified a.s.a.p. Bugs are easier to fix if they're identified at the time the relevant code is being actively developed.
* Turn bugs into assertions or tests. Check that bugs do not reappear.

When to finish:

* "It is nearly impossible to test software at the level of 100 percent of its logic paths", fact 32 in R. L. Glass (2002) [Facts and Fallacies of Software Engineering](http://www.amazon.com/Facts-Fallacies-Software-Engineering-Robert/dp/0321117425) ([PDF](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.94.2037&rep=rep1&type=pdf)).
* But, no excuse for not testing anything.
* When do you finish proof reading a paper? Learn from experience. 

"If it's not tested, it's broken" - Bruce Eckel, in [Thinking in Java, 3rd Edition](http://www.mindview.net/Books/TIJ/).

["Testing is science"](http://maori.geek.nz/post/testing_your_code_is_doing_science) - Graham Jenson.
