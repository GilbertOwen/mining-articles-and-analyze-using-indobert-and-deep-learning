# Text Mining Final Project — Handoff Notes

Last updated: 16 Sep 2026. Working folder: `D:\text-mining-aol`.

## 1. The assignment

**Course:** BINUS DTSC6008001 Text Mining, Final Project (AY 2026/2027, Odd term).
**Brief:** `Submit Form Final Project DTSC6008001.docx` (in Downloads).

Required pipeline: Web scraping → data collection → cleaning → preprocessing → classification →
model evaluation → classification by class → topic modelling per class → topic interpretation →
topic-aware summarization → final analysis.

| # | Task | Weight | Requirements |
|---|---|---|---|
| 1 | News data collection | 5% | ≥5,000 articles, publicly accessible Indonesian news, 5 news domains as labels, via web scraping, each record has the main text. Must document: data source, scraping method/tools, collection period, number of records (+ data fields per the rubric) |
| 2 | Text preprocessing | 5% | Cleansing, tokenization, filtering; stemming/lemmatization optional. Techniques must be **justified**, not applied blindly |
| 3 | Classification: DL + BERT | 30% | Model 1: LSTM/BiLSTM/GRU/CNN. Model 2: BERT-based (IndoBERT etc.). Tune ≥2 hyperparameters each, compare and explain why one is better/worse |
| 4 | Topic modelling | 30% | Separately per class; justify method + number of topics; keywords, topic names, representative docs, visualizations |
| 5 | Topic-aware summarization | 30% | Summary per class where every identified topic is represented; extractive or abstractive; evaluated with ROUGE |

**Submission:** one `.ipynb` per task (code cells + markdown explanations), a **compulsory public video per
task** with the link in its notebook, zipped as `NIM_Name.zip` via Exam Apps, plus the AI Use Declaration form.

**AI use type: Partial (≤50%).** AI may explain concepts, suggest techniques/tools/hyperparameters, help
debug. AI must **not** produce: a complete scraper the student doesn't understand, the final preprocessing
justification, the final model choice, the DL-vs-BERT comparison, topic names/interpretations, the
summarization conclusions, the answer to the "verification question", or any fabricated data/metrics/ROUGE.
`ai_usage_log.md` tracks AI use for the declaration form.

## 2. Decisions already made

- **Source: detik.com.** Its robots.txt allows crawlers (`Allow: /`) on every channel used.
  **Kompas was rejected** — its robots.txt explicitly prohibits text/data mining and AI/ML use.
  CNN Indonesia also allows crawling (unused backup). Antara allows general crawlers.
- **5 labels → channels:**

  | Label | Detik channel(s) | Usable articles/day (measured, Wed) |
  |---|---|---|
  | business | `finance.detik.com` | ~50 |
  | technology | `inet.detik.com` | ~30 |
  | sports | `sport.detik.com` + `sport.detik.com/sepakbola` | ~65 |
  | health | `health.detik.com` | ~18 |
  | entertainment | `hot.detik.com` | ~22 |

  health is the binding constraint: it sets the per-day quota for every label.
- **Provisional plan:** period 1 Jun – 31 Aug 2026, `PER_DAY = 15`, `SEED = 42` → ~1,300/label (~6,500 total),
  leaving buffer above the 5,000 minimum after cleaning. To be confirmed by a volume probe over ~6 sample
  dates (mix of weekdays/weekends): `PER_DAY = min(15, floor(median of smallest label))`,
  `days_needed = ceil(1300 / PER_DAY)`.
- **Planned models:** BiLSTM + IndoBERT (`indobenchmark/indobert-base-p1` or `indolem/indobert-base-uncased`).
- **Hardware:** RTX 3060 Laptop 6 GB VRAM, 16 GB RAM. Python 3.10 (laragon) with requests, bs4, lxml,
  pandas, tqdm, jupyter installed.

## 3. Verified facts about Detik (checked live, 10 Sep 2026)

