# PATHS
cdec = /home/waziz/workspace/cdec

# Make the structure of directories
dirs:
	@mkdir -p lm
	@mkdir -p wa
	@mkdir -p grammars
	@mkdir -p tuning

# WORD ALIGNMENT

wa/training.zh-en.fwd: | dirs
	$(cdec)/word-aligner/fast_align -i data/training.zh-en -o -v -d > $@ 2> $@.log

wa/training.zh-en.bwd: | dirs
	$(cdec)/word-aligner/fast_align -i data/training.zh-en -o -v -d -r > $@ 2> $@.log

wa/training.zh-en.gdfa: wa/training.zh-en.fwd wa/training.zh-en.bwd | dirs
	$(cdec)/utils/atools -i wa/training.zh-en.fwd -j wa/training.zh-en.bwd -c grow-diag-final-and > $@

# LANGUAGE MODEL

lm/btec.lm4: | dirs
	$(cdec)/corpus/cut-corpus.pl 2 data/training.zh-en | $(cdec)/klm/lm/builder/lmplz --order 4 --interpolate_unigrams > $@ 

lm/btec.klm4: lm/btec.lm4
	$(cdec)/klm/lm/build_binary $< $@

# SUFFIX ARRAY

training.sa: wa/training.zh-en.gdfa
	python -m cdec.sa.compile -c extract.ini -b data/training.zh-en -a $< -o $@

# GRAMMARS

grammars/dev.sgm: training.sa | dirs
	python -m cdec.sa.extract -c extract.ini -g grammars/dev -j 30 -z < data/dev.zh-en > $@ 

grammars/tst.sgm: training.sa | dirs
	python -m cdec.sa.extract -c extract.ini -g grammars/tst -j 30 -z < data/tst.zh-en > $@

cdec.ini: lm/btec.klm4
	@echo "formalism=scfg\ncubepruning_pop_limit=200\nadd_pass_through_rules=true\nfeature_function=WordPenalty\nfeature_function=ArityPenalty\nfeature_function=KLanguageModel `pwd`/lm/btec.klm4" > $@

weights.init:
	@echo "PassThrough 0\nGlue 0\nWordPenalty 0\nArity_0 0\nArity_1 0\nArity_2 0\nEgivenFCoherent 0\nSampleCountF 0\nCountEF 0\nMaxLexFgivenE 0\nMaxLexEgivenF 0\nIsSingletonF 0\nIsSingletonFE 0\nLanguageModel 0\nLanguageModel_OOV 0" > $@

wa: wa/training.zh-en.gdfa

lm: lm/btec.klm4

sa: training.sa

dev: grammars/dev.sgm

tst: grammars/tst.sgm

grammars: dev tst

mira: grammars/dev.sgm grammars/tst.sgm cdec.ini weights.init | dirs
	$(cdec)/training/mira/mira.py -d grammars/dev.sgm -t grammars/tst.sgm -c cdec.ini -j 30 -o `pwd`/tuning/mira_1 -w weights.init > tuning/mira_1.stdout 2> tuning/mira_1.stderr

.PHONY: all
all: wa lm sa dev tst grammars mira | dirs

.PHONY: clean
clean:
	-rm -fr wa
	-rm -fr lm
	-rm -fr tuning
	-rm -fr grammars
	-rm -fr training.sa
	-rm -fr extract.ini cdec.ini weights.init
