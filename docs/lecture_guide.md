# Lecture-to-project guide

How the 12 lecture decks of DTSC6008001 Text Mining (in `D:\Text-mining-material`) map onto this final project, stage by stage.

- Citations like **(S12 sl.34)** mean Session 12, slide 34. Session order: S1 Intro to Text Mining, S2 Text Data Preparation, S3 Intro of NLP, S4 Text Preprocessing, S5-6 Text Representation, S7 Classification with simple ML, S8 Deep Learning, S9 Summarization, S10 Transformers/BERT, S11 Clustering, S12 Topic Modelling, S13 RAG.
- Each section has: where it's taught, what the lectures say, how it applies to this project, what the project needs **beyond the slides** (not from the lecture), and the decisions the brief leaves **to you**.
- Built on 11 Sep 2026 from the slide text and slide images; the claims attributed to the lectures were spot-checked against the slides. Explanations only: the justifications, model choice, comparison, topic names and conclusions are yours to write.

## The big picture: how the course maps onto your project

**Where it's taught**

Session 1 sets up the framework: the definition of text mining (S1 sl.6-9), task types (sl.10), what makes text hard (sl.11-13), the text mining process (sl.15-22) and applications (sl.24). The table further down shows which later deck covers each stage.

**What the lectures say**

- Text mining is when a computer automatically extracts previously unknown information from large, varied collections of unstructured text (S1 sl.6). The aim is new knowledge, not just spotting trends. The input is natural written prose, not markup such as HTML (S1 sl.7). In the search-vs-discover matrix, text mining is the "discover + text" cell. Information retrieval sits next to it as "search + text" (S1 sl.8). Text mining combines two parts: statistical NLP turns text into structured objects, and data mining analyses those objects (S1 sl.9).
- Text mining tasks are either descriptive (trend analysis, summarization, visualization) or predictive (classification, question answering, forecasting) (S1 sl.10). Text is hard to mine because it is ambiguous, depends on context, is noisy, and has a very high-dimensional vocabulary (S1 sl.11-13).
- The process runs Text → Preprocessing → Feature generation → Feature selection → Data mining → Interpretation/Evaluation. Dashed arrows lead from evaluation back to every earlier stage (S1 sl.15). The mining step is one of two kinds:
  - supervised classification, which learns from labelled records and is judged on records it has not seen (S1 sl.20);
  - unsupervised clustering, which groups documents by similarity (S1 sl.21).
  
  After mining, you ask whether the results are good enough or whether a step has to be redone (S1 sl.22).
- Labelled corpora are used for supervised learning. Unlabelled corpora are used for topic modelling and clustering (S3 sl.18). Topic modelling finds latent structure that goes beyond a corpus's explicit categories (S12 sl.5).

Each later deck covers one stage of the process in depth:

| S1 sl.15 stage | Brief stage | Taught in |
|---|---|---|
| Text | Scraping, collection | S2 sl.6-7, 18 |
| Preprocessing | Cleaning, preprocessing | S3 sl.5-10, 18-19; S4 sl.3-15 |
| Feature generation/selection | Representation | S5-6 sl.4-36 |
| Mining, supervised | Classification | S7 sl.5; S8 sl.9-10, 22; S10 sl.35-44 |
| Mining, unsupervised | Topic modelling per class | S11 sl.5-15; S12 sl.4-34 |
| Descriptive task (S1 sl.10) | Topic-aware summarization | S9 sl.6-20 |
| Interpretation/Evaluation | Evaluation, interpretation, ROUGE, final analysis | S1 sl.22; S9 sl.22; S12 sl.34 |

The sessions are taught in this order: 1-4, 5-6, 7, 8, then 9 (summarization), 10 (Transformers/BERT), 11 (clustering), 12 (topic modelling), 13 (RAG) (sl.1 of each deck).

**How it applies to your project**

- Your ~6,500 Detik articles are the "large collection of unstructured text" from S1 sl.6. The scraper's real job is to turn Detik's HTML pages into plain article text (S1 sl.7).
- Your project combines one predictive task and two descriptive ones (S1 sl.10):
  - predictive: classifying articles into the 5 channels;
  - descriptive: topics within each class, and summaries.
- The channel labels make your corpus a labelled corpus for Task 3. For Task 4, each class becomes its own unlabelled sub-corpus (S3 sl.18). There you look for structure inside Detik's own categories (S12 sl.5).
- "Feature generation" means something different for each model:
  - BiLSTM: a vocabulary plus an embedding layer;
  - IndoBERT: its own tokenizer;
  - a bag-of-words topic model: a count or TF-IDF matrix.