- **Index page:** `https://<channel>/indeks?date=YYYY-MM-DD&page=N` — 20 articles/page; page 1's pagination
  links reveal the day's last page. The ISO date format works on both of Detik's page templates (detikInet
  uses a newer template that *requires* ISO; classic channels also accept MM/DD/YYYY). Archive goes back to
  at least Jun 2025.
- **Known quirk:** the index randomly serves a different page of the same date (its cache appears to ignore
  the `page` parameter). A `requests.Session` always returns page 1; `/indeks/N` paths 404. Workaround:
  re-request when a page contains no article ids you haven't seen.
- **Article URL:** `https://<channel>/<section>/d-<id>/<slug>`. `<id>` is unique → use it to dedupe.
  Skip sections matching `foto|video|detiktv|grafis` (photo/video/infographic pages, little text).
- **Article page:** body = every `<p>` inside `div.detail__body-text` (multi-page articles are fully present
  in the HTML as `section.multi-card`). Metadata from `<meta>` tags: `og:title`, `dtk:publishdate`,
  `dtk:author`, `dtk:keywords` — identical across both templates, unlike the visible HTML.
- **Junk found inside paragraphs** (leave raw at scrape time, clean in Task 2): "SCROLL TO CONTINUE WITH
  CONTENT" (12/12 test articles), "ADVERTISEMENT" (4/12, sometimes mid-paragraph), "detikcom"/"detikHealth"
  self-mentions (6/12 — **these leak the label**), "Jakarta -" datelines, detikevent/detikcourse promos.
  Some articles are genuinely ~20 words.
- **Expected label overlaps** (material for Task 3 error analysis): business↔technology (telco, e-commerce),
  sports↔entertainment (celebrity athletes), health↔technology (health apps/research),
  health↔entertainment (celebrity illness).

## 4. Tested helper functions

Written and tested against all channels on 10 Sep 2026 (both page templates, multiple dates). These are
helpers, not the scraper's main loop — the loop is the student's own work per the brief.

```python
HEADERS = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 "
                         "(KHTML, like Gecko) Chrome/128.0 Safari/537.36"}
SKIP_SECTIONS = re.compile(r"foto|video|detiktv|grafis", re.I)


def fetch(url, params=None, delay=1.5):
    """GET a page politely (pause after every request) and return it parsed."""
    r = requests.get(url, params=params, headers=HEADERS, timeout=20)
    time.sleep(delay)
    r.raise_for_status()
    return BeautifulSoup(r.text, "lxml")


def collect_urls(base, day, max_tries=4):
    """All article URLs a Detik channel listed on one day, e.g. base='https://finance.detik.com'."""
    pattern = re.compile(re.escape(base) + r"/([\w-]+)/d-(\d+)/[\w-]+")
    seen, urls = set(), {}
    page = last_page = 1
    while page <= last_page:
        for _ in range(max_tries):
            soup = fetch(f"{base}/indeks", params={"date": day.isoformat(), "page": page})
            links = [a["href"] for a in soup.select("a[href]")]
            ids = set(re.findall(r"/d-(\d+)/", " ".join(links)))
            if ids - seen:   # Detik sometimes returns a page we already have; if so, ask again
                break
        seen |= ids
        if page == 1:        # page 1 links to the day's last page ("1 2 3 4 5 ... 15")
            last_page = max([int(n) for h in links for n in re.findall(r"[?&]page=(\d+)", h)], default=1)
        for h in links:
            m = pattern.match(h)
            if m and not SKIP_SECTIONS.search(m.group(1)):
                urls[m.group(2)] = m.group(0)
        page += 1
    return list(urls.values())


def meta(soup, **attrs):
    tag = soup.find("meta", attrs=attrs)
    return tag.get("content") if tag else None


def parse_article(url):
    """Fetch one Detik article; content = raw paragraphs, cleaned in Task 2."""
    soup = fetch(url)
    body = soup.select_one("div.detail__body-text")
    paragraphs = [p.get_text(" ", strip=True) for p in body.find_all("p")] if body else []
    return {
        "url": url,
        "title": meta(soup, property="og:title"),
        "date": meta(soup, name="dtk:publishdate"),
        "author": meta(soup, name="dtk:author"),
        "tags": meta(soup, name="dtk:keywords"),
        "content": "\n".join(p for p in paragraphs if p),
    }
```

