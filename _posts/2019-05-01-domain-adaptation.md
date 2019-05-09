---
title: "Domain Adaptation for Syntactic Analysis"
hidden: true
last_modified_at: 2019-05-09
tag: NLP Tasks
author: Zi Lin
date: 2019-05-01
---
# Introduction
Current NLP systems tend to perform well only on their training domain and nearby genres, while the performances often degrade on the data drawn from different domains.

Recently, many endeavors have been made to explore and solve the problems of domain adaptation, ranging from creating challenging datasets to building algorithm that can avoid overfitting on the training data or transfer to the new domains. Here we will discuss several datasets build for the out-of-domain NLP task of syntactic analysis as well as some ideas on how to address the problem of domain adaptation. Note that in the literature, many definitions of the source domain and the target domain are proposed, here domain adaptation refers to the domains within the same NLP task and language (mainly English), but for other genres and domains such as emails, web forums and biomedical papers in terms of the dominant source domain of the Penn Treebank of Wall Street Journal (WSJ, financial news).


# Datasets

- **[Brown, ATIS & Switchboard of Treebank-3](<https://catalog.ldc.upenn.edu/LDC2009T26>)**: The Treebank-3 released by LDC also contains different domains other than WSJ: (1) the Brown Corpus (domain of literature), which has been completely retagged using the Penn Treebank tag set; (2) ATIS, the data from Department of Energy abstracts; (3) Switchboard, a collection of spontaneous telephone conversations between previously unacquainted speakers of American English on a variety of topics chosen from a pre-determined list.

- **[English Web Treebank (EWT)](<https://catalog.ldc.upenn.edu/LDC2012T13>)**: English Web Treebank was developed by the Linguistic Data Consortium (LDC) with funding from Google Inc. It contains 254,830 word-level tokens and 16,624 sentence-level tokens of webtext in 1174 files annotated for sentence- and word-level **tokenization,** **part-of-speech**, and **syntactic structure**. The data is roughly evenly divided across five genres: weblogs, newsgroups, email, reviews, and question-answers. The files were manually annotated following the sentence-level tokenization guidelines for web text and the word-level tokenization guidelines developed for English treebanks.

- **[Tweebank](<https://github.com/Oneplus/Tweebank>)**: Tweebank v2 is a collection of English tweets annotated in **Universal Dependencies** that can be exploited for the training of NLP systems to enhance their performance on social media texts. It is built on the original data of Tweebank v1 (840 unique tweets, 639/201 for training/test set), along with an additional 210 tweets sampled from the **POS-tagged** dataset of Gimpel et al. (2011) [^1] and 2,500 tweets sampled from the Twitter stream from Feb. 2016 to Jul. 2016. The latter data source consists of 147.4M English tweets. In the same way as Kong et al. (2011)[^2], reference unit is always the tweet in its entirety -- which may thus consist of multiple sentences -- not the sentence alone.

- **[QuestionBank](<https://www.computing.dcu.ie/~jjudge/qtreebank/>)**: a corpus of 4000 **parse-annotated** questions for use in training parsers employed in QA and evaluation of question parsing. In 2011, Chris Manning spent some time improving the parses in QuestionBank, and the results of that work appear [here](<https://nlp.stanford.edu/data/QuestionBank-Stanford.shtml>). The corrected version was released as a script that maps the original to the corrected version.

- **[English Translation Treebank (ETT)](<https://catalog.ldc.upenn.edu/LDC2012T02>)**: English Translation Treebank consists of 599 distinct newswire stories from the Lebanese publication An Nahar translated from Arabic to English and annotated for **part-of-speech** and **syntactic structure**. This corpus is part of an effort at LDC to produce parallel Arabic and English treebanks.

- **[PennBioIE Oncology](<https://catalog.ldc.upenn.edu/LDC2008T21>)**: The PennBioIE Oncology Corpus consists of 1414 [PubMed](http://www.ncbi.nlm.nih.gov/entrez/query.fcgi) abstracts on cancer, concentrating on molecular genetics, and comprising approximately 327,000 words of biomedical text, tokenized and annotated for paragraph, sentence, **part of speech**, and 24 types of **biomedical named entities** in five categories of interest. 318 of the abstracts have also been **syntactically** annotated. All of the annotation was based on [Penn Treebank II](http://www.cis.upenn.edu/~treebank/) standards, with some modifications for special characteristics of the biomedical text.

- **[GENIA corpus](<http://www.geniaproject.org/genia-corpus>)**: The corpus contains 1,999 Medline abstracts, selected using a [PubMed](http://pubmed.com/) query for the three MeSH terms "human", "blood cells", and "transcription factors". The corpus has been annotated with various levels of linguistic and semantic information, including: **part-of-speech annotation**, **constituency syntactic annotation**, term annotation, event annotation, relation annotation, and **coreference annotation**.


| Dataset      | Style | POS  | NER  | Coref |  #Token |
| ------------ | ----- | :--: | :--: | :---: | ------: |
| Brown        | PTB   |  ✔   |      |       | 486,140 |
| ATIS         | PTB   |  ✔   |      |       |       - |
| Switchboard  | PTB   |  ✔   |      |       |       - |
| EWT          | PTB   |  ✔   |      |       | 254,830 |
| Tweebank     | UD    |  ✔   |      |       |  55,427 |
| QuestionBank | PTB   |  ✔   |      |       |   4,000 |
| ETT          | PTB   |  ✔   |      |       | 461,489 |
| PennBio      | PTB   |  ✔   |  ✔   |       | 327,000 |
| GENIA        | PTB   |  ✔   |      |   ✔   | 468,793 |

# Tagging & Parsing

Domain adaptation has been recognized as a major NLP problem for over a decade. In particular, bridging the performance gap between in-domain and out-of-domain for tasks such as POS-tagging and parsing has received considerable attention. Here we will investigate some techniques for out-of-domain parsing.

## Semi-supervised parser domain adaptation with self-training

Because treebanks are expensive to create, while plain text in most domains is easily obtainable, semi-supervised approaches to parser domain adaptation are a particularly attractive solution to the domain portability problem. This usually involves a manually annotated training set (a treebank), and a larger set of unlabeled data (plain text).

Many work in unsupervised domain adaptation for state-of-the-art parsers has achieved accuracy levels on out-of-domain text that is comparable to that achieved with domain-specific training data. This is done in a self-training setting, where a parser trained on a treebank (in a seed domain) is used to parse a large amount of unlabeled data in the target domain (assigning only one parse per sentence). The automatically parsed corpus is then used as additional training data for the parser. Specifically, their highlights are:

- **Reranking**: McClosky et al. (2006b)[^3] presented the most successful semi-supervised approach to date for adaptation of a WSJ-trained parser to Brown data containing several genres of text. Their approach involves the use of a first-stage n-best parser and a reranker, which together produce parses for the unlabeled dataset. The automatically parsed in-domain corpus is then used as additional training material. In their previous work[^5], McClosky et al. (2006a) argue that the use of a reranker is an important factor in the success of their approach, which is a layered model: the baser layer is a generative statistical PCFG parser that creates a ranked list of k parses (say, 50), and the second layer is a reranker that reorders these parses using more detailed features.

- **Small seed**: Reichart and Rappoport (2007)[^4] used self-training to enhance the performance of a generative statistical PCFG parser for both the in-domain and the parser adaptation scenarios. The difference between their parser and McClosky et al.'s: [1] the 1-best list of a base parser can be used as a self-training material for the base parser itself; [2] the self-training can be done when the seed is small. They achieved this by identifying the self-training protocols: the self-training set contains several thousand sentences. A parser trained with a small seed set parses the self-training set, and then the *whole* automatically annotated self-training set is combined with the manually annotated seed set to retrain the parser.

- **Confidence-based**: Sagae and Tsujii (2007)[^6] improved the out-of-domain accuracy of a dependency parser trained on the entire WSJ training set (40k sentences) by using unlabeled data in the same domain as the out-of-domain test data (biomedical text). They use agreement between different parsers to estimate the quality of automatically generated training instances and selected only sentences with high estimated accuracy.

  Yu et al. (2015)[^9] used confidence-based methods to select additional training samples, where they compared two confidence-based methods: [1] using the parse score of the employed parser to measure the confidence into a parse tree; [2] calculating the score differences between the best tree and alternative trees.

- **Simple:** Sagae (2010)[^7] proposed a simple self-training framework for domain adaptation to avoid the settings as in previous work (without reranking, other means of training instance selection, or estimation of parse quality). The work investigated the contribution of the reranker for a constituency parser, and suggested that constituency parsers without a reranker can achieve significant improvements on the target domain but the results are still higher when a reranker is used.

- **Useful info from target domain**: Chen et al. (2008)[^8] proposed a model that learns reliable information on shorter dependencies from unlabeled target domain data to help parse longer distance words. The rationale for this procedure is the observation that short dependency edges show a higher accuracy than longer edges.

## Co-training

## Up-training

## Learning from partial annotation



[^1]: Kevin Gimpel, Nathan Schneider, Brendan O’Connor, Dipanjan Das, Daniel Mills, Jacob Eisenstein, Michael Heilman, Dani Yogatama, Jeffrey Flanigan, and Noah A. Smith. 2011. Part-of-speech tagging for twitter: Annotation, features, and experiments. In Proc. of ACL.
[^2]:  Lingpeng Kong, Nathan Schneider, Swabha Swayamdipta, Archna Bhatia, Chris Dyer, and Noah A. Smith. 2014. A Dependency Parser for Tweets. In Proc. of EMNLP.
[^3]: David McClosky, Eugene Charniak, and Mark Johnson. 2006b. Reranking and self-training for parser adaptation. In Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics, pages 337–344. Association for Computational Linguistics.
[^4]: Roi Reichart and Ari Rappoport. 2007. Self-training for enhancement and domain adaptation of statistical parsers trained on small datasets. In Proceedings of the 45th Annual Meeting of the Association for Computational Linguistics (ACL). Pages 616-623. Prague, Czech Republic.
[^5]: David McClosky, Eugene Charniak, and Mark Johnson. 2006b. Reranking and self-training for parser adaptation. In Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics, pages 337–344. Association for Computational Linguistics.
[^6]: Kenji Sagae and Jun’ichi Tsujii. 2007. Multilingual dependency parsing and domain adaptation with data-driven LR models and parser ensembles. In Proceedings of the CoNLL 2007 shared task. Prague, Czech Republic.
[^7]: Kenji Sagae. 2010. Self-training without reranking for parser domain adaptation and its impact on semantic role labeling. In Proceedings of the 2010 Workshop on Domain Adaptation for Natural Language Processing, pages 37–44. Association for Computational Linguistics.
[^8]: Wenliang Chen, Youzheng Wu, and Hitoshi Isahara. 2008. Learning reliable information for dependency parsing adaptation. In Proceedings of the 22nd International Conference on Computational Linguistics-Volume 1, pages 113–120. Association for Computational Linguistics.
[^9]: Juntao Yu, Mohab Elkaref, and Bernd Bohnet. 2015. Domain adaptation for dependency parsing via selftraining. In IWPT.