- The project order differs from the lecture order in two places:
  - Task 3 needs S8 (BiLSTM) and S10 (IndoBERT) together. S7 is only an optional baseline.
  - Task 5 needs S9 last, because topic-aware summaries are built from the S11-12 outputs.
  
  A workable study order is S1, S2, S3-4, S5-6, S8+S10, S11-12, S9.
- The feedback arrows describe real work. For example, if boilerplate shows up in topic keywords, or channel names leak into the classifier's input, you go back to preprocessing. Record those loops in each notebook.
- These sessions are background only:
  - S13 RAG: there is no retrieval or QA deliverable, but its split between encoder and generator models (S13 sl.5) helps you explain where IndoBERT fits.
  - CFG parsing (S3 sl.20-22).
  - The QA and plagiarism data formats (S2 sl.14, 16).
  - Hand-computed attention (S10 sl.23-33): you need to explain it, not code it.

**Beyond the slides** (not from the lecture)

- All lecture demos are in English. Indonesian stopword lists, stemmers such as Sastrawi, and IndoBERT checkpoints come from outside the course.
- No deck covers three things you will need:
  - classification metrics beyond accuracy, such as macro-F1 and the confusion matrix;
  - a procedure for choosing the number of topics;
  - how topic outputs should drive which sentences go into a summary.
- ROUGE needs reference summaries, and Detik articles do not come with any. Where your references come from is an open design problem.

**Yours to decide and write**

- Which five channels become labels, and your argument for using channels as labels.
- Which preprocessing steps each model gets, and why.
- Which hyperparameters to tune, the final model choice, and the explanation of why BiLSTM or IndoBERT does better or worse.
- The topic method, the number of topics per class, topic names and interpretations, and whether Task 4 splits the data by true labels or predicted labels.
- Extractive or abstractive summarization, what the ROUGE references are, and your summarization conclusions.
- How you present the project as a text mining process in each notebook and video.

---

## Stage 1: Collecting the news data (5%)

**Where it's taught**
Core: Session 2, Text Data Preparation (sl.5-8, 11, 13, 15, 18). Supporting: S1 sl.6-7, 11; S3 sl.18-19; S4 sl.5; S5-6 sl.17; S8 sl.10; S9 sl.21-22; S11 sl.8; S12 sl.32.

**What the lectures say**
- *Plan first.* Collection means gathering accurate data from several sources to answer a research problem (S2 sl.5). Before starting, settle the purpose, the kind of data, and how it will be collected, stored and processed (S2 sl.6).
- *Sources and scraping.* Text can be created by you, gathered by you, gathered by a third party, or scraped from sources such as news feeds (S2 sl.7-8). Scraping extracts specific page elements, such as titles and authors, using code or a tool. Check copyright first, because some sites allow text mining only through their API (S2 sl.18).
- *Labels.* Define the classes clearly, use at least three annotators with a majority vote, label consistently, and validate with spot checks or inter-annotator agreement (S2 sl.11).
- *Shape and quality.* A classification dataset is a text column plus a label column, profiled by label shares and null rates (S2 sl.13). A news summarization set keys each record on a SHA1 hash of its URL and pairs the body with a human-written "highlights" summary. It has 287,113 ids but only 284,005 unique bodies, so some articles repeat (S2 sl.15).
- *The corpus.* Text mining expects a large collection of natural text, not HTML or XML (S1 sl.6-7), and text is noisy and comes in many formats (S1 sl.11). Annotated corpora feed supervised learning; unannotated ones feed topic modelling (S3 sl.18). Documents split into paragraphs, then sentences (S3 sl.19).
- *Downstream effects.* TF-IDF depends on its corpus (S5-6 sl.17). The LSTM pipeline starts from collected text such as news and splits the data before building a vocabulary (S8 sl.10). The right cleaning steps depend on the data (S4 sl.5). ROUGE needs reference summaries (S9 sl.21-22), dynamic topic modelling needs time slices (S12 sl.32), and clustering works best on single-theme paragraphs (S11 sl.8).

