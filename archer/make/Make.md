Automation and Make
===================

Question: what are the problems with this?

    cc -o scheduler main.o schedule.o optimise.o io.o utils.o -I./include -L./lib -lm -lblas -llapack -lrng

Answer: we need to

* Type a lot.
* Remember syntax, flags, inputs, libraries, dependencies.
* Ensure .o files have been created.

Automation allows us to "write once, run many":

* Less typing of the same commands over and over.
* Documents syntax, flags, inputs, libraries, dependencies.
* Recreates files (e.g. binaries, output data, graphs) only when needed.
  - Input files => process => output files.
  - Source code => compiler => library or executable.
  - Configuration and data files => analysis program => data files.
  - Data files => visualisation program => images.

Make:

* A widely-used, fast, free, well-documented, build tool.
* Developed by Stuart Feldman:
  - From Bell Labs 1977 summer intern. 
  - to Vice President of Computer Science at IBM Research and Google ACM Software System Award winner, 2003.

Other build tools:

* Apache ANT designed for Java.
* Python doit.
* CMake and Autoconf/Automake generate platform-dependent build scripts e.g. Make, Visual Studio project files etc.

Data processing pipeline
------------------------

Suppose we have scripts that implement a common workflow:

* Read data files e.g. a text file.
* Perform an analysis e.g. count the number of occurrences of each word in the file.
* Writes the results to a file e.g. a file with each word and its number of occurrences.
* Plots the data e.g. a graph of the most frequently occurring words.
* Writes the graph to a file e.g. a PDF or a JPG.

Count words in a text file:

    python wordcount.py books/isles.txt isles.dat
    head isles.dat
    python wordcount.py books/abyss.txt abyss.dat
    head abyss.dat

Count words in a text file, whose length is >= 12 characters:

    python wordcount.py books/isles.txt isles.dat 12
    head isles.dat

Plot top 10 most frequently occuring words:

    python plotcount.py isles.dat show
    python plotcount.py abyss.dat show

Plot top 5 most frequently occuring words:

    python plotcount.py isles.dat show 5
    python plotcount.py abyss.dat show 5

Plot top 5 most frequently occuring words and save as a JPG:

    python plotcount.py isles.dat isles.jpg 5

Makefile
--------

    python wordcount.py books/isles.txt isles.dat
    head isles.dat

Let's pretend we update one of the input files. We can use `touch` to update its timestamp:

    touch books/isles.txt
    ls -l books/isles.txt isles.dat

Output file `isles.dat` is now older than input `books/isles.txt`, so we need to recreate it.

Question: we could write a shell script but what might be the problems?

Answer: if we have many source files to compile or data files to analyse, we don't want to recreate everything, just those outputs that depend on the changed files.

Create a `Makefile`:

    # Calculate word frequencies.
    isles.dat : books/isles.txt
	    python wordcount.py books/isles.txt isles.dat
 
Add information on the Makefile's structure as comments:

    # Make comments
    # target: dependency1 dependency1 dependency2 ...
    # TAB rule1
    # TAB rule2
    # TAB rule3
    # TAB ...

Make terminology:

* Target - a 'thing' to be built.
* Dependencies - other 'things' that 'thing' depends upon.
* Actions - commands to run to build the target ('thing'), or update it.
  - Actions are indented using TAB, not 8 spaces. 
  - A legacy of Make's 1970's origins.

To use the default Makefile:

    make

To use a named Makefile:

    make -f Makefile

Question: why did nothing happen?

Answer: the target is now up-to-date and newer than its dependency. Make uses a file's 'last modification time'.

Let's add another target:

    abyss.dat : books/abyss.txt
        python wordcount.py books/abyss.txt abyss.dat

And, run Make:

    make
    touch books/abyss.txt
    make

Nothing happens as the first, default, target in the makefile, is used. We can explicitly state which target to build:

    make abyss.dat

Let's add a target to allow us to build both data files:

    .PHONY : all
    all : isles.dat abyss.dat

`all` is not a file or directory but depends on files and directories, so can trigger their rebuilding. It is a 'phony' target.

A dependency in one rule can be a target in another.

We can now use this phony target:

    make all
    touch books/isles.txt books/abyss.txt
    make all

The order of rebuilding dependencies is arbitrary.

Dependencies must make up a directed acyclic graph.

Exercise 1 - add a rule 
-----------------------

See [exercises](MakeExercises.md).

Solution:

    last.dat : books/last.txt
        python wordcount.py books/last.txt last.dat

    all : isles.dat abyss.dat last.dat

Patterns
--------

Let's add a rule to create an archive with all the data files:

    analysis.tar.gz : isles.dat abyss.dat last.dat
        tar -czf analysis.tar.gz isles.dat abyss.dat last.dat

<p/>

    make analysis.tar.gz

Makefiles are code. Repeated code creates maintainability issues. So let's rewrite the rule as:

    tar -czf $@ isles.dat abyss.dat last.dat

`$@` is a Make 'special macro' which means 'the target of the current rule'. Let's add a comment to remind ourselves:
 
    # Make's special macros and notation:
    # $@ Target of the current rule.

We still have duplication - the name of the target within the rule. So let's rewrite the rule further:

    tar -czf $@ $^