## 5. What's in the repo

```
README.md                            plan as a checklist, grading, AI rules, folder layout
HANDOFF.md                           this file
ai_usage_log.md                      AI usage rows for the declaration form
requirements.txt                     task-1 packages (installed) + later tasks listed
docs/lecture_guide.md                ~5,800 words: all 12 lecture decks mapped onto the 5 project
                                     stages, with slide citations (S12 sl.34 = Session 12, slide 34)
notebooks/data_collection_#1.ipynb   the student's working Task 1 notebook
references/01_data_collection.ipynb  original scaffold, kept for reference
data/raw/, data/processed/, models/, figures/   (empty; data/ and models/ are gitignored)
```

Lecture decks live in `D:\Text-mining-material` (12 unique decks, Sessions 1–13; Text Representation spans
S5–6; `Text Representation (1).pptx` is a byte-identical duplicate).

## 6. Current state of the Task 1 notebook

Section lettering used by the student: A–N, at `###` level under the `# 1. Data Collection` title.

| Section | Status |
|---|---|
| A. Objective | done |
| B. Data Source | done |
| C. How Is The Structure of Detik.com | done |
| D. Import Libraries | done, runs |
| E/F. Helper Functions + code | done, runs |
| **G. Configuration** | **next** — CHANNELS, LABELS, START/END, PER_DAY, SEED, RAW_DIR/URLS_CSV/ARTICLES_CSV |
| H. Pilot Test | not started — `collect_urls` on one channel/day + `parse_article` on 3, compared against the live site |
| I. Volume Probe | not started — ~6 sample dates → set PER_DAY and the date range |
| J. Stage A (URL list) | not started — **student writes this loop** |
| K. Stage B (download articles) | not started — **student writes this loop** |
| L. Data Quality Checks | not started |
| M. Data Collection Summary | not started — must contain data source / scraping method+tools / collection period / number of collected records / data fields, in the brief's own wording |
| N. Limitations | not started |

**Stage A spec** (student's own implementation): seed once; iterate dates × labels; collect from every base
of a label; dedupe by article id; `random.sample(urls, min(PER_DAY, len(urls)))`; append `label,date,url` to
`urls.csv` after each label-day; resume by skipping `(date,label)` pairs already present. ~1 hour.

**Stage B spec:** read `urls.csv`; skip URLs already in `articles.csv` (resume); `parse_article` in
try/except; append each row immediately (`to_csv(mode="a", header=not exists, index=False)`); log failures to
`failed.csv` and retry once at the end; tqdm progress. ~1.9 s/article → ~3.5 h for ~6,500 articles; run
overnight.

**Checks before Task 1 is done:** counts per label (≥1,000 each, ≥5,000 total), empty/short content,
duplicate urls/titles, date coverage per month, the `content.str.contains("detik")` share (leakage for Task 2
to clean), and 10 random URLs compared against the live site by eye. Snapshot `articles.csv` afterwards.

## 7. Notes for whoever picks this up

- The student writes the scraping loops, all justifications, the model comparison, topic names and the
  summarization conclusions. Explain, review and debug — don't hand over those parts.
- Prior sessions burned ~3M tokens on a multi-agent workflow and hit usage limits twice. Keep it lean: the
  expensive outputs are already on disk (`docs/lecture_guide.md`), so read those rather than re-deriving.
- Two errors spotted in the lecture slides, worth knowing if the student cites them: S10 sl.29 scales
  attention by √3 when that example's d_k is 5 (W_K is 512×5 on sl.26), and S10 sl.35 states BERT was
  pre-trained on "more than 3 thousand words" (the real figure is ~3.3 billion).
- Open question for the lecturer: whether Task 4's per-class topic modelling groups articles by the true
  channel labels or by the classifier's predicted labels. The brief's wording implies predicted.
