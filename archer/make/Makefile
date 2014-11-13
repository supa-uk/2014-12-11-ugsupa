include config.mk

TXT_FILES=$(shell find books -type f -name '*.txt')
DAT_FILES=$(patsubst books/%.txt, %.dat, $(TXT_FILES))
JPG_FILES=$(patsubst books/%.txt, %.jpg, $(TXT_FILES))

.PHONY : dats
dats : $(DAT_FILES)

.PHONY : jpgs
jpgs : $(JPG_FILES)

# Count words.
%.dat : books/%.txt $(COUNT_SRC)
	$(COUNT_EXE) $< $@

# Plot word counts.
%.jpg : %.dat $(PLOT_SRC)
	$(PLOT_EXE) $< $@

analysis.tar.gz : *.dat *.jpg $(COUNT_SRC) $(PLOT_SRC)
	tar -czf $@ $^

clean : 
	rm -f analysis.tar.gz
	rm -f *.dat
	rm -f *.jpg
