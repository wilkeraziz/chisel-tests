# TED example

## Data

Chinese-English (zh-en) TED talks available from [WIT3](http://wit3.fbk.eu).


* Chinese segmentation: [jieba](https://pypi.python.org/pypi/jieba)
* English tokenization and lowercasing: [cdec](https://github.com/redpony/cdec/blob/master/corpus/tokenize-anything.sh).
* word alignment: [fast align](https://github.com/clab/fast_align)

## Model

1. Compile a suffix array representation of the corpus

    python -m cdec.sa.compile -b corpus/training.zh-en -a model/training.gdfa -o model/training.sa > extract.ini

2. Extract SCFGs for the dev and test sets

    cat corpus/dev2010.zh-en | python -m cdec.sa.extract -c extract.ini -g grammars/dev2010 -j10 -z > grammars/dev2010.sgm
    cat corpus/tst2010.zh-en | python -m cdec.sa.extract -c extract.ini -g grammars/tst2010 -j10 -z > grammars/tst2010.sgm

3. Train a 3-gram LM

    cat corpus/training.zh-en | $cdec/corpus/cut-corpus.pl 2 > lm/ted.en
    $kenlm/bin/lmplz -o 3 -T ~/tmp/lm --prune 0 0 1 --text lm/ted.en --arpa lm/ted.arpa
    $kenlm/bin/build_binary lm/ted.arpa lm/ted.klm

4. Estimate parameters 

    * with MIRA
        
        $cdec/training/mira/mira.py -d corpus/dev2010.zh-en -c cdec.ini  -w weight.init -j 20 -o tuning/mira -t corpus/tst2010.zh-en -k 500 --step-size 0.01 -g grammars/dev2010/grammar

    * with minimum risk training

        $cdec/training/minrisk/minrisk.pl  --source-file tuning/dev2010.zh.sgm --ref-files tuning/dev2010.en --weights weight.init --dont-accumulate --metric IBM_BLEU --workdir `pwd`/tuning/minrisk --jobs 20 cdec.ini
