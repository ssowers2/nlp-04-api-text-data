# API Text Data Pipeline (EVTL with Python)

[![Python 3.14+](https://img.shields.io/badge/python-3.14%2B-blue?logo=python)](#)
[![MIT](https://img.shields.io/badge/license-see%20LICENSE-yellow.svg)](./LICENSE)

> Professional Python project for Web Mining and Applied NLP.

Web Mining and Applied NLP focus on retrieving, processing, and analyzing text from the web and other digital sources.
This course builds those capabilities through working projects.

In the age of generative AI, durable skills are grounded in real work:
setting up a professional environment,
reading and running code,
understanding the logic,
and pushing work to a shared repository.
Each project follows a similar structure based on professional Python projects.
These projects are **hands-on textbooks** for learning Web Mining and Applied NLP.

## This Project

This project focuses on retrieving and processing structured text data
**from web APIs in JSON format**.

The goal is to acquire JSON data from an external source,
inspect and validate its structure,
transform it into a usable format,
and load it into a reproducible output.

You've likely heard of ETL or ELT.
We recommend EVTL.

In EVTL, each stage has a source, a process, and a sink.

- **Extract** acquires data
- **Validate** inspects and checks it
- **Transform** reshapes it
- **Load** sends it to the chosen destination

This project illustrates how to **work with real API data and understand its structure before analysis**.

## Key Files

You'll work with these files as you update authorship and experiment:

- **src/nlp/pipeline_api_json.py** - MAIN PIPELINE SCRIPT (no changes needed)
- **src/nlp/config_case.py** - Python configuration (<mark>**copy and edit**</mark> for your custom project)
- **src/nlp/stage01_extract.py** - EXTRACT (no changes needed)
- **src/nlp/stage02_validate_case.py** - VALIDATE (<mark>**copy and edit**</mark>)
- **src/nlp/stage03_transform_case.py** - TRANSFORM (<mark>**copy and edit**</mark>)
- **src/nlp/stage04_load.py** - LOAD (no changes needed)
- **pyproject.toml** - <mark>**update**</mark> authorship, links, and dependencies
- **zensical.toml** - <mark>**update**</mark> authorship and links

## First: Follow These Instructions

Follow the [step-by-step workflow guide](https://denisecase.github.io/pro-analytics-02/workflow-b-apply-example-project/) to complete:

1. Phase 1. **Start & Run**
2. Phase 2. **Change Authorship**
3. Phase 3. **Read & Understand**

## Success

After running the script successfully, you will see:


```shell
========================
Pipeline executed successfully!
========================
```

And new files will appear:

- project.log - confirming successful run
- data/raw/case_raw.json - dump of the fetched JSON
- data/processed/case_processed.csv - final loaded result

## Command Reference

The commands below are used in the workflow guide above.
They are provided here for convenience.

Follow the guide for the **full instructions**.

<details>
<summary>Show command reference</summary>

### In a machine terminal (open in your `Repos` folder)

After you get a copy of this repo in your own GitHub account,
open a machine terminal in your `Repos` folder:

```shell
# Replace username with YOUR GitHub username.
git clone https://github.com/ssowers2/nlp-04-api-text-data
cd nlp-04-api-text-data
code .
```

### In a VS Code terminal

```shell
uv self update
uv python pin 3.14
uv sync --extra dev --extra docs --upgrade

uvx pre-commit install
git add -A
uvx pre-commit run --all-files

# repeat if changes were made
git add -A
uvx pre-commit run --all-files

# Later, we install spacy data model and
# en_core_web_sm = english, core, web, small
# It's big: spacy+data ~200+ MB w/ model installed
#           ~350–450 MB for .venv is normal for NLP
# uv run python -m spacy download en_core_web_sm

# First, run the module
# IMPORTANT: Close each figure after viewing so execution continues
uv run python -m nlp.pipeline_api_json

uv run ruff format .
uv run ruff check . --fix
uv run zensical build

git add -A
git commit -m "update"
git push -u origin main
```

</details>

## Notes

- Use the **UP ARROW** and **DOWN ARROW** in the terminal to scroll through past commands.
- Use `CTRL+f` to find (and replace) text within a file.

## Example Artifact (Output)



```text
START PIPELINE
ROOT_PATH = .
DATA_PATH = data
RAW_PATH = data\raw
PROCESSED_PATH = data\processed
========================
STAGE 01: EXTRACT starting...
========================
SOURCE PATH = https://jsonplaceholder.typicode.com/posts
SINK PATH = data\raw\case_raw.json
========================
STAGE 02: VALIDATE starting...
========================
JSON STRUCTURE INSPECTION:
Top-level type: list
Keys in first record: ['userId', 'id', 'title', 'body']
Field types:
userId: int
id: int
title: str
body: str
Validation passed.
Sink: validated JSON object
========================
STAGE 03: TRANSFORM starting...
========================
Transformation complete.
DataFrame preview:
shape: (5, 6)
...preview of dataframe...
Sink: Polars DataFrame created
========================
STAGE 04: LOAD starting...
========================
SINK PATH = data\processed\case_processed.csv
========================
Pipeline executed successfully!
========================
```


## Enhancements

In production systems, validation is often automated using tools
such as Great Expectations or Soda.

In this module, validation is implemented manually to develop a
clear understanding of structure, assumptions, and data quality.

## Phase 4: Technical Modification

For Phase 4, I made a small technical modification in the Transform stage of the pipeline.

I added a new derived column called `total_text_length`, which combines the character length of the `title` and `body` fields. This was done using Polars by calculating the length of each field and combining them into a new column.

### Code Example

```python
df = df.with_columns(
    [
        pl.col("title").str.len_chars().alias("title_length"),
        pl.col("body").str.len_chars().alias("body_length"),
        (pl.col("title").str.len_chars() + pl.col("body").str.len_chars()).alias("total_text_length")
    ]
)

### Challenge Encountered

One challenge I encountered was correctly creating a derived column using Polars. At first, the new column did not calculate correctly because I was not combining the fields properly. I resolved this by adjusting the expression and verifying the output in the DataFrame preview.

## Phase 5: Apply the Pipeline to a New API

For Phase 5, I reused the same EVTL pipeline and applied it to a new API endpoint:

https://jsonplaceholder.typicode.com/comments

### JSON Structure

The JSON data is structured as a list of dictionaries. Each dictionary represents a single comment and contains fields such as `postId`, `id`, `name`, `email`, and `body`. The structure is flat and not nested, which makes it straightforward to inspect and transform.

### Selected Fields

I selected the following fields for analysis:

- `postId` – connects each comment to a specific post
- `id` – unique identifier for each comment
- `name` – a short label or title for the comment
- `email` – identifies the user who submitted the comment
- `body` – contains the main text of the comment

These fields are useful for organizing, identifying, and analyzing the data.

### Transform Changes

In the Transform stage, I updated the pipeline to match the new JSON structure. I replaced the original fields (`userId`, `title`) with the new fields (`postId`, `name`, `email`, and `body`).

I also created new derived columns:

- `name_length` – character length of the name field
- `body_length` – character length of the body field
- `total_text_length` – combined length of name and body
- `valid_email` – checks if the email contains "@"

These changes allowed the pipeline to adapt to a different dataset while still producing a clean and structured output.

### Output Files

To avoid overwriting the original dataset, I created new output files:

- `comments_raw.json`
- `comments_processed.csv`

This keeps the datasets organized and makes it easier to compare results across different API sources.

### Challenge Encountered

One challenge I encountered was a `KeyError` after switching APIs. This occurred because the Transform stage was still using field names from the original dataset. I resolved this by updating the code to use the correct fields.
