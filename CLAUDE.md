# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Research project analyzing how different prompt engineering strategies affect small language model (SLM) performance on Portuguese sentiment analysis. Evaluates six prompting templates (zero-shot, few-shot, roleplay, meta-prompt, JSON-formatted, task-agnostic) across four Brazilian Portuguese datasets. The full research article is available in the `STIL_2026___Prompt_Engineering_for_Small_Language_Models__Evaluating_ICL_for_Portuguese_Sentiment_Analysis.pdf` file.

## Environment

- Python 3.13 (managed via uv, see `pyproject.toml`)
- Primary development happens in Google Colab notebooks (`Notebooks/`)
- `main.py` is a placeholder; core logic lives in the notebooks

## Architecture

Three-phase pipeline, each implemented as a Colab notebook:

1. **Preprocessing** (`01 - datasets_preprocessing.ipynb`) — Downloads four datasets (IMDB-PT, OpCovidBR, ReLI, TweetSentBR) from Hugging Face/GitHub, standardizes columns to `text`/`label`, performs stratified 80-20 train-test splits, saves as CSV.

2. **Demonstration Selection** (`02 - demonstration_selection.ipynb`) — Generates embeddings via Jina-embeddings-v3, applies K-means clustering, samples one representative example per cluster per label to create balanced few-shot demo sets (~6 examples each).

3. **Evaluation** (`03 - evaluation_deepseek-v3.2.ipynb`) — Loads a model (e.g., Boto-9B-it), renders Jinja2 prompt templates with test inputs, runs inference for 3 rounds per template/dataset combo, saves results with checkpointing via a staging area.

## Data Conventions

- **Label encoding**: -1 (Negative), 0 (Neutral), 1 (Positive)
- **Binary datasets**: IMDB-PT, OpCovidBR (labels: -1, 1)
- **Multiclass datasets**: ReLI, TweetSentBR (labels: -1, 0, 1)
- **File naming**: `{dataset}_{split}.csv` for data, `{model}_{dataset}_{template}_{round}_result.csv` for results
- **Prompt templates**: Jinja2 files in `Data/prompts_templates/` using variables `examples`, `input_text`, `labels`

## Colab Secrets Required

Notebooks read paths/tokens from `google.colab.userdata`:

- `AP_DATA_RAW`, `AP_DATA_PREPROCESSED`, `AP_DATA_RESULTS`, `AP_PROMPTS_TEMPLATE`
- `AP_STAGIN-AREA_PATH` (note: intentional typo in secret name)
- `HF_TOKEN` (Hugging Face access token)