`$^` is a Make special macro which means 'all the dependencies of the current rule'. Let's add this as a comment:

    # $^ All dependencies of the current rule.

We can use the bash wild-card in our dependency list:

    analysis.tar.gz : *.dat

Now, let's check it still works:

    make analysis.tar.gz
    touch *.dat
    make analysis.tar.gz

Now let's delete the data files and recreate them:

    rm *.dat
    make analysis.tar.gz

Question: any guesses as to why this now fails?

Answer: there are no files that match `*.dat` so the name `*.dat` is used as-is.

We need to explicitly recreate them:

    make isles.dat abyss.dat last.dat

Dependencies on data and code
-----------------------------

Output data depends on both input data and programs that create it:

    isles.data : books/isles.txt wordcount.py
    ...
    abyss.dat : books/abyss.txt wordcount.py
    ...
    last.dat : books/last.txt wordcount.py
    ...

Let's recreate them all:

    touch wordcount.py
    make all

`.txt` files are input files and have no dependencies. To make these depend on `wordcount.py` would introduce a 'false dependency'.

Let's add our analysis script to the archive too:

    analysis.tar.gz : *.dat wordcount.py
	tar -czf $@ $^

Pattern rules
-------------

Question: our Makefile still has repeated content. Where?

Answer: the rules for each .dat file.

Let's replace the rules with a single 'pattern rule':

    %.dat : books/%.txt wordcount.py

`%` is a Make wild-card. Let's add this as a comment:

    # % - Make wild-card

Exercise 2 - simplify a rule 
----------------------------

See [exercises](MakeExercises.md).

You will need another special macro, `$<` which means 'the first dependency of the current rule'. Add this as a comment:

    # $< First dependency of the current rule.

Solution: 

    %.dat : books/%.txt wordcount.py
	    python wordcount.py $< $@

Macros
------

Question: there's still duplication in our makefile, where?

Answer: the program name. Suppose the name of our program changes?

    COUNT=wordcount.py

Question: is there an alternative to this?

Answer: we might change our programming language or the way in which our command is invoked, so we can instead define:

    COUNT_SRC=wordcount.py
    COUNT_EXE=python $(COUNT_SRC)

Exercise 3 - use a macro
------------------------

See [exercises](MakeExercises.md).

Solution:

    COUNT_SRC=wordcount.py
    COUNT_EXE=python $(COUNT_SRC)

    # Count words.
    %.dat : books/%.txt $(COUNT_SRC)
        $(COUNT_EXE) $< $@

    analysis.tar.gz : *.dat $(COUNT_SRC)
        tar -czf $@ $^

Keeping macros at the top of a Makefile means they are easy to find and modify. Alternativey, we can pull them out into a configuration file, `config.mk`:

    # Count words script.
    COUNT_SRC=wordcount.py
    COUNT_EXE=python $(COUNT_SRC)

We can then import these into our Makefile using:

    include config.mk

This is an example of food programming practice:

* It separates code from data.
* There is no need to edit code which reduces the risk of introducing a bug.
* Code that is configurable is more modular, flexible and reusable.

What make will do
-----------------

We can use `-n` to see what make would do - the commands it will run - without it actually running them:

    touch books/*.txt
    make -n analysis.tar.gz

Exercise 4 - add another processing stage
-----------------------------------------

See [exercises](MakeExercises.md).

Solution:

Makefile, `Makefile`:

    # Plot word counts.
    %.jpg : %.dat $(PLOT_SRC)
        $(PLOT_EXE) $< $@

    analysis.tar.gz : *.dat *.jpg $(COUNT_SRC) $(PLOT_SRC)
        tar -czf $@ $^

    clean : 
        rm -f analysis.tar.gz
        rm -f *.dat
        rm -f *.jpg

Configuration file, `config.mk`:

    # Plot word counts script.
    PLOT_SRC=plotcount.py
    PLOT_EXE=python $(PLOT_SRC)

shell and patsubst
------------------

Make supports many commands. `shell` allows us to run a shell command and save the output in a variable:

    TXT_FILES=$(shell find books -type f -name '*.txt')

`patsubst` allows us to substitute patterns in files e.g. change one suffix to another:

    DAT_FILES=$(patsubst books/%.txt, %.dat, $(TXT_FILES))
    JPG_FILES=$(patsubst books/%.txt, %.jpg, $(TXT_FILES))

    .PHONY : dats
    dats : $(DAT_FILES)

    .PHONY : jpgs
    jpgs : $(JPG_FILES)

Parallel jobs
-------------

Make can run on multiple cores if available:

    make -j 4 analysis.tar.gz

Conclusion
----------

See [the purpose of Make](MakePurpose.png).

Automated build scripts help us in a number of ways. They:

* Automate repetitive tasks.
* Reduce errors that we might make if typing commands manually.
* Document how software is built, data is created, graphs are plotted, papers are composed.
* Document dependencies between code, scripts, tools, inputs, configurations, outputs.

Automated scripts are code so:

* Use meaningful variable names.
* Provide comments to explain anything that is not clear.
* Separate configuration from computation via the use of configuration files.
* Keep under revision control.
