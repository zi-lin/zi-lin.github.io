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

Domain adaptation has been recognized as a major NLP problem for over a decade. In particular, bridging the performance gap between in-domain and out-of-domain for tasks such as POS-tagging and parsing has received considerable attention. Here we will investigate some techniques for out-of-domain parsing, mainly the semi-supervised approaches that do not require to manually annotate new data, as well as some other approaches.

## Self-training

Because treebanks are expensive to create, while plain text in most domains is easily obtainable, semi-supervised approaches to parser domain adaptation are a particularly attractive solution to the domain portability problem. This usually involves a manually annotated training set (a treebank), and a larger set of unlabeled data (plain text).

Many work in unsupervised domain adaptation for state-of-the-art parsers has achieved accuracy levels on out-of-domain text that is comparable to that achieved with domain-specific training data. This is done in a self-training setting, where a parser trained on a treebank (in a seed domain) is used to parse a large amount of unlabeled data in the target domain (assigning only one parse per sentence). The automatically parsed corpus is then used as additional training data for the parser. Specifically, their highlights are:

- **Reranking**: McClosky et al. (2006b)[^3] presented the most successful semi-supervised approach to date for adaptation of a WSJ-trained parser to Brown data containing several genres of text. Their approach involves the use of a first-stage n-best parser and a reranker, which together produce parses for the unlabeled dataset. The automatically parsed in-domain corpus is then used as additional training material. In their previous work[^5], McClosky et al. (2006a) argue that the use of a reranker is an important factor in the success of their approach, which is a layered model: the baser layer is a generative statistical PCFG parser that creates a ranked list of k parses (say, 50), and the second layer is a reranker that reorders these parses using more detailed features.

- **Small seed**: Reichart and Rappoport (2007)[^4] used self-training to enhance the performance of a generative statistical PCFG parser for both the in-domain and the parser adaptation scenarios. The difference between their parser and McClosky et al.'s: [1] the 1-best list of a base parser can be used as a self-training material for the base parser itself; [2] the self-training can be done when the seed is small. They achieved this by identifying the self-training protocols: the self-training set contains several thousand sentences. A parser trained with a small seed set parses the self-training set, and then the *whole* automatically annotated self-training set is combined with the manually annotated seed set to retrain the parser.

- **Confidence-based**: Yu et al. (2015)[^9] used confidence-based methods to select additional training samples, where they compared two confidence-based methods: [1] using the parse score of the employed parser to measure the confidence into a parse tree; [2] calculating the score differences between the best tree and alternative trees.

- **Simple:** Sagae (2010)[^7] proposed a simple self-training framework for domain adaptation to avoid the settings as in previous work (without reranking, other means of training instance selection, or estimation of parse quality). The work investigated the contribution of the reranker for a constituency parser, and suggested that constituency parsers without a reranker can achieve significant improvements on the target domain but the results are still higher when a reranker is used.

- **Useful info from target domain**: Chen et al. (2008)[^8] proposed a model that learns reliable information on shorter dependencies from unlabeled target domain data to help parse longer distance words. The rationale for this procedure is the observation that short dependency edges show a higher accuracy than longer edges.

## Co-training

Co-training is a technique that has been frequently used by domain adaptation for parsers, and it is very similar to self-training except that it involves more than one learner. The early version of co-training used two different 'view' of the classifier, each of which has a distinct feature set. They are used to annotate unlabeled set after trained on the same training set. Then both classifiers are retrained on the newly annotated data and the initial training set.[^13] 

Sagae and Tsujii (2007)[^6] improved the out-of-domain accuracy of a dependency parser trained on the entire WSJ training set (40k sentences) by using unlabeled data in the same domain as the out-of-domain test data (biomedical text). Specifically, they co-trained two dependency parsers by adding automatically parsed sentences for which the parsers agree to the training data. They use agreement between different parsers to estimate the quality of automatically generated training instances and selected only sentences with high estimated accuracy.

Weiss et al. (2015)[^14] used normal agreement based co-training and tri-training in their evaluation of a state-of-the-art neural network parser. In their work, the annotations agreed by a conventional transition-based parser (zPar) and the Berkeley constituency parser have been used as additional training data. They retained their neural network parser and the zPar parser on the extended training data.

## Other approaches

