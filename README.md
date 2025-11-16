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

 ## Presentation threads — what to polish

 I recommend focusing on 1–2 sharp stories for the slide deck rather than many small plots. Two complementary threads:

 1) Commute patterns (time × user_type)
        - Visuals: hourly time series by `user_type`, heatmap of mean/median duration by weekday × hour.
        - Message: show how subscribers dominate commute peaks and have shorter trips during commute hours while casual users dominate leisure times.
        - Design change: aggregate to hourly bins and annotate commute windows; prefer median lines or percentile bands.

 2) User type vs trip characteristics + top stations
        - Visuals: side-by-side bar charts (counts) and box/violin plots (median durations) comparing `Subscriber` vs `Customer`; top-10 stations by starts/ends (bar chart or small map overlay).
        - Message: highlight practical differences in behavior and where the system is most heavily used.
        - Design change: simplify to 2–3 clear charts, use a consistent color palette, and annotate important stations/time windows.

 ## Visual design notes 

 - Replace many exploratory plots with a small set of polished charts (2–4) that answer the chosen questions.
 - Use medians, percentiles, and log-scale views for duration to handle skew. Annotate axes clearly.
 - Use color and legends consistently (e.g., one color per `user_type`) and add short captions or Q/V/O (Question–Visualization–Observation) notes for each slide.

 ## Files of interest

 - `Part_I_exploration_template.ipynb` — the filled exploratory notebook (univariate, bivariate, multivariate views)
 - `data/201902-fordgobike-tripdata.csv` — raw data
 - `data/gobike_cleaned.csv` — cleaned export created by the notebook
 - `Part_II_slide_deck_template.ipynb` — starter slide-deck notebook for presentation

 ## How to reproduce locally

 1. Create a Python environment and install dependencies. A minimal set includes pandas, numpy, matplotlib, seaborn, jupyter, and nbformat.

 ```bash
 # create & activate a venv (Windows examples below)
 python -m venv .venv
 # Bash (Git Bash / WSL):
 source .venv/Scripts/activate
 # PowerShell:
 # .\.venv\Scripts\Activate.ps1
 # CMD:
 # .\.venv\Scripts\activate.bat

 pip install --upgrade pip
 pip install pandas numpy matplotlib seaborn jupyter nbformat
 ```

 2. Open `Part_I_exploration_template.ipynb` in Jupyter/VS Code and run cells top-to-bottom to reproduce the analysis and regenerate `data/gobike_cleaned.csv`.

 3. Presentation (Part II slide deck)

 - The slide-deck notebook is `Part_II_slide_deck_template.ipynb`. It includes three polished visualizations and an auto-fill code cell that computes summary numbers and writes them into the Key findings slide.

 - To populate the Key findings slide (programmatically):
     1. Open `Part_II_slide_deck_template.ipynb` in Jupyter or VS Code.
     2. Run the code cell titled "Auto-fill key findings and update the markdown slide in-place" (located immediately after the blank Key findings markdown slide). This cell computes `peak_hour`, `subs_pct`, median durations, `top3` stations and writes a headered markdown block into the Key findings slide. The code cell is marked to be skipped in slides and prints only a short success message, so it won't show the computed text in the slides output.

 - To export the slides and ensure outputs are up-to-date, execute the notebook during conversion. From your shell (bash on Windows), run:

 ```bash
 jupyter nbconvert Part_II_slide_deck_template.ipynb \
     --to slides \
     --execute \
     --ExecutePreprocessor.timeout=600 \
     --post serve \
     --no-input --no-prompt
 ```

 This will execute the notebook (so the visualizations and the auto-filled Key findings are run/updated) and then serve the slides in a browser.

 ## Notes / limitations

 - This repository contains a focused exploratory analysis; further cleaning (e.g., rigorous birth-year validation, station name normalization, and handling of duplicated trips) will strengthen modeling or causal claims.
 - Outliers in `duration_sec` must be inspected before relying on mean-based inferences.

 ---

