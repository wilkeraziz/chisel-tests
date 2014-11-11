# Data

Parallel training data consists of 44k sentences from the tourism and travel domain.  
Development and test data were distributed as part of the IWSLT evaluation.

    * dev: dev1 and dev2
    * tst: dev3

# Preparation

Chinese was automatically segmented using a conditional random field word segmenter trained on text segmented using the Chinese Treebank (CTB) segmentation style.  
Traditional characters were converted to their simple forms and punctuation was normalized to western variants.

# Citations 

Citation for the corpus:

@inproceedings{btec,
  Address = {Las Palmas, Spain},
  Author = {Toshiyuku Takezawa and Eiichiro Sumita and Fumiaki Sugaya and Hirofumi Yamamoto and Seiichi Yamamoto},
  Booktitle = {Proceedings of LREC 2002},
  Pages = {147--152},
  Title = {Toward a broad-coverage bilingual corpus for speech translation of travel conversations in the real world},
  Year = 2002
}

Citation for the Chinese segmenter:

@inproceedings{tseng:2005,
  Author = {Huihsin Tseng and Pi-Chuan Chang and Galen Andrew and Daniel Jurafsky and Christopher Manning},
  Booktitle = {Fourth SIGHAN Workshop on Chinese Language Processing},
  Title = {A Conditional Random Field Word Segmenter},
  Year = 2005
}