- **Up-training**: Petrov et al. (2010) [^19] proposed an up-training procedure in which a deterministic parser is trained on the output of a more accurate, but slower, latent variable constituency parser (converted to dependencies). In practice, they parsed a large amount of unlabeled data from the target domain with the constituency parser of Petrov et al. (2006)[^20] and then trained a deterministic dependency parser on this noisy, automatically parsed data. 

  The goal of up-training is different from self-training or co-training, which are to further improve the performance of the very best model, instead, it aims to train a computationally cheaper model (a linear time dependency parser) to match the performance of the best model, resulting in a computationally efficient, yet highly accurate model.

- **Using external lexicon**:  Lack of the knowledge of the unknown words is one of the key problems of domain adaptation tasks. One way to address this problem is to use the external lexicon resources, which provide additional information for tokens, such as word lemma, part-of-speech tags, morphological information and so on. This information can be used by parsers directly to help making the decision. In previous work, lexicons have been used by Szolovits (2003)[^10] and Pyysalo et al. (2006)[^11] to improve the link grammar parser on the medical domain. Both approaches showed large improvements on parsing accuracy. Pekar et al. (2014)[^12] extracted a lexicon from a crowd-sourced online dictionary (Wikitionary) and applied it to a strong dependency parser. However, the dictionary achieved a moderate improvement only.

- **Leveraging pre-trained embeddings**: By using pre-trained word embeddings the neural network-based parsers can usually achieve a higher accuracy compared with those who used randomly initialized embeddings. [^14] [^15] [^16] Furthermore, Joshi et al. (2018)[^18] showed that recent advances in word representations greatly diminish the need for domain adaptation when the target domain is syntactically similar to the source domain.

- **Word clustering**: Word clustering is an unsupervised algorithm that is able to group the similar words into the same classes by analyzing the co-occurence of the words in a large unlabeled corpus. Koo et al. (2008)[^21] first employed a set of features based on brown clusters to a second-order graph-based dependency parser. The similar features have been adapted to a transition-based parser of Bohnet and Nivre (2012)[^22].

- **Learning from partial annotation**: In Joshi et al. (2018)'s work[^18], they revisited domain adaptation for parsers in the neural era and provided a simple way to adapt a parser using only dozens of partial annotations. Specifically, they create some partial annotations to teach the parser how to fix the errors, then retrains the parser, repeating the process until they are satisfied.

- **Integrating with features learned from unlabeled data**: Chen et al. (2012)[^17] applied high-order DLMs to a second-order graph-based parser. The DLMs allow the new parser to explore high-order features without increasing the time complexity. The DLMs are extracted from a 43 million words English corpus and a 311 million words corpus of Chinese parsed by the baseline parser. Features based on the DLMs are used in the parser.

