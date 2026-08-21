# Bootcamp Repository

## Folder Structure
- **homework/** → All homework contributions will be submitted here.
- **project/** → All project contributions will be submitted here.
- **class_materials/** → Local storage for class materials. Never pushed to GitHub.

## Homework Folder Rules
- Each homework will be in its own subfolder (`homework0`, `homework1`, etc.)
- Include all required files for grading.

## Class Materials Rules
- Each stage's handouts go in their own subfolder, named exactly as the course folder, e.g. `class_materials/stage01_problem-framing-and-scoping/`.
- Run lecture notebooks in place from that folder.
- Copy a homework starter into `homework/homeworkN/` before working on it.

## Project Folder Rules
- Keep project files organized and clearly named.
- The project folder structure is set up in Stage 02.# bootcamp_Jiayi_Chen

## Data Storage

**Folder structure**

- `data/raw/` — original, unmodified source data. Files are named with a timestamp (e.g. `sample_20260821-153000.csv`) so re-running the notebook never overwrites previous pulls, and each raw pull stays traceable.
- `data/processed/` — cleaned/derived data ready for analysis, saved as Parquet.

**Formats used and why**

- **CSV** for raw data: plain text, human-readable, easy to diff in git and open in Excel — good for data you want to inspect or share informally. 
- **Parquet** for processed data: a binary columnar format that preserves dtypes exactly, loads faster, and takes less disk space than CSV — better suited for data that will be reloaded repeatedly in later pipeline stages.

**How the code reads/writes using env variables**

- Paths come from `.env` (`DATA_DIR_RAW`, `DATA_DIR_PROCESSED`), loaded via `get_key()` in `src/config.py`.
