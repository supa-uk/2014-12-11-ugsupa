include config.mk

TXT_FILES=$(shell find books -type f -name '*.txt')
DAT_FILES=$(patsubst books/%.txt, %.dat, $(TXT_FILES))
JPG_FILES=$(patsubst books/%.txt, %.jpg, $(TXT_FILES))

# Count words.
%.dat : books/%.txt $(COUNT_SRC)
	$(COUNT_EXE) $< $@

# Plot word counts.
%.jpg : %.dat $(PLOT_SRC)
	$(PLOT_EXE) $< $@

analysis.tar.gz : *.dat *.jpg $(COUNT_SRC) $(PLOT_SRC)
	tar -czf $@ $^

.PHONY : dats
dats : $(DAT_FILES)

.PHONY : jpgs
jpgs : $(JPG_FILES)

.PHONY : clean
clean : 
	rm -f *.jpg 
	rm -f *.dat 
	rm -f analysis.tar.gz