**How it applies to your project**
- Open the Task 1 notebook with your answers to the three questions on S2 sl.6.
- Your robots.txt check is the sl.18 step: Detik allows crawlers, and Kompas forbids text and data mining. Record the date you checked.
- Describe the parser as elements plus selectors: og:title, dtk:author, dtk:publishdate, dtk:keywords, and the `<p>` tags in `div.detail__body-text`. Skip foto/video/detiktv/grafis pages so only prose remains (S1 sl.11).
- Your labels come from Detik channels, not annotators. Apply the define-and-validate steps of sl.11 instead: write a channel-to-label map and spot-check each class.
- Follow the sl.13/15 layout: the `d-<id>` in each URL is your stable key, next to the url, label, title, date and raw body.
- Keep paragraph breaks (S3 sl.19, S11 sl.8) and publish dates (S12 sl.32). Detik has no "highlights" field (S9 sl.21), so decide now whether to store a candidate reference field.
- Log the junk you see ("SCROLL TO CONTINUE WITH CONTENT", "ADVERTISEMENT", "Jakarta -" datelines, "detikHealth" self-mentions that leak the label) as evidence for Task 2 (S4 sl.5).
- State the ~3-month window, because vocabulary and topics will reflect it (S5-6 sl.17).

**Beyond the slides (not from the lecture)**
- Scrape politely and resumably: add delays, send a clear User-Agent, back off on HTTP 429/5xx, and cache the raw HTML.
- Detik index pages sometimes return the wrong page; re-request when no new ids appear.
- Keep UTF-8 throughout. Deduplicate by id, then by a hash of the normalized body, to catch stories reposted in two channels.
- Channel labels are weak labels. For a hand-checked sample, Cohen's kappa = (p_o − p_e)/(1 − p_e) (observed vs. chance agreement) is the standard score; the slide names no metric.
- Add a quality table: per-class counts, articles per day, length distribution (some items are ~20 words), and dropped records with reasons.

**Yours to decide and write**
- The 5 channels, and how sub-sections such as sepakbola map to labels.
- The date range, the daily quota per label, and how to handle short days.
- The scraper's main loop (the brief bars an AI-written scraper you don't understand).
- Filtering rules (minimum length, non-article pages, duplicates, multi-page articles) and which metadata to keep.
- How far to validate the channel labels, and how you justify them.
- Your sl.6 answers, the source/tools/period/record-count statement, and the video.

---

## Stage 2: Cleaning, preprocessing and text representation (5%)

**Where it's taught**
- Core: S4 Text Preprocessing (sl.5-15) and S5-6 Text Representation (sl.4-36).
- Supporting: S1 (sl.12-19), S2 (sl.15), S3 (sl.6-10, 15-16, 24-27), S7 (sl.10-13, 18), S8 (sl.7, 9-11), S9 (sl.11-12), S10 (sl.35, 42), S11 (sl.9-10), S12 (sl.9, 20, 23), S13 (sl.12).

**What the lectures say**
- *Pipeline.* The order is preprocessing, then feature generation (bag of words or embeddings), then feature selection. Evaluation can send you back to any of these steps (S1 sl.15-16, 18-19). Text vectors are sparse, and a few function words dominate raw counts (S1 sl.12-13).
- *Cleansing.* Cleansing covers case folding, punctuation removal (`translate` or `re.sub` on `\W`) and collapsing whitespace with `\s+`. Pick the operations to fit the data and the task (S4 sl.5-7). Regex gives you character classes, quantifiers, anchors and `\b` word boundaries, and it is case-sensitive (S3 sl.6-10).
- *Tokenize, filter, normalize.*
  - Split text with `split()` or `word_tokenize` (S4 sl.9).
  - Drop stop words with NLTK's Indonesian list (S4 sl.11).
  - Stemming cuts affixes off by rule and can leave non-words. Lemmatization uses a vocabulary and the part of speech to return the dictionary form, and the deck prefers it (S4 sl.13-15).
  - POS tagging and NER are optional extras (S1 sl.17; S3 sl.15-16, 24-27).
- *Sparse vectors.*
  - A shared vocabulary gives every document a vector of the same length (S5-6 sl.4).
  - CountVectorizer counts words, loses word order and gives stop words too much weight (sl.9-13). `ngram_range` adds phrases (sl.12).
  - TF-IDF is (n/T)·log(TD/D_t), so a word that appears in every document scores 0 (sl.16-19). The weights only hold for the corpus they were computed on (sl.17).
