---
title: "Domain Adaptation for Syntactic Analysis"
hidden: true
last_modified_at: 2019-05-01
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

- **[English Translation Treebank (ETT)](<https://catalog.ldc.upenn.edu/LDC2012T02>)**: English Translation Treebank consists of 599 distinct newswire stories from the Lebanese publication An Nahar translated from Arabic to English and annotated for **part-of-speech** and **syntactic structure**. This corpus is part of an effort at LDC to produce parallel Arabic and English treebanks.

- **[PennBioIE Oncology](<https://catalog.ldc.upenn.edu/LDC2008T21>)**: The PennBioIE Oncology Corpus consists of 1414 [PubMed](http://www.ncbi.nlm.nih.gov/entrez/query.fcgi) abstracts on cancer, concentrating on molecular genetics, and comprising approximately 327,000 words of biomedical text, tokenized and annotated for paragraph, sentence, **part of speech**, and 24 types of **biomedical named entities** in five categories of interest. 318 of the abstracts have also been **syntactically** annotated. All of the annotation was based on [Penn Treebank II](http://www.cis.upenn.edu/~treebank/) standards, with some modifications for special characteristics of the biomedical text.

- **[GENIA corpus](<http://www.geniaproject.org/genia-corpus>)**: The corpus contains 1,999 Medline abstracts, selected using a [PubMed](http://pubmed.com/) query for the three MeSH terms "human", "blood cells", and "transcription factors". The corpus has been annotated with various levels of linguistic and semantic information, including: **part-of-speech annotation**, **constituency syntactic annotation**, term annotation, event annotation, relation annotation, and **coreference annotation**.


| Dataset     | Style | POS | NER | Coref |  #Token |
|-------------|-------|:---:|:---:|:-----:|--------:|
| Brown       | PTB   |  ✔  |     |       | 486,140 |
| ATIS        | PTB   |  ✔  |     |       |       - |
| Switchboard | PTB   |  ✔  |     |       |       - |
| EWT         | PTB   |  ✔  |     |       | 254,830 |
| Tweebank    | UD    |  ✔  |     |       |  55,427 |
| ETT         | PTB   |  ✔  |     |       | 461,489 |
| PennBio     | PTB   |  ✔  |  ✔  |       | 327,000 |
| GENIA       | PTB   |  ✔  |     |   ✔   | 468,793 |

[^1]: Kevin Gimpel, Nathan Schneider, Brendan O’Connor, Dipanjan Das, Daniel Mills, Jacob Eisenstein, Michael Heilman, Dani Yogatama, Jeffrey Flanigan, and Noah A. Smith. 2011. Part-of-speech tagging for twitter: Annotation, features, and experiments. In Proc. of ACL.
[^2]:  Lingpeng Kong, Nathan Schneider, Swabha Swayamdipta, Archna Bhatia, Chris Dyer, and Noah A. Smith. 2014. A Dependency Parser for Tweets. In Proc. of EMNLP.