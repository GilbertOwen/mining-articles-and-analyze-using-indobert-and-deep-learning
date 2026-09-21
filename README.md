# Text Mining Final Project — DTSC6008001 (AY 2026/2027)

End-to-end text mining on scraped Indonesian news:
scraping → cleaning → preprocessing → classification (deep learning + BERT) → topic modelling per class → topic-aware summarization → final analysis.

Brief: `Submit Form Final Project DTSC6008001.docx` (Downloads folder).
Lecture-to-project guide: [`docs/lecture_guide.md`](docs/lecture_guide.md), covering what each session teaches for each stage, with slide references.

## Grading and notebooks

| Task | Weight | Notebook |
|---|---|---|
| 1. News data collection | 5% | `notebooks/01_data_collection.ipynb` |
| 2. Text preprocessing | 5% | `notebooks/02_preprocessing.ipynb` |
| 3. Classification: deep learning + BERT | 30% | `notebooks/03_classification.ipynb` |
| 4. Topic modelling per class | 30% | `notebooks/04_topic_modelling.ipynb` |
| 5. Topic-aware summarization | 30% | `notebooks/05_summarization.ipynb` |

**Submission:** notebooks (code cells + markdown explanations), a **public video for every task** (link at the top of its notebook), and the AI Use Declaration form, zipped as `NIM_Name.zip` and submitted through Exam Apps.

## AI rules (AI use type: Partial, up to 50%)

- **Allowed:** brainstorming domains and ideas, scraping strategies and tools, candidate preprocessing techniques, concept explanations, hyperparameter options, debugging, grammar/readability, visualization suggestions. You verify everything.
- **Not allowed from AI:**
  - a complete scraper you don't understand
  - your final preprocessing justification
  - the choice of final model
  - the deep learning vs BERT comparison
  - topic names and interpretations
  - the summarization evaluation conclusions
  - your answer to the verification question
  - any fabricated data, metrics, ROUGE scores or references
- Keep `ai_usage_log.md` up to date. The declaration form asks for the tools, key prompts and a % estimate. The form's template says "Film Literacy"; change it to Text Mining.

## Folder layout

```
data/raw/         scraped files (never edit by hand)
data/processed/   cleaned datasets and train/val/test splits
notebooks/        one notebook per task
models/           saved models
figures/          charts for the notebooks and videos
```

`data/` and `models/` are in `.gitignore`: keep the scraped articles private.

## Plan

### Step 0 — Setup
- [x] Project folder
- [ ] Decide where to train: local RTX 3060 6 GB (IndoBERT-base at 256 tokens, fp16, batch ~8) or Colab
- [ ] Keep `ai_usage_log.md` updated as you go

### Step 1 — Data collection (5%) → `01_data_collection.ipynb`
- [ ] Pick 5 Detik channels as labels (table in the notebook)
- [ ] Decide the period (suggested 1 Jun – 31 Aug 2026) and the quota per label per day (suggested 15)
- [ ] Pilot: 1 label × 2 days, compare 5 articles with the website
- [ ] Write the main loop: stage A builds the URL list, stage B downloads the articles, and a rerun resumes where it stopped
- [ ] Full run (~4–6 hours, overnight)
- [ ] Checks: counts per label, empty/short articles, duplicates, date range, spot-check
- [ ] Document: source, tools, method, period, records raw → cleaned, fields
- [ ] Video

### Step 2 — Cleaning + preprocessing (5%)
- [ ] Clean before splitting: duplicates (URL, text, near-copies), very short articles, promos
- [ ] Remove boilerplate that gives away the label: "SCROLL TO CONTINUE WITH CONTENT", "ADVERTISEMENT", "detikcom"/"detikHealth" mentions, "Jakarta -" datelines
- [ ] Leak check: train TF-IDF + Logistic Regression; the top words per class shouldn't be boilerplate
- [ ] EDA: class balance, article length (sets `max_len`), top words per class
- [ ] One text column per use: `text_clean` (IndoBERT, BERTopic, summaries), `text_dl` (BiLSTM), `text_topic` (LDA)
- [ ] Test stopword removal and stemming on your data (Sastrawi turns *pemerintah*→*perintah*, *keuangan*→*uang*). Write the justification yourself.
- [ ] Video

### Step 3 — Classification (30%)
- [ ] One stratified 80/10/10 split with a fixed seed; save the IDs so both models use the same split
- [ ] Model 1: BiLSTM/CNN/GRU. Tune ≥2 hyperparameters (hidden size, dropout, learning rate, `max_len`)
- [ ] Model 2: IndoBERT (`indobenchmark/indobert-base-p1` or `indolem/indobert-base-uncased`). Tune ≥2 (learning rate, `max_len`)
- [ ] Log every run: settings, validation macro-F1, training time
- [ ] Test-set evaluation: accuracy, macro-F1, per-class report, confusion matrix, training curves, time. Read the misclassified articles.
- [ ] Write the comparison yourself: why one model does better or worse
- [ ] Video

### Step 4 — Topic modelling per class (30%)
- [ ] Group articles by the classifier's predicted class (confirm with the lecturer)
- [ ] Choose a method (LDA / NMF / BERTopic) and justify it; comparing two helps
- [ ] Number of topics per class: coherence (c_v/NPMI), topic diversity, reading the topics
- [ ] Per class: keywords, your own topic names, topic sizes, representative articles, charts (topic sizes, intertopic map, topics over time)
- [ ] Video

### Step 5 — Topic-aware summarization (30%)
- [ ] For each class and each topic, summarize its representative articles into a Class | Topic | Summary table
- [ ] Extractive (TextRank/LexRank + keyword similarity + MMR) or abstractive (`csebuetnlp/mT5_multilingual_XLSum`; select sentences first)
- [ ] Baseline: a whole-class summary that ignores topics
- [ ] ROUGE-1/2/L (`rouge-score`, `use_stemmer=False`) against references you write yourself, or against headlines/lead paragraphs
- [ ] Topic-coverage score: topic-aware summary vs baseline
- [ ] Discuss what ROUGE can't measure; write the conclusions yourself
- [ ] Video

### Step 6 — Final analysis + submission
- [ ] Final analysis: how classifier errors carry into topics and summaries, limitations, improvements
- [ ] Video links public (check in an incognito window)
- [ ] AI Use Declaration form
- [ ] `NIM_Name.zip`: notebooks, dataset (or a link), `requirements.txt`, declaration form