- *Dense vectors.* Word2Vec (CBOW or skip-gram) learns small vectors from neighbouring words but handles rare and out-of-vocabulary words badly (S5-6 sl.24-27). GloVe uses global co-occurrence counts (sl.29-31), and fastText adds up character n-gram vectors (sl.34-35). All three give each word one fixed vector, while in BERT-style models a word's vector changes with its context (sl.22).
- *Match the model.* Averaged word vectors suit classical classifiers, and padded sequences suit LSTM/CNN models (S7 sl.10-13, 18).
  - LSTM recipe (S8 sl.9-11): clean the text, then split the data *before* building the vocabulary or training Word2Vec, to avoid leakage. Then tokenize, drop stop words, index tokens with 0 reserved for padding, pad or truncate, and build the embedding matrix. The same deck praises LSTMs for handling modifiers like 'not' (S8 sl.7), and stop-word removal can delete exactly those words.
  - BERT uses its own tokens, starting from [CLS], up to 512 positions (S10 sl.35). Maximum sequence length is a hyperparameter (S10 sl.42).
  - Topic models and clustering use sparse count or TF-IDF matrices (S11 sl.9-10; S12 sl.9). Their quality depends on lemmatization, TF-IDF frequency bounds and chunking (S12 sl.20), and they ignore context (S12 sl.23).
  - Summarization cleans the text only to vectorize sentences, and outputs the original sentences (S9 sl.11-12). Long texts can be cut into 100-word passages (S13 sl.12).

**How it applies to your project**
- *Detik junk.* Remove 'SCROLL TO CONTINUE WITH CONTENT', 'ADVERTISEMENT', 'Jakarta -' datelines and promos. Use regex that is word-bounded and not anchored to the start of a line (S3 sl.9). The news sample in the lecture has similar boilerplate (S2 sl.15). Channel names inside the body (detikHealth, detikOto) give the label away and would inflate both classifiers' scores.
- *One raw column, four views.*
  - BiLSTM: follow the S8 recipe. Fit the vocabulary and Word2Vec on training data only, and set maxlen from your article-length distribution.
  - IndoBERT: it has its own tokenizer and a 512-token cap. You need to decide how much of the S4 processing it gets and how to truncate long articles.
  - Topic models: counts or TF-IDF. This is where stop words, df bounds and bigrams such as 'suku bunga' matter most.
  - Summarizer: needs intact sentences for the output and for ROUGE.
- *Pitfalls.* Deleting punctuation (S4 sl.6) glues 'COVID-19', 'Rp10.000' and 'anak-anak' into single tokens. Dropping digits loses match scores and rupiah amounts. Porter and WordNet (S4 sl.13-15) only work for English.
- *Evidence.* Per-class top-50 frequency tables before and after each step (like S1 sl.13), plus the vocabulary size at each step.

**Beyond the slides (not from the lecture)**
- *Indonesian tools.* Sastrawi provides a stemmer and a stop-word list. The stemmer is slow, so stem each unique word once and cache the result. You can extend NLTK's list with Detik-specific terms. Indonesian affixes (me-/di-/-kan/-nya) are mostly derivational, so the S4 distinction between stemming and lemmatization only loosely applies.
- *BERT preprocessing.* BERT-family models are usually given lightly cleaned text. Check whether your checkpoint is cased or uncased: indobenchmark/indobert-base-p1 vs indolem/indobert-base-uncased. Truncation options are head-only, head+tail, or chunking.
- *scikit-learn TF-IDF.* The default `TfidfVectorizer` does not match the slide formula. It uses raw counts, idf = ln((1+N)/(1+df)) + 1, and L2-normalized rows. Tune `min_df`/`max_df`.
- *Hygiene.* Apply NFKC normalization (it cleans up characters like `\xa0`), and remove duplicate articles before splitting.

**Yours to decide and write**
- Which junk patterns to strip, and how to handle channel names.
- How to treat punctuation, numbers and hyphenated words.
- For each branch (BiLSTM, IndoBERT, topics, summarizer): lowercasing, which stop-word list and additions, and whether to use stemming, lemmatization or neither.
- Representation settings: embedding source, maxlen and truncation, counts vs TF-IDF, n-grams, df bounds.
- The evidence you show, and the justification itself. The brief does not allow AI to write it.

---

## Stage 3: Classification with deep learning and BERT (30%)

**Where it's taught**: Core: S7 sl.5-18 (classical reference), S8 sl.5-11, 22, 24 (LSTM pipeline, BiLSTM), S10 sl.7-10, 19-21, 35-44 (attention, BERT, tuning). Supporting: S1 sl.20, 22; S2 sl.13; S3 sl.18; S5-6 sl.16, 29; S13 sl.5, 17.

