---
license: cc-by-4.0
task_categories:
- text-classification
- text-generation
language:
- en
- zh
pretty_name: LLMscore-ICLR-OpenReview
size_categories:
- 100K<n<1M
tags:
- peer-review
- scientific-text
- llm-scoring
- meta-evaluation
- openreview
---

# LLMscore-ICLR-OpenReview

## Dataset Description

`LLMscore-ICLR-OpenReview` is the released original dataset for the paper *Position: Peer Review Should Be Calibrated via LLM Scoring* by Zijin Chen, Lesui Yu, Xiaofei Liao, Hai Jin, and Qinbin Li, accepted to the ICML 2026 Position Track.

The dataset is constructed for peer review analysis. It contains OpenReview-linked paper metadata, review records, anonymized rationale items, extracted paper text, LLM-derived anchor scores, and residual-based bias measurements for ICLR 2023, 2024, and 2025.

The release does not redistribute paper PDFs. Paper-level records retain `openreview_url` and `pdf_url` fields so users can refer to the original public OpenReview source. Extracted paper text is distributed separately as UTF-8 `.txt` files under `texts/ICLR_2023`, `texts/ICLR_2024`, and `texts/ICLR_2025`; the JSONL index files store paths and conversion metadata rather than embedding full paper text.

## What The Dataset Captures

This dataset is intended to support analysis of:

- alignment between reviewer-written rationales and reviewer-assigned scores;
- paper-level and review-level residual patterns under LLM-based calibration;
- structured pro/con rationale extraction for peer review studies;
- linking review-level calibration variables to extracted paper text through stable `paper_id` keys;
- methodological reproduction and extension of the experiments in the associated paper.

The key analytical quantities are:

- `expected_score`: the anchor score inferred by ChatGPT-4o from the review rationale;
- `bias`: the residual between the reviewer-given score and the anchor score;
- `pros`, `cons`, and `anonymized_items`: structured rationale units used for downstream peer review analysis.

## Scoring Protocol

Across all yearly subsets (`ICLR_2023`, `ICLR_2024`, and `ICLR_2025`), anchor scoring is performed under a unified ChatGPT-4o-based scoring framework. This design preserves methodological consistency across years and supports cross-year comparison within a common calibration setting.

## Data Leakage Mitigation

Because the dataset is designed for LLM-based peer review analysis, the curation pipeline considered whether the scoring model might already recognize some papers. Candidate papers were screened with ChatGPT-4o for potential familiarity, and the downstream analysis focuses on papers judged unfamiliar to the model. This step reduces the risk that released anchor scores reflect prior memorization rather than the review text under analysis.

## Dataset Structure

The release stores each conference year in a separate directory to keep the yearly ICLR subsets unambiguous:

- `data/ICLR_2023/papers.jsonl.gz`: one row per ICLR 2023 paper;
- `data/ICLR_2023/reviews.jsonl.gz`: one row per ICLR 2023 review;
- `data/ICLR_2023/anonymized_items.jsonl.gz`: one row per ICLR 2023 anonymized pro/con rationale item;
- `data/ICLR_2023/paper_text_index.jsonl.gz`: one row per ICLR 2023 paper-text conversion record;
- `data/ICLR_2024/`: the same four files for ICLR 2024;
- `data/ICLR_2025/`: the same four files for ICLR 2025;
- `texts/ICLR_2023/{paper_id}.txt`: one extracted UTF-8 text file per converted ICLR 2023 paper PDF;
- `texts/ICLR_2024/{paper_id}.txt`: one extracted UTF-8 text file per converted ICLR 2024 paper PDF;
- `texts/ICLR_2025/{paper_id}.txt`: one extracted UTF-8 text file per converted ICLR 2025 paper PDF.

The rows are deterministically sorted:

- papers by `source_dataset`, `paper_id` within each yearly directory;
- reviews by `paper_id`, `review_id` within each yearly directory;
- anonymized items by `paper_id`, `mapped_reviewer_id`, `item_id` within each yearly directory.

## Basic Statistics

Total release contents:

- Papers: 22,177
- Reviews: 52,369
- Anonymized rationale items: 435,754
- Extracted paper text files: 22,675
- Covered subsets: `ICLR_2023`, `ICLR_2024`, `ICLR_2025`

Yearly subsets:

| Subset | Papers | Reviews | Anonymized rationale items | Text files | Text index rows |
| --- | ---: | ---: | ---: | ---: | ---: |
| `ICLR_2023` | 3,507 | 13,248 | 62,915 | 3,791 | 3,791 |
| `ICLR_2024` | 7,150 | 27,601 | 138,356 | 7,279 | 7,279 |
| `ICLR_2025` | 11,520 | 11,520 | 234,483 | 11,605 | 11,606 |

## Data Fields

### `papers.jsonl.gz`

