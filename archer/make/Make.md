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
* Developed by Stuart Feldman, when a Bell Labs 1977 summer intern. Now Vice President of Computer Science at IBM Research and Google ACM Software System Award, 2003.

Other build tools:

* Apache ANT - designed for Java.
* Python doit.
* Platform independent build tools like CMake or Autoconf/Automake - these generate platform-dependent build scripts e.g. Make, Visual Studio project files etc.

Data processing pipeline
------------------------

Suppose we have a scripts that takes some input files, do some analysis and plots the results. For example, counting the words in a text file and plotting the number of times each word occurs.

Count words in a text file:

    python wordcount.py books/war.txt war.dat     # All words
    head war.dat
    python wordcount.py books/abyss.txt abyss.dat
    head abyss.dat

    python wordcount.py books/war.txt war.dat 12  # Words >= 12 characters
    head war.dat

Plot word counts:

    python plotcount.py war.dat show              # Plot top 10 words
    python plotcount.py abyss.dat show

    python plotcount.py war.dat show N            # Plot top N words
    python plotcount.py abyss.dat show N

    python plotcount.py war.dat war.jpg           # Plot top 10 and save as JPG
    python plotcount.py war.dat war.jpg 5

Makefile
--------

    python wordcount.py books/war.txt war.dat
    head war.dat

Let's pretend we've updated one of the text files, using `touch` to update its timestamp:

    touch books/war.txt
    ls -l books/war.txt war.dat

Output file `war.dat` is now older than input `books/war.txt`, so we need to update it.

Question: we could write a shell script but what might be the problems?

Answer: if we have many sources to compile or data files to analyse, we don't want to update everything, just those that need updated.

Create `Makefile`:

    # Calculate word frequencies.
    war.dat : books/war.txt
	    python wordcount.py books/war.txt war.dat
 
Add Makefile format information as comments:

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

<p/>

    make              # Use default Makefile
    make -f Makefile  # Use named makefile
    make

Question: why did nothing happen?

Answer: the target is now up-to-date and newer than its dependency. Make uses a file's 'last modification time'.

    abyss.dat : books/abyss.txt
        python wordcount.py books/abyss.txt abyss.dat

`touch` updates a file's time-stamp which makes it look as if it's been modified.

    touch books/abyss.txt
    make

Nothing happens as the first, default, rule in the makefile, is used.

    make abyss.dat

Phony targets:

    .PHONY : all
    all : war.dat abyss.dat

`all` is not a file or directory but depends on files and directories, so can trigger their rebuilding.

A dependency in one rule can be a target in another.

    make all
    touch books/war.txt books/abyss.txt
    make all

Order of rebuilding dependencies is arbitrary.

Dependencies must make up a directed acyclic graph.

Exercise 1 - add a rule 
-----------------------

See [exercises](MakeExercises.md).

Solution:

    bridge.dat : books/bridge.txt
        python wordcount.py books/bridge.txt bridge.dat

    all : war.dat abyss.dat bridge.dat

Patterns
--------

    analysis.tar.gz : war.dat abyss.dat bridge.dat
        tar -czf analysis.tar.gz war.dat abyss.dat bridge.dat

<p/>

    make analysis.tar.gz

Makefiles are code. Repeated code creates maintainability issues. 

    tar -czf $@ war.dat abyss.dat bridge.dat

<p/>

    # Make's special macros and notation:
    # $@ Target of the current rule.

<p/>

    tar -czf $@ $^

<p/>

    # $^ All dependencies of the current rule.

Bash wild-card can be used in file names:

    analysis.tar.gz : *.dat

<p/>

    make analysis.tar.gz
    touch *.dat
    make analysis.tar.gz

    rm *.dat
    make analysis.tar.gz

Question: any guesses as to why this is?

Answer: there are no files that match `*.dat` so the name `*.dat` is used as-is.

    make war.dat abyss.dat bridge.dat

Dependencies on data and code
-----------------------------

Output data depends on both input data and programs that create it:

    war.data : books/war.txt wordcount.py
    ...
    abyss.dat : books/abyss.txt wordcount.py
    ...
    bridge.dat : books/bridge.txt wordcount.py
    ...

<p/>

    touch wordcount.py
    make all

`.txt` files are input files and have no dependencies. To make these depend on `wordcount.py` would introduce a 'false dependency'.

Pattern rules
-------------

Question: Makefile still has repeated content. Where?

Answer: the rules for each .dat file.

    %.dat : books/%.txt wordcount.py

    # % - Make wild-card

Exercise 2 - simplify a rule 
----------------------------

See [exercises](MakeExercises.md).

You will need another special macro:

    # $< First dependency of the current rule.

Solution: 

    %.dat : books/%.txt wordcount.py
	    python wordcount.py $< $@

Macros
------

    analysis.tar.gz : *.dat wordcount.py
	tar -czf $@ $^

Question: there's still duplication in our makefile, where?

Answer: the program name. Suppose the name of our program changes?

    COUNTER=wordcount.py

Question: is there an alternative to this?

Answer: we might change our programming language or the way in which our command is invoked:

    COUNTER_SRC=wordcount.py
    COUNTER_EXE=python $(COUNTER_SRC)

Exercise 3 - use a macro
------------------------

See [exercises](MakeExercises.md).

Solution:

    COUNTER_SRC=wordcount.py
    COUNTER_EXE=python $(COUNTER_SRC)

    # Calculate word frequencies.
    %.dat : books/%.txt $(COUNTER_SRC)
        $(COUNTER_EXE) $< $@

    analysis.tar.gz : *.dat $(COUNTER_SRC)
        tar -czf $@ $^

Keep macros at the top of a Makefile so they are easy to find, or move to `config.mk`:

    # Word frequency calculations.
    COUNTER_SRC=wordcount.py
    COUNTER_EXE=python $(COUNTER_SRC)

<p/>

    include config.mk

Good programming practice:

* Separate code from data.
* No need to edit code which reduces risk of introducing a bug.
* Code that is configurable is more modular, flexible and reusable.

What make will do
-----------------

    touch books/*.txt
    make -n analysis.tar.gz  # Display commands make will run

Exercise 4 - add another processing stage
-----------------------------------------

See [exercises](MakeExercises.md).

Solution:

Makefile, `Makefile`:

    # Calculate images
    %.jpg : %.dat $(PLOT_SRC)
        $(PLOT_EXE) $< $@

    analysis.tar.gz : *.dat *.jpg $(COUNT_SRC) $(PLOT_SRC)
        tar -czf $@ $^

    clean : 
        rm -f analysis.tar.gz
        rm -f *.dat
        rm -f *.jpg

Configuration file, `config.mk`:

    # Image output calculations
    PLOT_SRC=plotcount.py
    PLOT_EXE=python $(PLOT_SRC)

shell and patsubst
------------------

Avoid hard-coding file names:

    TXT_FILES=$(shell find books -type f -name '*.txt')
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

Build scripts:

* Automate repetitive tasks.
* Reduce errors.
* Document how software is built, data is created, graphs are done, papers formed.
* Document dependencies between code, scripts, tools, inputs, configurations, outputs.
* Are code so use meaningful variable names, comments, and separate configuration from computation.
* Should be kept under revision control.
