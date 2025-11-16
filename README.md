# Ford GoBike — Workspace Summary

Repository root: This workspace contains two project notebooks and a data folder used by those notebooks.

Files in this repository (exact names present in workspace):

- `Part_I_exploration_template.ipynb` — exploratory notebook used to clean, inspect, and export the dataset.
- `Part_II_slide_deck_template.ipynb` — slide-deck notebook containing visualization sections and automation cells related to the Key Findings slide.
- `data/201902-fordgobike-tripdata.csv` — raw February 2019 trip data CSV included in the `data/` folder.
- `data/gobike_cleaned.csv` — cleaned dataset CSV used as the preferred input for Part II (created by Part I when run).

Data: typical columns present in the CSV files include `start_time`, `end_time`, `start_station_id`, `start_station_name`, `end_station_id`, `end_station_name`, `bike_id`, `user_type`, `member_birth_year`, `member_gender`, and `duration_sec` (the notebooks derive `duration_min`).

Key notebook behaviors (current implementation in the workspace):

- `Part_I_exploration_template.ipynb` performs data cleaning and writes an export file `data/gobike_cleaned.csv` when executed.

- `Part_II_slide_deck_template.ipynb` is arranged as a slide deck and includes:
	- A defensive data-loading code cell that prefers `data/gobike_cleaned.csv` and falls back to `data/201902-fordgobike-tripdata.csv`. The cell ensures derived fields such as `duration_min` and `start_hour` exist.
	- Visualization sections that create presentation-quality charts (hourly counts & median duration, weekday×hour heatmap, and top start stations).
	- An automation cell (store-only) that computes summary metrics — peak hour, subscriber share, median durations by user type, and top start stations — and writes those values into the notebook file metadata under `nb['metadata']['key_findings']`. This cell stores values on-disk and does not overwrite the Key Findings markdown slide by itself.
	- A Key Findings markdown slide that contains placeholder tokens. Substitution of stored values into the markdown slide is performed only if an explicit substitution helper cell is run (the notebook may include such a helper as a separate, opt-in step).

Cell references (numbered from 1 in the notebook UI):

- The defensive data-loading cell appears as the first code cell that loads data (run this before visualizations).
- The store-only auto-fill cell computes and stores key metrics (the file metadata key is `key_findings`).
- The Key Findings slide is a markdown cell in the Part II notebook that contains placeholder tokens for the stored values.

Exact runtime steps to reproduce the prepared slide deck (factual):

1. Create and activate a Python environment and install required packages. A minimal set used in the notebooks is `pandas`, `numpy`, `matplotlib`, `seaborn`, `jupyter`, and `nbformat`.

```bash
# create & activate a venv (example)
python -m venv .venv
# Bash (Git Bash / WSL):
source .venv/Scripts/activate

pip install --upgrade pip
pip install pandas numpy matplotlib seaborn jupyter nbformat
```

2. Run `Part_I_exploration_template.ipynb` if you need to regenerate `data/gobike_cleaned.csv`.

3. In `Part_II_slide_deck_template.ipynb` run the following cells (in order):

- The data-loading cell (creates `gobike_clean` and derived fields).
- The visualization cells to produce the presentation charts.
- The store-only auto-fill cell to write summary metrics into the notebook metadata key `key_findings`.

4. To export and serve slides while executing the notebook (ensures outputs and stored metadata are up-to-date):

```bash
jupyter nbconvert Part_II_slide_deck_template.ipynb \
		--to slides \
		--execute \
		--ExecutePreprocessor.timeout=600 \
		--post serve \
		--no-input --no-prompt
```

Notes on stored metrics (factual):

- The store-only auto-fill cell writes a dictionary of values to `nb['metadata']['key_findings']` in the on-disk notebook file. That metadata entry contains both simple keys (e.g., `peak_hour`, `subs_pct`, `median_sub`, `median_cust`, `top3`) and a placeholders map for programmatic substitution.
- Substitution of stored values into the Key Findings markdown slide requires an explicit substitution step (a helper cell) if you want the slide text updated programmatically; the store-only cell itself only writes metadata.

````

