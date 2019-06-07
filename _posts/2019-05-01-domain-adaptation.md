---
title: "Domain Adaptation for Syntactic Analysis"
hidden: true
last_modified_at: 2019-05-11
tag: NLP Tasks
author: Zi Lin
date: 2019-05-01
---
# Introduction
Current NLP systems tend to perform well only on their training domain and nearby genres, while the performances often degrade on the data drawn from different domains. For instance, the performance of a statistical parser trained on the Penn Treebank Wall Street Journal (WSJ; newspaper text) significantly drops when evaluated on text from other domains, as shown in the following table (results are from McClosky, 2010; for the details of those domains, cf. the next section):

| Train | WSJ | Brown | GENIA | SWBD |
| :---: | :-: | :---: | :---: | :--: |
|  WSJ  | 89.7|  84.1 |  76.2 | 76.7 |

Recently, many endeavors have been made to explore and solve the problems of domain adaptation, ranging from creating challenging datasets to building algorithm that can avoid overfitting on the training data or transfer to the new domains. Here we will discuss several datasets build for the out-of-domain NLP task of syntactic analysis as well as some ideas on how to address the problem of domain adaptation. Note that in the literature, many definitions of the source domain and the target domain are proposed, here domain adaptation refers to the domains within the same NLP task and language (mainly English), but for other genres and domains such as emails, web forums and biomedical papers in terms of the dominant source domain of the Penn Treebank of Wall Street Journal (WSJ, financial news).


# Datasets

