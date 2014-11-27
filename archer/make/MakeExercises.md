Automation and Make Exercises
=============================

Exercise 1 - add a rule 
-----------------------

Add in a rule for `last.dat`, based on the rules for `isles.dat` and
`abyss.dat`.

Be sure to use a single tab when indenting the actions, not spaces!

`touch` all the `books/*.txt` files.

Re-run `make` and all the `.dat` files should be rebuilt.

Exercise 2 - rewrite the action
-------------------------------

Rewrite the of the `%.dat` target so that it works. Use the following special macros:

* `$@` is the target of the current rule.
* `$<` is the first dependency only.

Exercise 3 - use a macro
------------------------

Replace duplicated occurrences of `python wordcount.py` with the
macros `$(COUNT_SRC)` and `$(COUNT_EXE)`.

Exercise 4 - add another processing stage
-----------------------------------------

Define macros to hold `plotcount.py` and the command `python
plotcount.py`.

Add a pattern rule to create `.jpg` files from `.dat` files.

Add a rule with a phony target, `jpgs`, to create all the `.jpg`
files.

Modify the `analysis.tar.gz` rule to add the `.jpg` files to the `.gz`
file. 

Add a `clean` rule to remove `.jpg` and `.dat` files and
`analysis.tar.gz`.
