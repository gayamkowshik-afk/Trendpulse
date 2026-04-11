# TrendPulse

A Python data pipeline that scrapes trending stories from [Hacker News](https://news.ycombinator.com/), classifies them by topic, cleans and analyses the data, and produces visualisation charts and a summary dashboard — all in four sequential tasks.

---

## Overview

| Task | File | What it does |
|------|------|--------------|
| 1 | `task1_data_collection.py` | Scrapes HN top stories via API, classifies each into a topic, saves raw JSON |
| 2 | `task2_data_processing.py` | Deduplicates, removes nulls, filters low-score stories, exports clean CSV |
| 3 | `task3_analysis.py` | Computes descriptive stats, derives engagement and popularity columns, saves enriched CSV |
| 4 | `task4_visualization.py` | Reads the enriched CSV and produces three charts plus a combined dashboard PNG |

---

## Project Structure

```
Trendpulse/
├── task1_data_collection.py
├── task2_data_processing.py
├── task3_analysis.py
├── task4_visualization.py
│
├── data/                                  # auto-created by Task 1
│   ├── hn_trends_<YYYYMMDD_HHMM>.json    # raw scraped data
│   ├── trends_clean.csv                  # cleaned output from Task 2
│   └── trends_analysed.csv               # enriched output from Task 3
│
└── outputs/                               # auto-created by Task 4
    ├── chart1_top_stories.png             # top 10 stories by score (horizontal bar)
    ├── chart2_categories.png              # story count per category (bar)
    ├── chart3_scatter.png                 # score vs comments, popular vs not (scatter)
    └── dashboard.png                      # all three charts in one figure
```

---

## Topics Tracked

Stories are classified into five categories using keyword matching on their titles:

| Category | Example Keywords |
|---|---|
| `technology` | ai, gpu, llm, github, linux, api |
| `worldnews` | war, election, climate, policy, china |
| `sports` | cricket, nba, fifa, tournament, goal |
| `science` | nasa, quantum, genome, mars, telescope |
| `entertainment` | netflix, anime, streaming, award, film |

Up to **25 stories per topic** are collected per run.

---

## Requirements

- Python 3.7+
- `requests`
- `pandas`
- `numpy`
- `matplotlib`

Install all dependencies:

```bash
pip install requests pandas numpy matplotlib
```

---

## Usage

Run the four tasks in order:

```bash
# Step 1: Collect data from Hacker News
python task1_data_collection.py

# Step 2: Clean and deduplicate
python task2_data_processing.py

# Step 3: Analyse and enrich
python task3_analysis.py

# Step 4: Generate charts and dashboard
python task4_visualization.py
```

Each script prints progress to the console. After Task 4 finishes, all charts are saved in the `outputs/` folder.

---

## Charts Produced (Task 4)

### chart1_top_stories.png — Top 10 Stories by Score
Horizontal bar chart of the 10 highest-scoring stories. Titles are truncated to 50 characters for readability, sorted highest-to-lowest.

### chart2_categories.png — Stories per Category
Vertical bar chart showing how many stories were collected in each of the five topic categories.

### chart3_scatter.png — Score vs Comments
Scatter plot with each story as a point. Stories above the dataset average score are labelled **Popular** (one colour) and the rest **Not Popular** (another). Useful for spotting whether high-scoring stories also attract proportionally more discussion.

### dashboard.png — Combined Dashboard
All three charts laid out side-by-side in a single 18×5 inch figure titled *TrendPulse Dashboard* — a single-image summary suitable for sharing or dropping into a report.

---

## Data Fields (Task 3 output — input to Task 4)

| Field | Type | Description |
|---|---|---|
| `post_id` | int | Hacker News item ID |
| `title` | str | Story headline |
| `category` | str | Classified topic |
| `score` | int | Upvote score |
| `num_comments` | int | Number of comments |
| `posted_by` | str | HN username |
| `url` | str | Original link |
| `scraped_at` | str | ISO timestamp of collection |
| `engagement` | float | `num_comments / (score + 1)` |
| `is_popular` | bool | `True` if score is above the dataset average |

---

## Notes

- Each run of Task 1 creates a **new timestamped JSON file** so previous runs are never overwritten.
- Task 2 always reads the **most recent** JSON from `data/`.
- Stories with a score below 5 are dropped during processing.
- The `outputs/` folder is created automatically on first run of Task 4.
- No API key is required — the pipeline uses the public [Hacker News Firebase API](https://github.com/HackerNews/API).