- **[Brown, ATIS & Switchboard (SWBD) of Treebank-3](https://catalog.ldc.upenn.edu/LDC2009T26)**: The Treebank-3 released by LDC also contains different domains other than WSJ: (1) the Brown Corpus (domain of literature), which has been completely retagged using the Penn Treebank tag set; (2) ATIS, the data from Department of Energy abstracts; (3) Switchboard, a collection of spontaneous telephone conversations between previously unacquainted speakers of American English on a variety of topics chosen from a pre-determined list.

- **[British National Corpus (BNC)](https://www.computing.dcu.ie/~jfoster/resources/bnc1000.html)**: Gold Standard **Parse Trees** for 1,000 sentences from the British National Corpus -- annotated according to Penn Treebank bracketing guidelines and checked using Markus Dickinson's treebank annotation error detection software.

- **[English Web Treebank (EWT)](https://catalog.ldc.upenn.edu/LDC2012T13)**: English Web Treebank was developed by the Linguistic Data Consortium (LDC) with funding from Google Inc. It contains 254,830 word-level tokens and 16,624 sentence-level tokens of webtext in 1174 files annotated for sentence- and word-level **tokenization,** **part-of-speech**, and **syntactic structure**. The data is roughly evenly divided across five genres: weblogs, newsgroups, email, reviews, and question-answers. The files were manually annotated following the sentence-level tokenization guidelines for web text and the word-level tokenization guidelines developed for English treebanks.

- **[Tweebank](https://github.com/Oneplus/Tweebank)**: Tweebank v2 is a collection of English tweets annotated in **Universal Dependencies** that can be exploited for the training of NLP systems to enhance their performance on social media texts. It is built on the original data of Tweebank v1 (840 unique tweets, 639/201 for training/test set), along with an additional 210 tweets sampled from the **POS-tagged** dataset of Gimpel et al. (2011) [^1] and 2,500 tweets sampled from the Twitter stream from Feb. 2016 to Jul. 2016. The latter data source consists of 147.4M English tweets. In the same way as Kong et al. (2011)[^2], reference unit is always the tweet in its entirety -- which may thus consist of multiple sentences -- not the sentence alone.

- **[Foreebank](http://nclt.computing.dcu.ie/mt/confidentmt.html)**: the Foreebank treebank contains 1,000 English sentences and 1,000 French sentences. The English sentences come from the Symantec Norton technical support user forum. Half of the French sentences come from the French Norton forum and the other half are human translations of sentences from the English forum. The English annotation are guided by **the Penn Treebank bracketing** guidelines and a Foreebank-adapted version of the English Web Treebank bracketing guidelines. The French annotators used the French treebank (FTB) guidelines, following the SPMRL strategy for multiword expressions. They also contains the annotation for **grammatical errors**.

- **[QuestionBank(QB)](https://www.computing.dcu.ie/~jjudge/qtreebank/)**: a corpus of 4000 **parse-annotated** questions for use in training parsers employed in QA and evaluation of question parsing. In 2011, Chris Manning spent some time improving the parses in QuestionBank, and the results of that work appear [here](<https://nlp.stanford.edu/data/QuestionBank-Stanford.shtml>). The corrected version was released as a script that maps the original to the corrected version.

- **[English Translation Treebank (ETT)](https://catalog.ldc.upenn.edu/LDC2012T02)**: English Translation Treebank consists of 599 distinct newswire stories from the Lebanese publication An Nahar translated from Arabic to English and annotated for **part-of-speech** and **syntactic structure**. This corpus is part of an effort at LDC to produce parallel Arabic and English treebanks.

- **[PennBioIE Oncology](https://catalog.ldc.upenn.edu/LDC2008T21)**: The PennBioIE Oncology Corpus consists of 1414 [PubMed](http://www.ncbi.nlm.nih.gov/entrez/query.fcgi) abstracts on cancer, concentrating on molecular genetics, and comprising approximately 327,000 words of biomedical text, tokenized and annotated for paragraph, sentence, **part of speech**, and 24 types of **biomedical named entities** in five categories of interest. 318 of the abstracts have also been **syntactically** annotated. All of the annotation was based on [Penn Treebank II](http://www.cis.upenn.edu/~treebank/) standards, with some modifications for special characteristics of the biomedical text.

- **[GENIA corpus](http://www.geniaproject.org/genia-corpus)**: The corpus contains 1,999 Medline abstracts, selected using a [PubMed](http://pubmed.com/) query for the three MeSH terms "human", "blood cells", and "transcription factors". The corpus has been annotated with various levels of linguistic and semantic information, including: **part-of-speech annotation**, **constituency syntactic annotation**, term annotation, event annotation, relation annotation, and **coreference annotation**.

Here are some statistics and examples of English data from various source:

| Dataset      | Style | POS  | NER  | Coref |  #Token   |
| ------------ | ----- | :--: | :--: | :---: | --------: |
| Brown        | PTB   |  ✔   |      |       |   130,417 |
| ATIS         | PTB   |  ✔   |      |       |     4,350 |
| SWBD         | PTB   |  ✔   |      |       | 1,095,089 |
| BNC          | PTB   |  ✔   |      |       |    28,311 |
| EWT          | PTB   |  ✔   |      |       |   272,779 |
| EWT_UD       | UD    |  ✔   |      |       |   254,830 |
| Tweebank     | UD    |  ✔   |      |       |    55,427 |
| Foreebank    | PTB*  |  ✔   |      |       |    15,613 |
| QB           | PTB   |  ✔   |      |       |         - |
| ETT          | PTB   |  ✔   |      |       |   461,489 |
| PennBio      | PTB   |  ✔   |  ✔   |       |   327,000 |
| GENIA        | PTB   |  ✔   |      |   ✔   |   468,793 |

```
# Newspaper text (WSJ):
- Rolls - Royce Motor Cars Inc. said it expects its U.S. sales to remain steady at
about 1,200 cars in 1990 .
- The luxury auto maker last year sold 1,214 cars in the U.S.
- Bell , based in Los Angeles , makes and distributes electronic , computer and
building products .
- Investors are appealing to the Securities and Exchange Commission not to limit
their access to information about stock purchases and sales by corporate
insiders .
```
```
# Literature (Brown):
- With the end of the trial Diane disappeared from New York .
Several years ago she married a Houston business man , Robert Graham .
- She later divorced Graham , who is believed to have moved to Bolivia .
- The next time the police saw her she was dead .
- It was September 20 , 1960 , in a lavishly decorated apartment littered with
liquor bottles .
- When the police arrived , they found McClellan and the two lawyers sitting and
staring silently .
- An autopsy disclosed a large amount of morphine in Diane ’s body .
- ‘‘ I think that maybe she wanted it this way ’’ , a vice squad cop said .
```
```
# Social Media Data (Twitter):
- new unique backpack ! combines vintage with modern ! URL1283 via @USER415
- RT @USER767 : It 's been 3 years since the release of the music video of 
Burning Desire . URL637
- RT @USER526 : empathy 4 Kesha .... the worst . smh smh .. 💔
- RT @USER1218 : @UER265 Blessed Sunday 😊
- ; idk why ... but ifeel like talkin dirty O_o
```
```
# Questions (QuestionBank):
- Who is the author of the book, `` The Iron Lady : A Biography of Margaret 
Thatcher '' ?
- What was the monetary value of the Nobel Prize in 1989 ?
- How much did Mercury spend on advertising in 1993 ?
- Why did David Koresh ask the FBI for a word processor ?
- Name the designer of the shoe that spawned millions of plastic imitations , 
known as `` jellies '' .
```
```
# Biomedical abstract (GENIA):
- Glucocorticoid resistance in the squirrel monkey is associated with
overexpression of the immunophilin FKBP51 .
- The low binding affinity of squirrel monkey GR does not result from
substitutions in the receptor , because squirrel monkey GR expressed in vitro
exhibits high affinity .
- Rather , squirrel monkeys express a soluble factor that , in mixing studies of
cytosol from squirrel monkey lymphocytes ( SML ) and mouse L929 cells , reduced
GR binding affinity by 11-fold .
```

# Domain Adaptation for Tagging & Parsing

Domain adaptation has been recognized as a major NLP problem for over a decade. In particular, bridging the performance gap between in-domain and out-of-domain for tasks such as POS-tagging and parsing has received considerable attention. 

Similar to the general trichotomy of machine learning algorithms, according to the data available for the new target domain, there are three main approaches to domain adaptation that have been identified in the literature:

- Supervised domain adaptation (e.g., Hera et al., 2005[^3]; Daumé III, 2007[^4])
- Unsupervised domain adaptation (e.g. Blitzer, McDonald & Pereira, 2006[^5]; McClosky et al, 2006[^6])
- Semi-supervised domain adaptation (e.g. Daumé III, Kumar & Saha, 2010[^7]; Chang, Connor & Roth, 2010[^8])

Here we will investigate some techniques for out-of-domain parsing, mainly the semi-supervised approaches that do not require to manually annotate new data, as well as some other approaches. Note that what was previously called semi-supervised is nowadays often called unsupervised domain adaptation, although the new "convention" still needs to be established.

## Supervised Domain Adaptation
- **Incorporate prior knowledge**: the original, out-of-domain, source domain model is exploited as a prior when estimating a model on the target domain for which only limited resources are available. Roark and Bacchiani (2003)[^9] examined this idea to adapt a PCFG parser. Hara et al. (2005)[^3] try to adapt the probabilistic disambiguation component of a grammar-driven parser (based on a HPSG) trained on newspaper text (WSJ) to the biomedical domain (GENIA corpus). The disambiguation component of their parser, Enju, is a maximum entropy model on a packed forest structure. Their approach consisted in integrating the original model estimated on the larger, out-of-domain data (newspaper text) as a reference distribution when learning a model on the target domain.

- **Altering the feature space**: A basic assumption is that there are three underlying distributions: an in-domain distribution, an out-of-domain distribution and a general distribution. Thus, it is assumed that the out-of-domain distribution is drawn from a mixture of the out-of-domain and the general distribution.

	Daumé III (2007)[^4] introduces an algorithm called *easy 	adapt*, whose idea is to transform the domain adaptation learning problem into a standard supervised learning problem to which “any standard algorithm may be applied (e.g. SVM, MaxEnt)”. He proposes a simple pre-processing step in which the feature space is altered and the resulting data is used as input to a standard learning algorithm. In more detail, easy adapt basically triples the feature space: it takes each feature in the original feature space and makes three versions of it, a general version, a source-specific and a target-specific version. By transforming the feature space, the supervised learning algorithm is supposed to ‘learn’ which features transfer better to the new domain.

	Finkel and Manning (2009)[^10] extend *easy adapt* by adding explicit hyperparameters to the model. That is, each domain has its own set of domain-specific parameters, but they are linked by introducing an additional layer that acts as general parameter set. This parametrization encourages the features to have similar weights across domains, unless there is evidence in the domains to the contrary.

- **Changing the instance distribution**: Jiang and Zhai (2007)[^11] propose to weigh source training instances by their similarity to the target distribution. They have shown the effectiveness of this approach on three NLP tasks (POS tagging, named entity classification and spam filtering).

## Unsupervised Domain Adaptation

### Self-training

Because treebanks are expensive to create, while plain text in most domains is easily obtainable, semi-supervised approaches to parser domain adaptation are a particularly attractive solution to the domain portability problem. This usually involves a manually annotated training set (a treebank), and a larger set of unlabeled data (plain text).

Many work in unsupervised domain adaptation for state-of-the-art parsers has achieved accuracy levels on out-of-domain text that is comparable to that achieved with domain-specific training data. This is done in a self-training setting, where a parser trained on a treebank (in a seed domain) is used to parse a large amount of unlabeled data in the target domain (assigning only one parse per sentence). The automatically parsed corpus is then used as additional training data for the parser. Specifically, their highlights are:

- **Reranking**: McClosky et al. (2006b)[^6] presented the most successful semi-supervised approach to date for adaptation of a WSJ-trained parser to Brown data containing several genres of text. Their approach involves the use of a first-stage n-best parser and a reranker, which together produce parses for the unlabeled dataset. The automatically parsed in-domain corpus is then used as additional training material. In their previous work[^12], McClosky et al. (2006a) argue that the use of a reranker is an important factor in the success of their approach, which is a layered model: the baser layer is a generative statistical PCFG parser that creates a ranked list of k parses (say, 50), and the second layer is a reranker that reorders these parses using more detailed features.

- **Small seed**: Reichart and Rappoport (2007)[^13] used self-training to enhance the performance of a generative statistical PCFG parser for both the in-domain and the parser adaptation scenarios. The difference between their parser and McClosky et al.'s: [1] the 1-best list of a base parser can be used as a self-training material for the base parser itself; [2] the self-training can be done when the seed is small. They achieved this by identifying the self-training protocols: the self-training set contains several thousand sentences. A parser trained with a small seed set parses the self-training set, and then the *whole* automatically annotated self-training set is combined with the manually annotated seed set to retrain the parser.

- **Confidence-based**: Yu et al. (2015)[^14] used confidence-based methods to select additional training samples, where they compared two confidence-based methods: [1] using the parse score of the employed parser to measure the confidence into a parse tree; [2] calculating the score differences between the best tree and alternative trees.

- **Simple:** Sagae (2010)[^15] proposed a simple self-training framework for domain adaptation to avoid the settings as in previous work (without reranking, other means of training instance selection, or estimation of parse quality). The work investigated the contribution of the reranker for a constituency parser, and suggested that constituency parsers without a reranker can achieve significant improvements on the target domain but the results are still higher when a reranker is used.

- **Useful info from target domain**: Chen et al. (2008)[^16] proposed a model that learns reliable information on shorter dependencies from unlabeled target domain data to help parse longer distance words. The rationale for this procedure is the observation that short dependency edges show a higher accuracy than longer edges. Kiperwasser and Goldberg (2015)[^17] presented a approach to improve dependency parsing accuracy by using bilexical statistics derived from auto-parsed data. The method is based on estimating the attachment potential of head-modifier words, by taking into account not only the head and modifier words themselves, but also the words surrounding the head and the modifier.

### Co-training

Co-training is a technique that has been frequently used by domain adaptation for parsers, and it is very similar to self-training except that it involves more than one learner. The early version of co-training used two different 'view' of the classifier, each of which has a distinct feature set. They are used to annotate unlabeled set after trained on the same training set. Then both classifiers are retrained on the newly annotated data and the initial training set.[^18] 

Sagae and Tsujii (2007)[^19] improved the out-of-domain accuracy of a dependency parser trained on the entire WSJ training set (40k sentences) by using unlabeled data in the same domain as the out-of-domain test data (biomedical text). Specifically, they co-trained two dependency parsers by adding automatically parsed sentences for which the parsers agree to the training data. They use agreement between different parsers to estimate the quality of automatically generated training instances and selected only sentences with high estimated accuracy.

Weiss et al. (2015)[^20] used normal agreement based co-training and tri-training in their evaluation of a state-of-the-art neural network parser. In their work, the annotations agreed by a conventional transition-based parser (zPar) and the Berkeley constituency parser have been used as additional training data. They retained their neural network parser and the zPar parser on the extended training data.

### Other approaches

- **Up-training**: Petrov et al. (2010) [^21] proposed an up-training procedure in which a deterministic parser is trained on the output of a more accurate, but slower, latent variable constituency parser (converted to dependencies). In practice, they parsed a large amount of unlabeled data from the target domain with the constituency parser of Petrov et al. (2006)[^22] and then trained a deterministic dependency parser on this noisy, automatically parsed data. 

  The goal of up-training is different from self-training or co-training, which are to further improve the performance of the very best model, instead, it aims to train a computationally cheaper model (a linear time dependency parser) to match the performance of the best model, resulting in a computationally efficient, yet highly accurate model.

- **Using external lexicon**:  Lack of the knowledge of the unknown words is one of the key problems of domain adaptation tasks. One way to address this problem is to use the external lexicon resources, which provide additional information for tokens, such as word lemma, part-of-speech tags, morphological information and so on. This information can be used by parsers directly to help making the decision. In previous work, lexicons have been used by Szolovits (2003)[^23] and Pyysalo et al. (2006)[^24] to improve the link grammar parser on the medical domain. Both approaches showed large improvements on parsing accuracy. Pekar et al. (2014)[^25] extracted a lexicon from a crowd-sourced online dictionary (Wikitionary) and applied it to a strong dependency parser. However, the dictionary achieved a moderate improvement only.

- **Leveraging pre-trained embeddings**: By using pre-trained word embeddings the neural network-based parsers can usually achieve a higher accuracy compared with those who used randomly initialized embeddings. [^20] [^26] [^27] Furthermore, Joshi et al. (2018) [^28] showed that recent advances in word representations greatly diminish the need for domain adaptation when the target domain is syntactically similar to the source domain.

- **Word clustering**: Word clustering is an unsupervised algorithm that is able to group the similar words into the same classes by analyzing the co-occurence of the words in a large unlabeled corpus. Koo et al. (2008)[^29] first employed a set of features based on brown clusters to a second-order graph-based dependency parser. The similar features have been adapted to a transition-based parser of Bohnet and Nivre (2012)[^30].

- **Learning from partial annotation**: In Joshi et al. (2018)'s work[^28], they revisited domain adaptation for parsers in the neural era and provided a simple way to adapt a parser using only dozens of partial annotations. Specifically, they create some partial annotations to teach the parser how to fix the errors, then retrains the parser, repeating the process until they are satisfied.

- **Learning domain differences**: Yu et al.[^31] addressed the relation between domain differences and domain adaptation for dependency parsing. They first showed that it is the inconsistent behavior of same features cross-domain, rather than word or feature coverage, that is the major cause of performances decrease of out-of-domain model, and the set of ambiguous features is small and has concentric distributions. Based on the analyses, they proposed a DA model that can automatically learn which features are ambiguous cross domain according to errors made by out-domain model on in-domain training data.

- **Integrating with features learned from unlabeled data**: Chen et al. (2012)[^32] applied high-order DLMs to a second-order graph-based parser. The DLMs allow the new parser to explore high-order features without increasing the time complexity. The DLMs are extracted from a 43 million words English corpus and a 311 million words corpus of Chinese parsed by the baseline parser. Features based on the DLMs are used in the parser.

- **Normalization**: A theoretical exploration of the effect of normalization on forum data is done by Kaljahi et al. (2015)[^33], where they show that parsing manually normalize sentences results in a 2% increase of F1 score. Baldwin and Li (2015)[^34] evaluate the effects of different normalization actions on dependency parsing performance for the social media domain. They conclude that a variety of different normalization actions is useful for parsing. Zhang et al. (2013) [^35] test the effect of automatic normalization on dependency parsing by using automatically derived parse trees of the normalized sentences as reference. van der Goot and van Noord (2017) [^36] show that integrating the normalization model into the parsing algorithm is beneficial. This way multiple normalization candidates can be leveraged, improving parsing performance on social media.

# Constituency parsing performance across different domains

There have been many constituency parsers developed based on different methodologies, and much progress has been made in recent years. A question is that whether those *so-called* state-of-the-art parsers could work robustly on other domains, here I list some of the constituency parsers as well as their performance on different domains.

The performance of Stanford, Stanford-RNN, Berkeley, Charniak, BLLIP and BLLIP* are reported from Choe et al.[^37], where all the parsers are trained on the WSJ training set (section 2-21) and the test data splits follow the standard as introduced in Foster and van Genabith (2008), Kim et al. (2003), Godfrey et al. (1992), Judge et al. (2006), Francis and Kučera (1989) and Marcus et al. (1993). BLLIP* (self-trained) BLLIP is self-trained using two million sentences from Gigaword and Stanford-RNN uses word embeddings trained from larger corpora. For more details, cf. Choe et al.[^37]. The results of Berkeley* (Berkeley self-attentive parser) are based on my own experiments.

| Parser          |  BNC  |  GENIA |  SWBD  |   QB*  |   Brown  |  WSJ  | Foreebank |
| :-------------- | :---: | :----: | :----: | :----: | :------: | :---: |  :------: |
| Berkeley*       | 92.8  |  84.2  |  81.8  |  91.5  |  89.2    |  95.1 |   77.9    |
| BLLIP*          | 85.2  |  77.8  |  80.9  |  89.5  |  87.4    |  92.2 |     -     |
| BLLIP           | 84.1  |  76.7  |  79.2  |  88.1  |  85.8    |  91.5 |     -     |
| Charniak        | 82.5  |  74.8  |  76.8  |  85.6  |  83.9    |  89.7 |     -     |
| Berkeley        | 82.3  |  76.4  |  74.5  |  86.5  |  84.6    |  90.0 |     -     |
| Stanford-RNN    | 82.0  |  84.0  |  70.7  |  82.9  |  84.0    |  89.6 |     -     |
| Stanford        | 78.4  |  73.1  |  67.0  |  78.6  |  80.7    |  85.4 |     -     |

# Useful resource
Apart from the reference, this post is also inspired a lot from:
- Yu, Juntao, 2018. [Semi-supervised methods for out-of-domain dependency parsing](https://arxiv.org/pdf/1810.02100.pdf). arXiv preprint arXiv:1810.02100.
- Barbara Plank, 2011. [Domain Adaptation for Parsing](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.721.5989&rep=rep1&type=pdf). Ph.D. thesis, University of Groningen.

Many thanks to their work, surveys on previous work and valuable thinkings to this direction. The literature review is necessarily incomplete as the literature on domain adaptation is huge (even though I only focus on parsing and tagging). I will appreciate it if you could give some feedbacks and suggestions after reading this post :)

[^1]: Kevin Gimpel, Nathan Schneider, Brendan O’Connor, Dipanjan Das, Daniel Mills, Jacob Eisenstein, Michael Heilman, Dani Yogatama, Jeffrey Flanigan, and Noah A. Smith. 2011. Part-of-speech tagging for twitter: Annotation, features, and experiments. In Proc. of ACL.
[^2]:  Lingpeng Kong, Nathan Schneider, Swabha Swayamdipta, Archna Bhatia, Chris Dyer, and Noah A. Smith. 2014. A Dependency Parser for Tweets. In Proc. of EMNLP.
[^3]:Hara, T., Miyao, Y. & Tsujii, J. 2005. Adapting a Probabilistic Disambiguation Model of an HPSG Parser to a New Domain. In R. Dale, K.-F. Wong, J. Su & O. Y. Kwong (Eds.), Natural Language Processing IJCNLP 2005 (Vol. 3651, p. 199-210). Springer Berlin / Heidelberg.
[^4]:Daume III, H. 2007. Frustratingly Easy Domain Adaptation. In ´ Proceedings of the 45th Meeting of the Association for Computational Linguistics. Prague, Czech Republic: Association for Computational Linguistics.
[^5]: Blitzer, J., McDonald, R. & Pereira, F. 2006. Domain Adaptation with Structural Correspondence Learning. In Proceedings of the 2006 Conference on Empirical Methods in Natural Language Processing. Sydney, Australia.
[^6]: David McClosky, Eugene Charniak, and Mark Johnson. 2006b. Reranking and self-training for parser adaptation. In Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics, pages 337–344. Association for Computational Linguistics.
[^7]: Daume III, H., Kumar, A. & Saha, A. 2010. Frustratingly Easy Semi- ´ Supervised Domain Adaptation. In Proceedings of the 2010 Workshop on Domain Adaptation for Natural Language Processing (pp. 53–59). Uppsala, Sweden: Association for Computational Linguistics.
[^8]: Chang, M.-W., Connor, M. & Roth, D. 2010. The Necessity of Combining Adaptation Methods. In Proceedings of the 2010 Conference on Empirical Methods in Natural Language Processing (pp. 767–777). Cambridge, MA: Association for Computational Linguistics.
[^9]: Roark, B. & Bacchiani, M. 2003. Supervised and unsupervised PCFG adaptation to novel domains. In Proceedings of the 4th Conference of the North American Chapter of the ACL (pp. 126–133). Edmonton, Canada: Association for Computational Linguistics.
[^10]: Finkel, J. R. & Manning, C. D. 2009. Hierarchical Bayesian Domain Adaptation. In Proceedings of Human Language Technology Conference of the North American Chapter of the Association for Computational Linguistics (pp. 602–610). Boulder, Colorado: Association for Computational Linguistics.
[^11]:Jiang, J. & Zhai, C. 2007. Instance Weighting for Domain Adaptation in NLP. In Proceedings of the 45th Meeting of the Association for Computational Linguistics (pp. 264–271). Prague, Czech Republic: Association for Computational Linguistics.
[^12]: David McClosky, Eugene Charniak, and Mark Johnson. 2006b. Reranking and self-training for parser adaptation. In Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics, pages 337–344. Association for Computational Linguistics.
[^13]: Roi Reichart and Ari Rappoport. 2007. Self-training for enhancement and domain adaptation of statistical parsers trained on small datasets. In Proceedings of the 45th Annual Meeting of the Association for Computational Linguistics (ACL). Pages 616-623. Prague, Czech Republic.
[^14]: Juntao Yu, Mohab Elkaref, and Bernd Bohnet. 2015. Domain adaptation for dependency parsing via selftraining. In IWPT.
[^15]: Kenji Sagae. 2010. Self-training without reranking for parser domain adaptation and its impact on semantic role labeling. In Proceedings of the 2010 Workshop on Domain Adaptation for Natural Language Processing, pages 37–44. Association for Computational Linguistics.
[^16]: Wenliang Chen, Youzheng Wu, and Hitoshi Isahara. 2008. Learning reliable information for dependency parsing adaptation. In Proceedings of the 22nd International Conference on Computational Linguistics-Volume 1, pages 113–120. Association for Computational Linguistics.
[^17]: Eliyahu Kiperwasser and Yoav Goldberg. 2015. Semi-supervised dependency parsing using bilexical contextual features from auto-parsed data. In Proceedings of the 2015 Conference on Empirical Methods in Natural Language Processing. Association for Computational Linguistics, Lisbon, Portugal, pages 1348–1353. <http://aclweb.org/anthology/D15-1158>.
[^18]: Avrim Blum and Tom Mitchell. 1998. Combining labeled and unlabeled data with co-training. In Proceedings of the Eleventh Annual Conference on Computational Learning Theory, pages 92–100, Madison, Wisconsin, USA. Association for Computing Machinery.
[^19]: Kenji Sagae and Jun’ichi Tsujii. 2007. Multilingual dependency parsing and domain adaptation with data-driven LR models and parser ensembles. In Proceedings of the CoNLL 2007 shared task. Prague, Czech Republic.
[^20]: David Weiss, Chris Alberti, Michael Collins, and Slav Petrov. 2015. Structured training for neural network transition-based parsing. In Proceedings of the 53rd Annual Meeting of the Association for Computational Linguistics and the 7th International Joint Conference on Natural Language Processing, pages 323–333, Beijing, China. Association for Computational Linguistics.
[^21]: Petrov, S., Chang, P. C., Ringgaard, M., & Alshawi, H. 2010, October. Uptraining for accurate deterministic question parsing. In *Proceedings of the 2010 Conference on Empirical Methods in Natural Language Processing* (pp. 705-713). Association for Computational Linguistics.
[^22]: Petrov, S., Barrett, L., Thibaux, R., & Klein, D. (2006, July). Learning accurate, compact, and interpretable tree annotation. In *Proceedings of the 21st International Conference on Computational Linguistics and the 44th annual meeting of the Association for Computational Linguistics* (pp. 433-440). Association for Computational Linguistics.
[^23]:Peter Szolovits. 2003. Adding a medical lexicon to an English parser. AMIA Annual Symposium Proceedings, pages 639–643.
[^24]: Sampo Pyysalo, Tapio Salakoski, Sophie Aubin, and Adeline Nazarenko. 2006. Lexical adaptation of link grammar to the biomedical sublanguage: A comparative evaluation of three approaches. BMC Bioinformatics, 7 (Suppl 3).
[^25]: Viktor Pekar, Juntao Yu, Mohab Elkaref, and Bernd Bohnet. 2014. Exploring options for fast domain adaptation of dependency parsers. In Proceedings of the First Joint Workshop on Statistical Parsing of Morphologically Rich Languages and Syntactic Analysis of Non-Canonical Languages, pages 54–65, Dublin, Ireland. Dublin City University.
[^26]: Danqi Chen and Christopher Manning. 2014. A fast and accurate dependency parser using neural networks. In Proceedings of the 2014 Conference on Empirical Methods in Natural Language Processing, pages 740–750, Doha, Qatar. Association for Computational Linguistics.
[^27]: Timothy Dozat and Christopher Manning. 2017. Deep biaffine attention for neural dependency parsing. In Proceedings of the 5th International Conference on Learning Representations, Toulon, France.
[^28]: Joshi, V., Peters, M., & Hopkins, M. 2018, July. Extending a Parser to Distant Domains Using a Few Dozen Partially Annotated Examples. In *Proceedings of the 56th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)* (pp. 1190-1199).
[^29]: Terry Koo, Xavier Carreras, and Michael Collins. 2008. Simple semisupervised dependency parsing. In Proceedings of the 46th Annual Meeting of the Association for Computational Linguistics: Human Language Technologies, pages 595–603, Columbus, Ohio, USA. Association for Computational Linguistics.
[^30]: Bernd Bohnet and Joakim Nivre. 2012. A transition-based system for joint part-of-speech tagging and labeled non-projective dependency parsing. In Proceedings of the 2012 Joint Conference on Empirical Methods in Natural Language Processing and Computational Natural Language Learning, pages 1455–1465, Jeju Island, Korea. Association for Computational Linguistics.
[^31]: Yu, M., Zhao, T. and Bai, Y., 2013, June. Learning domain differences automatically for dependency parsing adaptation. In Twenty-Third International Joint Conference on Artificial Intelligence.
[^32]: Wenliang Chen, Min Zhang, and Haizhou Li. 2012. Utilizing dependency language models for graph-based dependency parsing models. In Proceedings of the 50th Annual Meeting of the Association for Computational Linguistics, pages 213–222, Jeju Island, Korea. Association for Computational Linguistics.
[^33]: Rasoul Kaljahi, Jennifer Foster, Johann Roturier, Corentin Ribeyre, Teresa Lynn, and Joseph Le Roux. 2015. Foreebank: Syntactic analysis of customer support forums. In Proceedings of the 2015 Conference on Empirical Methods in Natural Language Processing. Association for Computational Linguistics, Lisbon, Portugal, pages 1341–1347. <http://aclweb.org/anthology/D15-1157>.
[^34]: Tyler Baldwin and Yunyao Li. 2015. An in-depth analysis of the effect of text normalization in social media. In Proceedings of the 2015 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies. Association for Computational Linguistics, Denver, Colorado, pages 420–429. <http://www.aclweb.org/anthology/N15-1045>.
[^35]: Congle Zhang, Tyler Baldwin, Howard Ho, Benny Kimelfeld, and Yunyao Li. 2013. Adaptive parser-centric text normalization. In Proceedings of the 51st Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers). Association for Computational Linguistics, Sofia, Bulgaria, pages 1159–1168. <http://www.aclweb.org/anthology/P13-1114>.
[^36]: Rob van der Goot and Gertjan van Noord. 2017. Parser adaptation for social media by integrating normalization. In Proceedings of ACL.
[^37]: Do Kook Choe, David McClosky, and Eugene Charniak. 2015. Syntactic parse fusion. In Proceedings of the 2015 Conference on Empirical Methods in Natural Language Processing.