# Prompt Engineering for Small Language Models: Evaluating ICL for Portuguese Sentiment Analysis

[![Paper](https://img.shields.io/badge/ACL%20Anthology-2026.stil--1.31-red)](https://aclanthology.org/2026.stil-1.31/)
[![DOI](https://img.shields.io/badge/DOI-10.5753%2Fstil.2026.25253-blue)](https://doi.org/10.5753/stil.2026.25253)

> **Paper:** André da F. Schuck, Gabriel L. Garcia, João Renato R. Manesco, Pedro Henrique Paiola, Leandro A. Passos, João Paulo Papa. *Prompt Engineering for Small Language Models: Evaluating ICL for Portuguese Sentiment Analysis.* In **Proceedings of the 17th Brazilian Symposium in Information and Human Language Technology (STIL 2026)**, pages 375–389, Cuiabá, Brazil. Association for Computational Linguistics.
>
> 📄 [ACL Anthology](https://aclanthology.org/2026.stil-1.31/) · 🔗 [DOI: 10.5753/stil.2026.25253](https://doi.org/10.5753/stil.2026.25253) · 📑 [How to cite](#citation)

---

## Abstract

The In-Context Learning (ICL) paradigm enables adapting LLMs without parameter tuning. This work evaluates the impact of prompt engineering on 9B models for Brazilian Portuguese sentiment classification, comparing six prompting formats (few- and zero-shot) across three linguistically diverse models (Gemma2-9B-it, Boto-9B-IT, Qwen3.5-9B) and four public datasets, anchored by a fine-tuned encoder (MSA-DistilBERT, ~0.1B) and a 685B MoE model (DeepSeek-V3.2) under a unified protocol. Results show that all 9B models outperform the encoder and recover 71–101% of the weak-to-strong gap, with Qwen3.5-9B achieving the highest mean coverage (94.5%). Statistical tests indicate that well-defined instructions are the main driver of ICL performance, capturing most of the achievable accuracy even in zero-shot settings, while elaborate formats (roleplay, JSON schema, meta-prompting) add no consistent gain over a minimal instruction-plus-demonstrations prompt, offering practical guidance for deploying SLMs in resource-constrained PT-BR NLP scenarios.

---

## Research Questions

- **RQ1:** Where do 9B models under ICL sit between the ~0.1B encoder floor and the 685B ceiling, and does prompt sensitivity persist across scales?

- **RQ2:** Do instructions and demonstrations contribute distinctly to ICL performance for PT-BR SA, and does combining them outperform either in isolation?

- **RQ3:** Do more elaborate combined formats yield statistically significant gains over the minimal one, or do they exhibit practical equivalence?

---

## Key Contributions

1. **A controlled PT-BR SA design** that fixes scale at 9B and varies linguistic profile, including a base→fine-tuned pair (Gemma2/Boto) that isolates PT-BR adaptation at constant architecture and initialization. The 685B baseline is run under the same six prompts, demonstrations, and orderings, so that prompt *sensitivity*, not only accuracy, becomes comparable across scales.

2. **Evidence that 9B SLMs recover 71–101% of the weak-to-strong gap**, with a natively multilingual model (Qwen3.5-9B) matching the 685B model's robustness to prompt variation.

3. **Evidence that well-defined instructions are the main driver of ICL performance**, capturing most of the achievable accuracy even zero-shot, while demonstrations alone may degrade it.

---

## Key Findings

### RQ1: Positioning 9B SLMs between a ~0.1B encoder and a 685B model

Under their best prompt, all three 9B models exceed MSA-DistilBERT on every dataset by +0.12 to +0.20 BAcc, covering **71–101%** of the weak-to-strong gap (9 of 12 model–dataset cells above 85%). Qwen3.5-9B attains the highest mean coverage (**94.5%**) and is the only 9B model to reach the 685B ceiling on any dataset (ReLI, 101%).

Prompt sensitivity does not vanish at 685B: DeepSeek's best and worst prompts still differ by 8.5 pp BAcc on average. Qwen3.5 matches DeepSeek on both sensitivity dimensions (mean format amplitude Δ 0.090 vs. 0.085; ordering CV 0.022 vs. 0.019), whereas Boto and Gemma2 (same architecture and initialization) differ by 1.8× in format sensitivity and 3.2× in ordering sensitivity. Sensitivity is thus tied less to scale than to the model itself.

Gap coverage = (BAcc_SLM − BAcc_MSA) / (BAcc_DeepSeek − BAcc_MSA) × 100, computed per dataset under each model's best prompt.

> **Note on the floor.** MSA-DistilBERT is used off-the-shelf (fine-tuned for SA, not for the target domains), and its five-class output is consolidated into polarity classes. The floor should therefore be read as conservative, and gap-coverage percentages as correspondingly optimistic. OPCovid-BR has a small test set (N=123), where a single instance shifts gap coverage by roughly 5 pp.

![RQ1: % of Weak-to-Strong Gap Covered](Article/Images/rq1_relative.png)

_Coverage (%) of the weak-to-strong gap per model and dataset. DeepSeek-V3.2 (685B) is the strong ceiling; MSA-DistilBERT (~0.1B, fine-tuned for SA) is the weak floor._

---

### RQ2: Instructions vs. Demonstrations

Instructions alone (zero-shot *Inst-only*) achieve significantly higher BAcc than demonstrations alone (*Demos-only*) in **7 of 12** model–dataset comparisons (paired bootstrap, p < 0.05), with a mean ΔBAcc of **+0.122**. Adding demonstrations to instructions yields only a marginal gain (**+0.017** mean ΔBAcc for *Inst+Demos* vs. *Inst-only*).

Instructions also stabilize predictions against ordering perturbations: the mean BAcc range across the three demonstration orderings drops from **9.6 pp** (*Demos-only*) to **2.5 pp** (*Inst+Demos*), a **3.8×** narrowing that holds for every model, DeepSeek-V3.2 included (4.4×).

**Exception:** on ReLI (72.7% Neutral), *Demos-only* numerically outperforms *Inst-only* for all three models (significantly for Gemma2 and Qwen3.5).

![Legend](Article/Images/rq2_legend.png)
![RQ2: Decomposing instruction and demonstration contributions (forest plot)](Article/Images/rq2_forest_plot.png)

_Forest plot of paired bootstrap effect sizes (ΔBAcc) for Inst-only vs. Demos-only (top), Inst+Demos vs. Demos-only (middle), and Inst+Demos vs. Inst-only (bottom). Dark markers indicate p < 0.05; light markers p ≥ 0.05. The shaded band marks practical equivalence (|Δ| < 0.02)._

![RQ2: Ordering Sensitivity Across Formats](Article/Images/rq2_ordering_sensitivity_6fmt.png)

_Balanced accuracy across three demonstration orderings for each format, model, and dataset. Demos-only exhibits wide variance; instruction-anchored formats collapse to narrow variance._

---

### RQ3: Does prompt elaboration pay off?

No elaborated format consistently outperforms the simplest combined baseline (*Inst+Demos*). Six of the nine model × format mean differences fall inside the ±0.02 practical-equivalence band, and the significant comparisons that do occur alternate in sign. Mean ΔBAcc over *Inst+Demos* across all models:

| Format       | Mean ΔBAcc | Significant (↑ / ↓) |
| ------------ | ---------- | ------------------- |
| Meta-prompt  | −0.001     | 2 ↑ / 3 ↓           |
| Roleplay     | +0.009     | 4 ↑ / 1 ↓           |
| JSON-Schema  | +0.013     | 3 ↑ / 2 ↓           |

The only model that benefits from elaboration is **Boto**, the model with the weakest baseline format compliance (JSON-Schema +0.045, Roleplay +0.031, Meta-prompt +0.013). Elaboration is thus not a reliable route to accuracy once a task instruction is present.

![Legend](Article/Images/rq2_legend.png)
![RQ3: Elaborated Formats vs. Inst+Demos (forest plot)](Article/Images/rq2_forest_equivalence_plot.png)

_Forest plot comparing Meta-prompt, Roleplay, and JSON-Schema against Inst+Demos. The shaded band marks practical equivalence (|Δ| < 0.02)._

---

## Experimental Scale

| Dimension                   | Value                                                                    |
| --------------------------- | ------------------------------------------------------------------------ |
| Total individual inferences | **337,479**                                                              |
| SLMs evaluated              | 3 (Gemma2-9B-it, Boto-9B-IT, Qwen3.5-9B)                                 |
| Baselines                   | 2 (MSA-DistilBERT ~0.1B, weak; DeepSeek-V3.2 685B MoE, strong)           |
| PT-BR datasets              | 4 (IMDB-PT, OPCovid-BR, ReLI, TweetSentBr)                               |
| Tasks                       | Binary (Positive/Negative) and multiclass (Positive/Negative/Neutral)    |
| Prompt templates            | 6 (zero-shot to elaborated few-shot)                                     |
| Inference rounds per config | 3 (distinct demonstration orderings)                                     |
| Primary metric              | Balanced Accuracy (BAcc); unparseable outputs counted as errors (FNC)    |
| Statistical test            | Paired bootstrap (B = 10,000, α = 0.05) + ±0.02 BAcc equivalence band    |

---

## Models

| Role      | Model                                                                                                        | Parameters | Linguistic profile        |
| --------- | ------------------------------------------------------------------------------------------------------------ | ---------- | ------------------------- |
| SLM       | [google/gemma-2-9b-it](https://huggingface.co/google/gemma-2-9b-it)                                          | 9B         | English-dominant          |
| SLM       | [lucianosb/boto-9B-it](https://huggingface.co/lucianosb/boto-9B-it)                                          | 9B         | PT-BR fine-tuned (LoRA on Gemma2-9B-it) |
| SLM       | [Qwen/Qwen3.5-9B](https://huggingface.co/Qwen/Qwen3.5-9B)                                                    | 9B         | Multilingual (201 langs.) |
| Weak BL   | [tabularisai/multilingual-sentiment-analysis](https://huggingface.co/tabularisai/multilingual-sentiment-analysis) | ~0.1B  | Multilingual encoder fine-tuned for SA, used off-the-shelf |
| Strong BL | [deepseek-ai/DeepSeek-V3.2](https://huggingface.co/deepseek-ai/DeepSeek-V3.2) (non-thinking mode, official API) | 685B (MoE) | Multilingual          |

---

## Repository Structure

```
.
├── Article/
│   └── Images/                # Figures used in the paper
├── Notebooks/
│   ├── 01 - datasets_preprocessing.ipynb       # Dataset download and preprocessing
│   ├── 02 - demonstration_selection.ipynb       # Few-shot demonstration selection via K-means
│   ├── 03 - evaluation_deepseek-v3.2.ipynb      # Evaluation: DeepSeek-V3.2 (strong baseline)
│   ├── 04 - evaluation_gemma2-9b.ipynb          # Evaluation: Gemma 2 9B-it
│   ├── 05 - evaluation_qwen3.5-9b.ipynb         # Evaluation: Qwen 3.5-9B
│   ├── 06 - evaluation_boto-9b-it.ipynb         # Evaluation: Boto-9B-it
│   ├── 07 - evaluation_multilingual-sentiment-analysis.ipynb  # Evaluation: weak baseline
│   └── 08 - Paired bootstrap majority vote.ipynb # Statistical analysis and results
├── Data/
│   ├── preprocessed/      # Train/test splits and selected demonstrations (CSV)
│   ├── prompts_templates/ # Six Jinja2 prompt templates
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
git clone https://github.com/AndreSchuck/slm_prompt_analysis.git
cd slm_prompt_analysis
uv sync
```

**Configure environment variables**: copy `.env.example` to `.env` and fill in your paths:

```bash
cp .env.example .env
```

```dotenv
AP_DATA_RAW=Data/raw                        # required by notebook 01
AP_DATA_PREPROCESSED=Data/preprocessed
AP_DATA_RESULTS=Data/results
AP_PROMPTS_TEMPLATE=Data/prompts_templates
AP_STAGIN-AREA_PATH=Data/staging_area
HF_TOKEN=your_huggingface_token_here        # required for HF-hosted models
DEEPSEEK_API_KEY=sk-your-api-key-here       # required for notebook 03 only
```

---

## Notebooks

| #   | Notebook                                     | Description                                                                                                                                                                                                 |
| --- | -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 01  | `datasets_preprocessing`                     | Loads IMDB-PT, OPCovid-BR, ReLI, and TweetSentBr; standardizes columns to `text`/`label`; builds train/test splits and subsamples test sets to at most 1,500 instances (see [Datasets](#datasets)).         |
| 02  | `demonstration_selection`                    | Encodes training texts with jina-embeddings-v3; per label, runs K-means and samples one example per cluster, yielding 6 balanced demonstrations (3 per class for binary, 2 per class for multiclass).        |
| 03  | `evaluation_deepseek-v3.2`                   | Async inference via the DeepSeek API (non-thinking mode, temperature=0) for all dataset/template/round combinations. Serves as the strong baseline.                                                          |
| 04  | `evaluation_gemma2-9b`                       | Inference via Hugging Face `pipeline` for Gemma 2 9B-it. Requires GPU.                                                                                                                                      |
| 05  | `evaluation_qwen3.5-9b`                      | Inference for Qwen3.5-9B. Requires GPU.                                                                                                                                                                     |
| 06  | `evaluation_boto-9b-it`                      | Inference for Boto-9B-it (PT-BR fine-tuned variant of Gemma2-9B-it). Requires GPU.                                                                                                                          |
| 07  | `evaluation_multilingual-sentiment-analysis` | Inference with MSA-DistilBERT, a multilingual encoder fine-tuned for SA, used off-the-shelf without prompting or dataset-specific adaptation. Five-class output consolidated into polarity classes. Weak baseline. |
| 08  | `Paired bootstrap majority vote`             | Consolidates 3-round predictions via majority vote; runs two-sided paired bootstrap tests to compare prompting strategies; outputs `consolidation_stats.csv` and `bootstrap_results.csv`.                    |

**Execution order**: 01 → 02 → 03–07 (independent) → 08

---

## Datasets

| Dataset                                                                     | Task                 | Test size | Labels                      | Reference              |
| --------------------------------------------------------------------------- | -------------------- | --------- | --------------------------- | ---------------------- |
| [IMDB-PT](https://huggingface.co/datasets/maritaca-ai/imdb_pt)              | Binary sentiment     | 1,500     | -1 (Negative), 1 (Positive) | Maas et al. (2011)     |
| [OPCovid-BR](https://github.com/franciellevargas/OPCovidBR)                 | Binary sentiment     | 123       | -1 (Negative), 1 (Positive) | Vargas et al. (2020)   |
| [ReLI](https://www.linguateca.pt/Repositorio/ReLi/)                         | Multiclass sentiment | 1,500     | -1, 0 (Neutral), 1          | Freitas et al. (2014)  |
| [TweetSentBr](https://huggingface.co/datasets/eduagarcia/tweetsentbr_fewshot) | Multiclass sentiment | 1,500   | -1, 0 (Neutral), 1          | Brum and Nunes (2018)  |

**Label encoding**: `-1` = Negative, `0` = Neutral, `1` = Positive

**Splits** (as implemented in notebook 01, seed 42):
- **IMDB-PT**: original train split; original test split randomly subsampled to 1,500 instances.
- **OPCovid-BR**: polarity labels {-1, 1} only; 80/20 stratified train/test split.
- **ReLI**: sentence-level polarity read with the [ReLi corpus reader](https://github.com/pedrobalage/ReLi_Experiments); 7,500 sentences sampled, then 80/20 stratified train/test split.
- **TweetSentBr**: original train split; test split subsampled to 500 instances per class (1,500 total).

---

## Prompt Templates

Six Jinja2 templates in `Data/prompts_templates/`, referenced in the paper by the following names. All few-shot formats use 6 balanced demonstrations in a `Q:{text}` / `A:{label}` format.

| Name        | File                                                       | Components                                              |
| ----------- | ---------------------------------------------------------- | ------------------------------------------------------- |
| Inst-only   | `template-task-specific-instructions-zero-shot.jinja`      | Task instruction only (zero-shot)                       |
| Demos-only  | `template-task-agnostic-no-instructions-few-shot-en.jinja` | Demonstrations only (no instruction)                    |
| Inst+Demos  | `template-task-specific-instructions-few-shot.jinja`       | Task instruction + demonstrations                       |
| JSON-Schema | `template-task-specific-instructions-few-shot-json.jinja`  | Task instruction + JSON schema + demonstrations         |
| Roleplay    | `template-task-specific-roleplay-few-shot.jinja`           | Role specification + task instruction + demonstrations  |
| Meta-prompt | `template-task-specific-meta-prompt-few-shot.jinja`        | Meta-prompt optimized instruction + demonstrations (instruction generated by Claude 3.5 Sonnet, two refinement iterations) |

---

## Implementation Details

| Aspect                    | Detail                                                                                              |
| ------------------------- | --------------------------------------------------------------------------------------------------- |
| Hardware                  | NVIDIA A100 (40 GB) via Google Colab (SLMs); official API (DeepSeek-V3.2)                           |
| Precision                 | BFloat16                                                                                            |
| Decoding                  | SLMs: greedy (`do_sample=False`), `max_new_tokens=10` (20 for JSON-Schema); DeepSeek: `temperature=0` |
| Reasoning                 | All models in standard (non-reasoning) mode                                                         |
| Demonstration selection   | jina-embeddings-v3 + K-means per label, `n_clusters = ceil(6 / n_classes)` (3 for binary, 2 for multiclass), one example per cluster |
| Demonstrations per prompt | 6 (3 per class for binary; 2 per class for multiclass)                                              |
| Prediction consolidation  | Majority vote across 3 orderings; ties → Format Non-Compliance (code 2)                             |
| Output parsing            | Format-specific regex; 3-layer cascaded strategy for non-JSON formats; unmatched outputs → FNC (code 2) |
| Random seeds              | Subsampling/selection: s=42; orderings: s ∈ {42, 2, 3}                                              |

---

## Citation

If you use this code or results, please cite:

```bibtex
@inproceedings{schuck-etal-2026-prompt,
    title = "Prompt Engineering for Small Language Models: Evaluating {ICL} for {P}ortuguese Sentiment Analysis",
    author = "Schuck, Andr{\'e} da F.  and
      Garcia, Gabriel L.  and
      Manesco, Jo{\~a}o Renato R.  and
      Paiola, Pedro Henrique  and
      Passos, Leandro A.  and
      Papa, Jo{\~a}o Paulo",
    editor = "Barbosa, Bryan Khelven da Silva  and
      Paes, Aline  and
      Felippo, Ariani Di",
    booktitle = "Proceedings of the 17th {B}razilian Symposium in Information and Human Language Technology",
    month = oct,
    year = "2026",
    address = "Cuiab{\'a}, Mato Grosso, Brazil",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2026.stil-1.31/",
    doi = "10.5753/stil.2026.25253",
    pages = "375--389"
}
```

### Related work by the authors

This study builds on our earlier large-scale comparison of 23 LLMs for PT-BR sentiment analysis:

```bibtex
@article{schuck-etal-2025-evaluating,
    title = "Evaluating Large Language Models for {B}razilian {P}ortuguese Sentiment Analysis: A Comparative Study of Multilingual State-of-the-Art vs. {B}razilian {P}ortuguese Fine-Tuned {LLM}s",
    author = "Schuck, Andr{\'e} da Fonseca  and
      Garcia, Gabriel Lino  and
      Manesco, Jo{\~a}o Renato Ribeiro  and
      Paiola, Pedro Henrique  and
      Papa, Jo{\~a}o Paulo",
    journal = "Journal of the Brazilian Computer Society",
    volume = "31",
    number = "1",
    pages = "885--917",
    year = "2025",
    doi = "10.5753/jbcs.2025.5793"
}
```