**What the lectures say**:
- *Framing and the classical reference.* Classification is supervised learning. A model learns a mapping from features to classes using labelled records, then labels records it has not seen (S1 sl.20; S7 sl.5). The data is a text column plus a label column (S2 sl.13), which is an annotated corpus (S3 sl.18). The classical route feeds TF-IDF vectors to an ML classifier, because raw counts give too much weight to common words (S7 sl.5). The formula is tf-idf = (n/T)·log(TD/D_t) (S5-6 sl.16). For SVM/RF, word2vec vectors are averaged into one document vector, V_doc = (1/N)Σv_i. Zero-padded sequences are meant for LSTM/CNN (S7 sl.11-13, 18). A LinearSVC scored 64% accuracy on the averaged vectors and 27% on the padded sequences (S7 sl.15).
- *LSTM.* Bag-of-words cannot tell "not good, absolutely terrible" apart from the reversed sentence. An LSTM carries a hidden state and a cell state from word to word, and its forget, input and output gates decide what information is kept (S8 sl.5-7).
- *Pipeline.* Clean → split into train/val/test *before* building the vocabulary or training Word2Vec, to avoid leakage → tokenize → remove stop words → then two tracks: (A) train Word2Vec and map each word to its vector; (B) index tokens starting from 1 (0 = padding) and pad or truncate to a fixed length → build the embedding matrix (vocab × dim, where row i is word i's vector) → the Embedding layer looks up rows, turning 2-D ID arrays into a 3-D tensor → train and evaluate (S8 sl.9-11).
- *BiLSTM.* The last forward state and the last backward state are concatenated into Φ, followed by a Linear layer and softmax (S8 sl.22). Static embeddings give each word a single vector. Contextual embeddings (ELMo, BERT) change a word's vector depending on its context (S8 sl.24; S5-6 sl.29).
- *Attention.* LSTMs lose the influence of early tokens, cannot run their steps in parallel, and never see the whole text at once (S10 sl.7). Self-attention compares each token's Query with every token's Key (S10 sl.10): Attention(Q,K,V) = softmax(QKᵀ/√d_k)V. This runs in several heads whose outputs are concatenated (S10 sl.19-21).
- *BERT.* BERT is a 12-layer encoder that reads up to 512 positions, with [CLS] as the first token (S10 sl.35). It is pre-trained with MLM and NSP, then fine-tuned (S10 sl.36-38). In MLM, 15% of tokens are selected; of those, 80% become [MASK], 10% become a random token and 10% stay unchanged. The NSP figure shows an FFNN+softmax head on the [CLS] output (S10 sl.38). BERT belongs to the "understanding" (encoder) family of models (S13 sl.5). Fine-tuning updates the model's weights and costs GPU time (S13 sl.17).
- *Tuning and evaluation.* Hyperparameters to vary: learning rate (1e-5/3e-5/5e-5), batch size (16/32/64), epochs (too few underfits, too many overfits), maximum sequence length, classifier head and optimiser (S10 sl.42). Stop early when validation loss stops improving (S10 sl.43), record every run (S10 sl.44), and go back to earlier steps if results are poor (S1 sl.22). Accuracy is the only metric the slides show (S7 sl.15).

**How it applies to your project**:
- Keep `id, text, label` for the 5 channels and show the count per class after removing duplicates (S2 sl.13). With 5 balanced classes, random guessing gives about 20%.
- BiLSTM = the S8 sl.9-11 pipeline plus the sl.22 architecture, with a 5-way softmax. Fit the vocabulary and Word2Vec on the training split only, and choose the sequence length L from the lengths of your Detik articles. S8 sl.10 says to remove stop words, but sl.7 says modifier words matter, so test this rather than assuming. Settings you can tune: embedding dimension and initialisation, frozen vs trainable embeddings, L, hidden size, number of layers.
- IndoBERT = S10 sl.35-38 with a 5-class head on [CLS]. It has its own tokenizer and a 512-token limit, so it should get a lightly cleaned text column. Its tunable settings come from S10 sl.42.
- Angles for the comparison: word order (S8 sl.7), static vs contextual meaning (S5-6 sl.29), long-range context and speed (S10 sl.7, 10), pre-training vs training from scratch (S10 sl.36), and compute cost (S13 sl.17). Attention can pick up a signal from any token, so channel names left in the text ("detikHealth", "detikOto") would give away the label.
- A TF-IDF + linear model like the one in S7 is an optional point of reference. The brief does not require it.

**Beyond the slides** (not from the lecture):
- Metrics: per-class precision TP/(TP+FP), recall TP/(TP+FN), F1 = 2PR/(P+R), macro-F1, and a confusion matrix (sklearn `classification_report`). Use a stratified split, tune on the validation set, and touch the test set only once. Use the same split and seeds for both models, and report training time and parameter count.
- Mask the padding (`mask_zero` in Keras, `pack_padded_sequence` in PyTorch) and reserve an index for unknown words.
- IndoBERT: load `indobenchmark/indobert-base-p1` or `indolem/indobert-base-uncased` with `AutoModelForSequenceClassification(num_labels=5)` and train with AdamW plus warmup. Long articles need truncation, either keeping the head or keeping head+tail. BERT-base uses learned position embeddings and 768-d hidden states, not the sinusoidal 512-d example in S10. Fine-tuning results vary from seed to seed.
- Errors in the slides: S10 sl.29 divides by √3, but d_k is 5 in that example. S10 sl.42's claim that larger batches generalise better is disputed. S7's Version 2 reduces each word to a single number, so it is not how BiLSTM input should look. S10 sl.35 says BERT was pre-trained on "more than 3 thousand words"; the BERT paper reports about 3.3 billion (BooksCorpus 800M + Wikipedia 2,500M), and "12 layers" is BERT-base (BERT-large has 24).

**Yours to decide and write**:
- Which ≥2 hyperparameters to tune for each model, which values to try, and the final settings.
- Embedding initialisation, sequence length, truncation strategy, which IndoBERT checkpoint, and how much preprocessing each model's input gets.
- Split scheme (random stratified vs time-based), which metric to headline, and whether to add a classical baseline.
- The BiLSTM-vs-IndoBERT comparison, why one is better or worse, the final model choice, and what the confusions between channels mean.
- Whether the per-class topic modelling uses the true labels or the predicted labels.

---

## Stage 4: Topic modelling per class (30%)

**Where it's taught**: Session 12 *Topic Modelling* (sl.4-7 concepts, sl.9-20 LSA/LDA, sl.22-32 BERTopic, sl.34 coherence, sl.35 code link) and Session 11 *Text Clustering* (sl.5-15). Supporting slides: S5-6 sl.11-20, S3 sl.18, S1 sl.21, S4 sl.11, S13 sl.12.

**What the lectures say**
- **Goal.** Topic modelling is unsupervised. It finds the latent structure of a whole corpus rather than labelling individual documents, and it is still useful when explicit categories already exist (S12 sl.4-5; S3 sl.18). A topic is a probability distribution over words or n-grams, and each document is a mixture of overlapping topics (S12 sl.4-5, sl.7).
- **Clustering vs topic modelling.** Clustering groups similar documents (S1 sl.21; S11 sl.5) and puts each document in exactly one group (S11 sl.7). Topic models instead give mixtures and suit long multi-theme texts; clustering suits short single-theme units (S12 sl.6). The clustering pipeline: TF-IDF over tokens and bigrams, then K-means with k fixed in advance, then label each cluster by the top terms of its members' summed TF-IDF vectors (S11 sl.9, sl.12). Expect uneven cluster sizes and one meaningless catch-all cluster (S11 sl.11, sl.13).
- **LSA.** Build a sparse TF-IDF document-term matrix, apply SVD and keep the top-k singular values; each remaining dimension is read as a topic (S12 sl.9-10; S5-6 sl.16). The matrix is factored as U (documents→topics) × Σ (topic importance) × V* (topics→words), and word weights can be negative (S12 sl.12-13).
- **LDA.** A probabilistic model. Dirichlet priors keep the number of topics per document and dominant words per topic small, and the model is fitted by repeated sampling. It outputs positive probabilities but is much slower than LSA (S12 sl.14-16, sl.19). The slides suggest an LSA/NMF baseline first, LDA for finer topics, and say feature engineering matters more than the algorithm (S12 sl.19-20).
- **BERTopic.** Embeddings capture context that bag-of-words misses (S12 sl.23-24). The pipeline is SBERT → UMAP → HDBSCAN (dense regions become topics, sparse points outliers) → CountVectorizer → c-TF-IDF (S12 sl.27, sl.29-30). The c-TF-IDF weight is W(t,c) = tf(t,c) × log(1 + A/tf(t)): tf(t,c) is the term's frequency in cluster c, tf(t) its frequency across all clusters, A the average number of words per cluster (S12 sl.31). Layers are swappable, e.g. k-Means for HDBSCAN (S12 sl.28). Computing c-TF-IDF per time slice gives dynamic topics (S12 sl.32).
- **Number of topics.** You set k yourself in LSA and K-means (S12 sl.10; S11 sl.9); HDBSCAN derives topics from density (S12 sl.30).
- **Evaluation.** Coherence measures how related a topic's top words are. The slides list C_v (sliding window, 0-1, higher is better), UMass (faster, agrees less with human judgement) and NPMI (S12 sl.34). For clusters they give Silhouette (higher is better), Davies-Bouldin (lower is better) and human reading of samples (S11 sl.15).
- **Representative documents and visuals.** Representative documents are not taught directly. The nearest hooks are U (S12 sl.12) and top-K ranking by similarity (S13 sl.12). For visuals the slides show an intertopic distance map, an in-topic vs corpus term-frequency chart (S12 sl.17-18) and cluster-size bars (S11 sl.11).

**How it applies to your project**: Each class of about 1,300 articles gets its own model, so you fit five in total. Inside one channel, words found in almost every article (channel names, "detikcom", "Jakarta" datelines, "mengatakan") behave like the slides' "coronavirus" example and will fill the keyword lists unless you filter them (S5-6 sl.13; S4 sl.11). Bigrams such as "suku bunga" make keywords easier to read (S5-6 sl.12). In c-TF-IDF, "class c" means a topic cluster inside one channel, not the Detik label. A typical Detik article covers one event, which is relevant when weighing mixture against single assignment (S12 sl.6). If you save publish dates, you can show topics over time (S12 sl.32). Outlier and catch-all topics need a plan before Task 5, because Task 5 must cover every topic.

**Beyond the slides (not from the lectures)**
- No slide explains how to choose k. A common approach is to sweep a range of k per class, plot coherence (gensim `CoherenceModel`) and topic diversity, and read the resulting topics. In BERTopic the count is controlled by `min_topic_size` or `nr_topics`.
- Slide 34 labels C_v as "NPMI". C_v does use NPMI internally (combined with a cosine-similarity step over a sliding window), but it is a different score from plain NPMI coherence, which the slide also lists separately.
- For representative documents, use the highest topic probability (LDA), the highest U weight (LSA), or `get_representative_docs()` (BERTopic).
- LDA takes raw counts, while NMF and LSA take TF-IDF. For plots, use pyLDAvis or BERTopic's `visualize_*` functions.
- BERTopic's default embedding model is English, so use a multilingual one; it will truncate long articles. Fix UMAP's `random_state` so results are reproducible.
- BERTopic's GPT/T5 option names topics automatically, which the brief forbids.

**Yours to decide and write**
- Which method to use, why, and whether to compare it with a baseline.
- The unit of analysis, the preprocessing and the representation.
- Whether to group articles by their channel labels or by the classifier's predictions.
- The number of topics per class, and the evidence for it.
- How to treat outlier and catch-all topics.
- Topic names and interpretations.
- Which representative documents and visualizations to show, and what you conclude from them.

---

## Stage 5: Topic-aware summarization and ROUGE (30%)

**Where it's taught**: Session 9 (Text Summarization) is the main deck: the two paradigms are sl.5-7, extractive sl.9-12, abstractive sl.14-20, sample data sl.21 and ROUGE sl.22. Other decks that support it: S8 sl.14, 21 and S10 sl.12-14 (encoder-decoder models and attention); S13 sl.9, 13 (hallucination and the generator); S5-6 sl.22 and S7 sl.11 (sentence vectors); S12 sl.19, 31 and S11 sl.13 (topic links); S2 sl.15 and S3 sl.12 (reference summaries and n-grams); S1 sl.10; S4 sl.9.

**What the lectures say**: Summarization is a descriptive text-mining task (S1 sl.10). It makes a text shorter without changing what it means (S9 sl.5-6). Extractive methods work like a highlighter: they copy existing sentences without changing them. Abstractive methods work like a writer: they produce new sentences, but need far more parameters and data (S9 sl.14-15).

*Extractive*: every method follows three steps: represent, score, rank (S9 sl.9-10). The deck expands these into six stages (S9 sl.11-12):
1. Preprocess the text.
2. Turn each sentence into a vector, either sparse TF-IDF or a dense BERT-style embedding.
3. Score each sentence, in one of two ways. The graph way is TextRank: sentences are nodes, edges are weighted by cosine similarity, and PageRank-style propagation gives high scores to central sentences. The feature way scores position, length, keywords and numbers.
4. Keep the top-N sentences, or a set percentage of the length.
5. Remove redundancy with MMR (Maximal Marginal Relevance). MMR weighs how relevant a sentence is against how different it is from the sentences already chosen.
6. Put the chosen sentences back in their original order.

Other ways to get sentence vectors are doc2vec or BERT [CLS] encodings (S5-6 sl.22) and averaged word vectors (S7 sl.11).

*Abstractive*: this uses a Transformer seq2seq model (S9 sl.15-16). S8 sl.14 lists "article to abstract" as a seq2seq task. The encoder builds contextual representations with self-attention (S9 sl.17). The decoder produces one token at a time and is trained with teacher forcing and a cross-entropy loss (sl.18). Encoder-decoder attention lets each output word look back at the source words that matter for it (sl.19; S10 sl.12-14; S8 sl.21). S9 sl.20 compares five options:
- PEGASUS: strong on news.
- BART: input limited to about 1024 tokens.
- T5.
- PRIMERA: built for many documents into one summary.
- Prompted LLMs: expensive and can hallucinate.

A generator can confidently state false facts (S13 sl.9). It can also write text that appears word for word in no single source (S13 sl.13).

*ROUGE* (S9 sl.22). The slide's formula is:

`ROUGE-N = Σ_{r_i∈reference} Σ_{gram∈r_i} Count(gram, candidate) / Σ_{r_i∈reference} numNgrams(r_i)`

It measures what share of the reference's n-grams (S3 sl.12) appear in the candidate summary, so it is a recall measure. The other variants on the slide:
- ROUGE-L uses the longest common subsequence.
- ROUGE-W weights that subsequence toward consecutive matches.
- ROUGE-S counts skip-bigrams.

Every ROUGE score needs a human-written reference summary, like the `highlights` column that sits next to each article in the sample data (S9 sl.21; S2 sl.15).

No slide teaches topic-aware summarization. The closest material is c-TF-IDF topic keywords (S12 sl.31), LSA listed as a use for summarization (S12 sl.19), and the advice to split text into single-theme units such as sentences before grouping (S11 sl.13).

**How it applies to your project**:
- **Structure**: summarize each channel, then each topic from Task 4 inside that channel.
- **Topic coverage**: the top-N from slide 12 becomes a quota per topic. That quota is what guarantees every topic is represented, and a table mapping each summary sentence to its topic can show it.
- **MMR**: relevance can be measured against the topic's keywords. The novelty term stops Detik's follow-up stories from repeating the same fact.
- **Ordering**: the sentences come from many articles, so "original order" needs a rule, for example publish date.
- **Which text to select from**: take sentences from cleaned but unstemmed text. The stopword removal and stemming in S9 sl.11 then only affect the vectors used for scoring.
- **Junk sentences**: leftover "SCROLL TO CONTINUE WITH CONTENT" lines and "Jakarta -" datelines become candidate sentences. A position feature would pick the dateline sentence first.
- **Abstractive route**: one topic's articles are far longer than BART-style input limits, so you would extract first and then generate. All the models on slide 20 are English.
- **ROUGE references**: Detik has no `highlights` field, so reference summaries must be created. Because the slide's ROUGE-N is recall, longer summaries score higher, so compare methods at the same length.

**Beyond the slides (not from the lectures)**:
- **ROUGE details**: standard ROUGE clips matches (the slide's formula does not) and reports precision, recall and F1. Most papers report ROUGE-1, ROUGE-2 and ROUGE-L F1. In the `rouge-score` package, set `use_stemmer=False`, because its stemmer is the English Porter stemmer.
- **Sentence splitting**: S4 sl.9 only teaches word tokenization. NLTK's Punkt has no Indonesian model, so handle abbreviations like dr., Rp and No., and numbers like 1.500.
- **Where references can come from**:
  - summaries you write by hand for a sample
  - pseudo-references such as the headline, lead paragraph or meta description (these favour extractors that pick the lead)
  - sanity checks on IndoSum, Liputan6 or the Indonesian part of XL-Sum
- **Baselines**: Lead-k (first k sentences) and Random-k.
- **Formulas**: TextRank's damping factor is usually d ≈ 0.85. MMR picks argmax[λ·Sim(s, topic) − (1−λ)·max Sim(s, selected)].
- **Indonesian models**: IndoBART-v2, mT5 fine-tuned on XL-Sum, and multilingual sentence-transformers for dense sentence vectors.
- **Limits of ROUGE**: it only matches words, so it cannot see paraphrase or hallucinated facts, and Indonesian affixes reduce exact matches. Check a few summaries by hand for faithfulness.
- **LLM APIs**: ask the lecturer before using one as the summarizer, because of the brief's AI rules.

**Yours to decide and write**:
- Extractive or abstractive, and the specific algorithm or model, with your justification.
- How sentences are assigned to topics, and whether the groups come from channel labels or from the classifier's predictions.
- The quota per topic (equal or proportional to topic size), the total summary length, and what to do with outlier or catch-all topics.
- The sentence representation, the scoring method, the MMR λ and the ordering rule.
- Where the reference summaries come from, and how that choice biases the scores.
- Which ROUGE variants to report, and how the text is normalized before scoring.
- What the ROUGE results mean, and every summarization conclusion. The brief forbids AI from writing these.
