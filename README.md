# Prompt Engineering for Small Language Models: Evaluating In-Context Learning for Portuguese Sentiment Analysis

This repository contains the code, data, and notebooks used in a research study evaluating how different prompt engineering strategies affect the performance of small language models (SLMs) on Portuguese sentiment analysis tasks.

The study systematically compares six prompting templates — ranging from zero-shot to few-shot with meta-prompts and role-play framing — across four Brazilian Portuguese datasets and four models, with statistical significance testing via paired bootstrap.

---

## Repository Structure

```
.
├── Notebooks/
│   ├── 01 - datasets_preprocessing.ipynb       # Dataset download and preprocessing
│   ├── 02 - demonstration_selection.ipynb       # Few-shot demonstration selection via K-means
│   ├── 03 - evaluation_deepseek-v3.2.ipynb      # Evaluation: DeepSeek-v3.2 (strong baseline)
│   ├── 04 - evaluation_gemma2-9b.ipynb          # Evaluation: Gemma 2 9B-it
│   ├── 05 - evaluation_qwen3.5-9b.ipynb         # Evaluation: Qwen 3.5-9B
│   ├── 06 - evaluation_boto-9b-it.ipynb         # Evaluation: Boto-9B-it
│   ├── 07 - evaluation_multilingual-sentiment-analysis.ipynb  # Evaluation: weak baseline
│   └── 08 - Paired bootstrap majority vote.ipynb # Statistical analysis and results
├── Data/
│   ├── preprocessed/      # Stratified train/test splits (CSV)
│   ├── prompts_templates/ # Six Jinja2 prompt templates
│   ├── raw/               # Original dataset files
│   ├── results/           # Model prediction CSVs (one per model/dataset/template/round)
│   └── staging_area/      # Inference checkpoints
├── .env.example           # Environment variable template
├── pyproject.toml
└── README.md
```

---

## Setup

**Prerequisites**: Python 3.13, [uv](https://docs.astral.sh/uv/)

```bash
# Clone and install dependencies
git clone <repository-url>
cd slm_prompt_analysis
uv sync
```

**Configure environment variables** — copy `.env.example` to `.env` and fill in your paths:

```bash
cp .env.example .env
```

```dotenv
AP_DATA_RAW=Data/raw
AP_DATA_PREPROCESSED=Data/preprocessed
AP_DATA_RESULTS=Data/results
AP_PROMPTS_TEMPLATE=Data/prompts_templates
AP_STAGIN-AREA_PATH=Data/staging_area
HF_TOKEN=your_huggingface_token_here        # required for HF-hosted models
DEEPSEEK_API_KEY=sk-your-api-key-here       # required for notebook 03 only
```

---

## Notebooks

| # | Notebook | Description |
|---|----------|-------------|
| 01 | `datasets_preprocessing` | Downloads IMDB-PT, OpCovidBR, ReLI, TweetSentBR from HuggingFace/GitHub; standardizes columns to `text`/`label`; performs 80/20 stratified splits. |
| 02 | `demonstration_selection` | Generates text embeddings via Jina-embeddings-v3; applies K-means clustering; samples one representative example per cluster per label for balanced few-shot demonstrations. |
| 03 | `evaluation_deepseek-v3.2` | Async inference via DeepSeek API for all dataset/template/round combinations. Serves as the strong baseline. |
| 04 | `evaluation_gemma2-9b` | Inference via HuggingFace `pipeline` for Gemma 2 9B-it. Requires GPU. |
| 05 | `evaluation_qwen3.5-9b` | Inference for Qwen 3.5-9B. Requires GPU. |
| 06 | `evaluation_boto-9b-it` | Inference for Boto-9B-it (Brazilian Portuguese-tuned model). Requires GPU. |
| 07 | `evaluation_multilingual-sentiment-analysis` | Inference for a multilingual zero-shot classifier. Serves as the weak baseline. |
| 08 | `Paired bootstrap majority vote` | Consolidates 3-round predictions via majority vote; runs bilateral paired bootstrap tests to compare prompting strategies; outputs `consolidation_stats.csv` and `bootstrap_results.csv`. |

**Execution order**: 01 → 02 → 03–07 (independent) → 08

---

## Datasets

| Dataset | Source | Task | Labels |
|---------|--------|------|--------|
| [IMDB-PT](https://huggingface.co/datasets/maritaca-ai/imdb_pt) | HuggingFace | Binary sentiment | -1 (Negative), 1 (Positive) |
| [OpCovidBR](https://github.com/t-davidson/hate-speech-and-offensive-language) | GitHub | Binary sentiment | -1 (Negative), 1 (Positive) |
| [ReLI](https://huggingface.co/datasets/ruanchaves/reli) | HuggingFace | Multiclass sentiment | -1, 0 (Neutral), 1 |
| [TweetSentBR](https://huggingface.co/datasets/ruanchaves/tweetsentbr) | HuggingFace | Multiclass sentiment | -1, 0 (Neutral), 1 |

**Label encoding**: `-1` = Negative, `0` = Neutral, `1` = Positive

---

## Prompt Templates

Six Jinja2 templates in `Data/prompts_templates/`, referenced in the paper by the following abbreviations:

| Abbreviation | File | Strategy |
|---|---|---|
| ZSP-MTSI | `template-task-specific-instructions-zero-shot.jinja` | Zero-shot with task-specific instructions |
| FSP-MTSI | `template-task-specific-instructions-few-shot.jinja` | Few-shot with task-specific instructions + demonstrations |
| FSP-NI | `template-task-agnostic-no-instructions-few-shot-en.jinja` | Few-shot with demonstrations only (no instructions) |
| FSP-MRI | `template-task-specific-roleplay-few-shot.jinja` | Few-shot with role-play framing |
| FSP-AMI | `template-task-specific-meta-prompt-few-shot.jinja` | Few-shot with automated meta-prompt instructions |
| FSP-MTSISOF | `template-task-specific-instructions-few-shot-json.jinja` | Few-shot with structured JSON output format |

---

## Citation

```bibtex
@article{anonymous2026prompt,
  title   = {Prompt Engineering for Small Language Models: Evaluating
             In-Context Learning for Portuguese Sentiment Analysis},
  year    = {2026},
  note    = {Under review}
}
```