- `paper_id`: OpenReview paper identifier.
- `title`: paper title.
- `abstract`: paper abstract.
- `year`: conference year.
- `source_dataset`: yearly subset label.
- `num_reviews`: number of linked reviews in the released set.
- `avg_actual_score`: mean reviewer-given score.
- `avg_expected_score`: mean anchor score inferred by ChatGPT-4o.
- `avg_bias`: mean residual between actual and expected scores.
- `std_actual_score`: standard deviation of reviewer-given scores.
- `std_expected_score`: standard deviation of anchor scores.
- `has_anchor_scores`: whether anchor scores are available.
- `openreview_url`: OpenReview forum URL.
- `pdf_url`: OpenReview PDF URL.

### `reviews.jsonl.gz`

- `paper_id`: linked paper identifier.
- `review_id`: review identifier.
- `reviewer_id`: anonymized reviewer identifier within paper.
- `review_text`: normalized review text.
- `summary`, `strengths`, `weaknesses`, `questions`: structured review fields when available.
- `actual_score`: reviewer-given score.
- `expected_score`: ChatGPT-4o-derived anchor score.
- `bias`: residual between actual and expected score.
- `pros`, `cons`: structured rationale lists with `text`, `category`, and `weight`.
- `confidence`, `soundness`, `presentation`, `contribution`: additional review metadata when available.
- `year`: conference year.

### `anonymized_items.jsonl.gz`

- `paper_id`: linked paper identifier.
- `mapped_reviewer_id`: anonymized reviewer identifier within paper.
- `item_id`: rationale item identifier.
- `item_type`: `pro` or `con`.
- `text`: rationale text.
- `category`: rationale category.
- `weight`: rationale weight used in anchor-score construction.
- `year`: conference year.

### `paper_text_index.jsonl.gz`

- `paper_id`: linked paper identifier.
- `text_path`: relative path to the extracted UTF-8 `.txt` file.
- `char_count`, `word_like_count`, and page-count fields: conversion metadata for the extracted text.
- conversion status and quality flags when available.

## Source Data

The release is derived from OpenReview-linked ICLR 2023, 2024, and 2025 review data curated for the LLMscore project. The dataset package contains normalized paper-level, review-level, rationale-level, and paper-text records together with metadata for analysis and reproducibility.

Paper text was extracted from the corresponding public OpenReview PDFs using `pdftotext` with a layout-preserving UTF-8 configuration. The original PDFs are not redistributed in this package.

## Anonymization, Redaction, And Privacy

- Reviewer identifiers are anonymized within each paper.
- No paper PDFs are redistributed.
- Extracted paper text is stored only as individual `.txt` files under `texts/ICLR_*`, not embedded in combined JSONL files.
- Key-like tokens matching common API-key patterns in extracted paper text are replaced with `[REDACTED_API_KEY]` or related redacted placeholders before release.
- The current redaction report records 54 replacements across 42 files; see `metadata/redaction_report.json`.

## Intended Uses

This dataset is intended for:

- peer review calibration research;
- analysis of rationale-score consistency;
- empirical study of LLM-assisted meta-review or auditing pipelines;
- linking paper text to review-level calibration variables through `paper_id`;
- reproduction of the experiments in the associated ICML 2026 Position Track paper.

## Out-Of-Scope Uses

This dataset is not intended to serve as:

- a general-purpose paper corpus detached from the peer review setting;
- a benchmark for full-paper language modeling;
- a substitute for the original OpenReview records and venue-specific context;
- a source of original PDF redistribution.

## Limitations

- The data are restricted to OpenReview-linked ICLR papers included in the released curation pipeline.
- Residual-based quantities such as `expected_score` and `bias` are analysis artifacts produced by an LLM-based scoring framework rather than venue-native annotations.
- Extracted paper text is generated from PDFs and may contain conversion artifacts, OCR/layout noise, missing content, or page-order issues.
- Although a familiarity screening step is used to mitigate data leakage, no automatic procedure can eliminate all possible prior exposure.
- Cross-year comparisons should be interpreted in the context of changes in venue composition, review style, and submission populations.

## Licensing And Attribution

This curated package is released under CC-BY-4.0. Original OpenReview content should be attributed to OpenReview and the corresponding ICLR venue. Users should also follow the original source terms and norms of responsible use.

## Acknowledgement

We thank Huazhong University of Science and Technology for supporting this work and the preparation of this dataset release.

## Citation

If you use this dataset, please cite the associated paper using the official ICML bibliographic entry for:

- *Position: Peer Review Should Be Calibrated via LLM Scoring*
- Zijin Chen, Lesui Yu, Xiaofei Liao, Hai Jin, and Qinbin Li

To avoid distributing a non-official or incorrectly formatted citation, this dataset card does not include a hand-written BibTeX entry. Please use the final citation record released with the official ICML proceedings.
