# A Digital Critical Edition of Jonathan Swift

An exploratory text analytics study of the complete works of Jonathan Swift (1667–1745), built for DS 5001 (Exploratory Text Analytics). The project asks whether unsupervised methods, given only vocabulary, reproduce the genre groupings traditionally assigned to Swift's writing or reveal a different organization underneath them.

## Motivation

Swift's career spans an unusually wide range of forms: satirical fiction (*Gulliver's Travels*, *A Tale of a Tub*), political tract (*The Drapier's Letters*, *A Modest Proposal*), sermon, prayer, intimate verse, and comedic dialogue. Traditional literary study sorts these into genres. This project asks a different question: if we hand a machine nothing but the words, does it recover those same genre boundaries, or does it find a more natural organization of Swift's writing that genre labels would otherwise mask?

The corpus comprises **49 distinct works totaling 3,585 paragraphs**, drawn from 7 Project Gutenberg source files and spanning Swift's career from 1697 to 1744.

## Methods

**Corpus construction.** The Gutenberg plain-text files were parsed into an OHCO hierarchy `(work_id, part_num, chap_num, para_num, sent_num, token_num)` using three custom parsers for the formats encountered (single works, multi-work bundles, and works with explicit chapter hierarchies). Standard NLP enrichment followed — tokenization, stopword flagging, Porter stemming, WordNet lemmatization, POS tagging, and TFIDF at the work and paragraph levels — producing the `LIB`, `TOKEN`, `VOCAB`, `BOW_WORK`, and `BOW_PARA` tables. Genre labels were used only for visualization, never as model features.

Four unsupervised analyses were then run:

- **PCA** on L2-normalized TFIDF (5,000-term significant vocabulary), in three runs: all paragraphs, paragraphs with the dominant *Polite Conversation* excluded, and aggregated to the work level.
- **LDA** (scikit-learn) with *k* = 15 topics on a noun / proper-noun filtered corpus, retaining proper nouns because Swift's named entities (Houyhnhnms, Drapier, Stella) are analytically meaningful.
- **Word2Vec** (gensim skip-gram, 100 dimensions, 50 epochs) trained on the filtered token sequences.
- **Sentiment analysis** computed two ways: NRC emotion scores joined at the token level and aggregated upward, and VADER compound scores at the sentence level aggregated to works.

## Findings

Applied independently, the three structural methods (PCA, LDA, Word2Vec) **converged on the same patterns**, indicating these are true properties of the corpus rather than artifacts of any one method:

- ***Polite Conversation*** stands out as a vocabulary outlier — PCA's first component, three dedicated LDA topics, and a tight character-name cluster in the Word2Vec t-SNE projection.
- **The Wood's halfpence controversy** forms a coherent vocabulary signature — PCA 2's leading component, LDA topic T4, and a corner cluster in t-SNE.
- **The Stella works** group *across* genre boundaries (prayers cluster with birthday poems) in PCA's work-level analysis and LDA topic T8.

Most notably, PCA's work-level axes describe Swift's writing as **public vs. intimate** and **satirical vs. formal** — organizing principles beyond genre alone. Word2Vec internalized the logic of *Gulliver's Travels*, returning `mankind` and `wise` for the analogy `horse:reason::man:?`, reflecting the Houyhnhnm world where horses are rational and humans are not.

Sentiment analysis exposed the limits of lexicon-based methods: *A Modest Proposal* scores **positive** on both NRC and VADER because its irony uses cheerful vocabulary to describe gruesome content, while the equally satirical *The Logicians Refuted* scores negative — revealing two distinct satirical tones (ironic and aggressive) within a single genre. Where lexicon sentiment does work is tracking narrative pacing: the polarity trajectory across *Gulliver's Travels* recovers its four-part arc, each voyage opening positive, dipping during conflict, and resolving before the next.

**Overall:** unsupervised methods produced a coherent, analytically useful map of Swift's writing — one that emerged from vocabulary alone and that conventional genre categories would have hidden.

## Repository layout

| Notebook | Stage |
|----------|-------|
| `01_f1_construction.ipynb` | Parsing the source files into the OHCO hierarchy |
| `02_f2_construction.ipynb` | Tokenization, annotation, and building VOCAB |
| `03_f3_construction.ipynb` | NLP enrichment (stopwords, Porter stems, POS, WordNet lemma) |
| `04_f4_construction.ipynb` | Statistical VOCAB features (rank, Zipf, entropy) and TFIDF (work + paragraph level) |
| `05_f5_pca.ipynb` | PCA (three runs) |
| `06_f5_lda.ipynb` | LDA topic model |
| `07_f5_word2vec.ipynb` | Word2Vec embeddings |
| `08_f5_sentiment_analysis.ipynb` | NRC and VADER sentiment |