[^1]: Kevin Gimpel, Nathan Schneider, Brendan O’Connor, Dipanjan Das, Daniel Mills, Jacob Eisenstein, Michael Heilman, Dani Yogatama, Jeffrey Flanigan, and Noah A. Smith. 2011. Part-of-speech tagging for twitter: Annotation, features, and experiments. In Proc. of ACL.
[^2]:  Lingpeng Kong, Nathan Schneider, Swabha Swayamdipta, Archna Bhatia, Chris Dyer, and Noah A. Smith. 2014. A Dependency Parser for Tweets. In Proc. of EMNLP.
[^3]: David McClosky, Eugene Charniak, and Mark Johnson. 2006b. Reranking and self-training for parser adaptation. In Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics, pages 337–344. Association for Computational Linguistics.
[^4]: Roi Reichart and Ari Rappoport. 2007. Self-training for enhancement and domain adaptation of statistical parsers trained on small datasets. In Proceedings of the 45th Annual Meeting of the Association for Computational Linguistics (ACL). Pages 616-623. Prague, Czech Republic.
[^5]: David McClosky, Eugene Charniak, and Mark Johnson. 2006b. Reranking and self-training for parser adaptation. In Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics, pages 337–344. Association for Computational Linguistics.
[^6]: Kenji Sagae and Jun’ichi Tsujii. 2007. Multilingual dependency parsing and domain adaptation with data-driven LR models and parser ensembles. In Proceedings of the CoNLL 2007 shared task. Prague, Czech Republic.
[^7]: Kenji Sagae. 2010. Self-training without reranking for parser domain adaptation and its impact on semantic role labeling. In Proceedings of the 2010 Workshop on Domain Adaptation for Natural Language Processing, pages 37–44. Association for Computational Linguistics.
[^8]: Wenliang Chen, Youzheng Wu, and Hitoshi Isahara. 2008. Learning reliable information for dependency parsing adaptation. In Proceedings of the 22nd International Conference on Computational Linguistics-Volume 1, pages 113–120. Association for Computational Linguistics.
[^9]: Juntao Yu, Mohab Elkaref, and Bernd Bohnet. 2015. Domain adaptation for dependency parsing via selftraining. In IWPT.
[^10]:Peter Szolovits. 2003. Adding a medical lexicon to an English parser. AMIA Annual Symposium Proceedings, pages 639–643.
[^11]: Sampo Pyysalo, Tapio Salakoski, Sophie Aubin, and Adeline Nazarenko. 2006. Lexical adaptation of link grammar to the biomedical sublanguage: A comparative evaluation of three approaches. BMC Bioinformatics, 7 (Suppl 3).
[^12]: Viktor Pekar, Juntao Yu, Mohab Elkaref, and Bernd Bohnet. 2014. Exploring options for fast domain adaptation of dependency parsers. In Proceedings of the First Joint Workshop on Statistical Parsing of Morphologically Rich Languages and Syntactic Analysis of Non-Canonical Languages, pages 54–65, Dublin, Ireland. Dublin City University.
[^13]: Avrim Blum and Tom Mitchell. 1998. Combining labeled and unlabeled data with co-training. In Proceedings of the Eleventh Annual Conference on Computational Learning Theory, pages 92–100, Madison, Wisconsin, USA. Association for Computing Machinery.
[^14]: David Weiss, Chris Alberti, Michael Collins, and Slav Petrov. 2015. Structured training for neural network transition-based parsing. In Proceedings of the 53rd Annual Meeting of the Association for Computational Linguistics and the 7th International Joint Conference on Natural Language Processing, pages 323–333, Beijing, China. Association for Computational Linguistics.
[^15]: Danqi Chen and Christopher Manning. 2014. A fast and accurate dependency parser using neural networks. In Proceedings of the 2014 Conference on Empirical Methods in Natural Language Processing, pages 740–750, Doha, Qatar. Association for Computational Linguistics.
[^16]: Timothy Dozat and Christopher Manning. 2017. Deep biaffine attention for neural dependency parsing. In Proceedings of the 5th International Conference on Learning Representations, Toulon, France.
[^17]: Wenliang Chen, Min Zhang, and Haizhou Li. 2012. Utilizing dependency language models for graph-based dependency parsing models. In Proceedings of the 50th Annual Meeting of the Association for Computational Linguistics, pages 213–222, Jeju Island, Korea. Association for Computational Linguistics.
[^18]: Joshi, V., Peters, M., & Hopkins, M. 2018, July. Extending a Parser to Distant Domains Using a Few Dozen Partially Annotated Examples. In *Proceedings of the 56th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)* (pp. 1190-1199).
[^19]: Petrov, S., Chang, P. C., Ringgaard, M., & Alshawi, H. 2010, October. Uptraining for accurate deterministic question parsing. In *Proceedings of the 2010 Conference on Empirical Methods in Natural Language Processing* (pp. 705-713). Association for Computational Linguistics.
[^20]: Petrov, S., Barrett, L., Thibaux, R., & Klein, D. (2006, July). Learning accurate, compact, and interpretable tree annotation. In *Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics* (pp. 433-440). Association for Computational Linguistics.
[^21]: Terry Koo, Xavier Carreras, and Michael Collins. 2008. Simple semisupervised dependency parsing. In Proceedings of the 46th Annual Meeting of the Association for Computational Linguistics: Human Language Technologies, pages 595–603, Columbus, Ohio, USA. Association for Computational Linguistics.
[^22]: Bernd Bohnet and Joakim Nivre. 2012. A transition-based system for joint part-of-speech tagging and labeled non-projective dependency parsing. In Proceedings of the 2012 Joint Conference on Empirical Methods in Natural Language Processing and Computational Natural Language Learning, pages 1455–1465, Jeju Island, Korea. Association for Computational Linguistics.