 # Ford GoBike — Part I: Dataset Exploration
 ## by John Burke 

 This repository contains a short exploratory analysis of the Ford GoBike February 2019 trip data and a cleaned export used for downstream analysis and presentation.

 ## Dataset

 - Source files (included):
     - `data/201902-fordgobike-tripdata.csv` — raw trip records CSV
     - `data/gobike_cleaned.csv` — cleaned/exported dataset produced by the notebook

 - Typical columns (examples):
     - start_time, end_time (timestamps)
     - start_station_id / start_station_name, end_station_id / end_station_name
     - bike_id, user_type (Subscriber / Customer)
     - member_birth_year, member_gender
     - duration_sec (and derived `duration_min`)

 ## Preprocessing performed (summary)

 - Parsed `start_time` and `end_time` to pandas datetime to extract hour/day and support time-based grouping.
 - Created `duration_min` from `duration_sec` for clearer, human-friendly units.
 - Filtered or flagged implausible durations (very small or extremely long values) for downstream plots and summary statistics.
 - Derived common time features: `start_hour` and `start_weekday` (day name).
 - Saved a cleaned CSV (`data/gobike_cleaned.csv`) so other parts of the project can load a reproducible cleaned dataset.

 ## Exploratory findings (summary)

 - Trip duration distribution is strongly right-skewed: most trips are short (many under 30 minutes) with a long tail of much longer rides. Use medians/percentiles or log-scaled plots to summarize central tendency.
 - Strong weekday commute pattern: clear morning and evening peaks on weekdays. Weekends show flatter patterns and different timing for peak activity (often afternoons).
 - User-type differences: `Subscriber` trips dominate counts and tend to have shorter median durations; `Customer` (casual) trips show higher median durations consistent with leisure use.
 - Station concentration: a small set of stations (downtown/transit hubs) account for a large share of trip starts/ends.
 - Demographic fields are partially populated: `member_birth_year` and `member_gender` are useful where present but often contain missing values or require cleaning (plausibility checks on birth years).
 - Long-duration outliers can strongly affect mean-based summaries; medians and trimmed/winsorized statistics give more robust insights.

````markdown
# Ford GoBike — Project Overview
## by John Burke

This repository contains exploratory analysis and a slide-deck template for the February 2019 Ford GoBike trip data.

## Files included

- `Part_I_exploration_template.ipynb` — exploratory notebook used to clean and inspect the dataset.
- `Part_II_slide_deck_template.ipynb` — slide-deck notebook with three visualization sections and automation cells for storing/publishing key findings.
- `data/201902-fordgobike-tripdata.csv` — the raw February 2019 trip records CSV (source provided with the repo).
- `data/gobike_cleaned.csv` — cleaned/exported dataset produced by the notebooks (used as the preferred input for Part II).

## Data schema (typical columns)

- `start_time`, `end_time` (timestamps)
- `start_station_id`, `start_station_name`, `end_station_id`, `end_station_name`
- `bike_id`, `user_type` (Subscriber / Customer)
- `member_birth_year`, `member_gender`
- `duration_sec` (and derived `duration_min`)

## Notebooks — what they do (factual)

- `Part_I_exploration_template.ipynb` performs cleaning and exploratory analysis and can produce `data/gobike_cleaned.csv` as an export of the cleaned dataset.

- `Part_II_slide_deck_template.ipynb` is organized as a slide deck and includes:
  - A defensive data-loading cell that prefers `data/gobike_cleaned.csv` and falls back to the raw CSV if needed (it also ensures derived fields like `duration_min` and `start_hour` exist).
  - Three visualization sections (hourly counts & median duration, weekday×hour heatmap, top start stations).
  - An automation cell that computes summary metrics (peak hour, subscriber share, median durations, top start stations) and stores those values into the notebook's on-disk metadata under the key `key_findings`. This store-only behavior writes values into `nb['metadata']['key_findings']` and does not automatically overwrite the Key Findings markdown cell.
  - A Key Findings markdown slide that contains placeholder tokens. Substitution into that slide is performed only if an explicit substitution helper cell is run (the notebook may include a helper to perform the substitution separately).

## Runtime steps (reproduce)

1. Create and activate a Python environment and install required packages (minimal set): `pandas`, `numpy`, `matplotlib`, `seaborn`, `jupyter`, `nbformat`.

```bash
# Create & activate a venv (example)
python -m venv .venv
# Bash (Git Bash / WSL):
source .venv/Scripts/activate

pip install --upgrade pip
pip install pandas numpy matplotlib seaborn jupyter nbformat
```

2. Open `Part_I_exploration_template.ipynb` and run top-to-bottom to regenerate `data/gobike_cleaned.csv` (if you need the cleaned CSV).

3. Open `Part_II_slide_deck_template.ipynb` and run the following cells in order to prepare slides:

- Run the data-loading cell (it creates `gobike_clean` with derived fields).
- Run the visualization cells to generate the charts and any summary variables used interactively.
- To store computed summary metrics into the notebook (for later substitution), run the store-only auto-fill cell. That cell writes values to `nb['metadata']['key_findings']` on disk.

4. Export and serve slides (executes the notebook during conversion so outputs and stored metadata are up-to-date):

```bash
jupyter nbconvert Part_II_slide_deck_template.ipynb \
    --to slides \
    --execute \
    --ExecutePreprocessor.timeout=600 \
    --post serve \
    --no-input --no-prompt
```

This command executes the notebook and then serves the generated slides via a local HTTP page.

````


