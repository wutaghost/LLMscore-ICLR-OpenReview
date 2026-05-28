# LLMscore-ICLR-OpenReview

[![GitHub](https://img.shields.io/badge/GitHub-wutaghost%2FLLMscore--ICLR--OpenReview-blue?logo=github)](https://github.com/wutaghost/LLMscore-ICLR-OpenReview)
[![Hugging Face](https://img.shields.io/badge/🤗%20Hugging%20Face-Wutaghost%2FLLMscore--ICLR--OpenReview-orange)](https://huggingface.co/datasets/Wutaghost/LLMscore-ICLR-OpenReview)

This dataset is the released original dataset for the paper *Position: Peer
Review Should Be Calibrated via LLM Scoring* by Zijin Chen, Lesui Yu, Xiaofei
Liao, Hai Jin, and Qinbin Li. The paper has been accepted to the ICML 2026
Position Track.

Its concrete purpose is **peer review analysis**: the dataset is meant for
studying how paper-review rationales, numeric ratings, LLM-derived anchor
scores, and review-score residuals interact in scientific peer review. It is
not a generic paper corpus, and it should be used with that specific
methodological context in mind.

The package organizes OpenReview-linked ICLR 2023, 2024, and 2025 data from the
LLMscore project. It contains paper metadata, normalized review records,
anonymized pro/con reason items, anchor scores, bias values, and extracted
paper text.

The package intentionally does not include paper PDFs. Use `openreview_url` and
`pdf_url` to access the original OpenReview pages and PDFs. Extracted paper text
is stored as one UTF-8 `.txt` file per paper under `texts/ICLR_*`.

## Release Scope

This open release is prepared to support both:

- archival artifact release alongside the associated paper; and
- public dataset distribution on platforms such as Hugging Face.

Accordingly, the package is organized as a self-contained release: it includes
compressed JSONL data, per-paper extracted text files, schema information,
dataset-level summary statistics, and safety notes, while excluding
redistributed PDFs.

## Intended Use

This release is intended for:

- peer review calibration analysis;
- reviewer rationale and score-consistency analysis;
- paper-level and review-level residual/bias analysis;
- reproducing or extending the experiments in *Position: Peer Review Should Be
  Calibrated via LLM Scoring*.
- linking review-level calibration variables to paper text through stable
  `paper_id` keys and `paper_text_index.jsonl.gz` files.

Because the data were prepared for peer review analysis, some fields are best
understood as analysis artifacts rather than raw venue metadata:

- `expected_score` is the LLM-derived anchor score inferred from review
  rationales;
- `bias` is the residual between the reviewer-given score and the anchor score;
- `pros`, `cons`, and `anonymized_items` are structured rationale units used for
  downstream peer review analysis.

## Files

- `data/ICLR_2023/papers.jsonl.gz`: one row per ICLR 2023 paper, sorted
  deterministically by `source_dataset` and `paper_id`.
- `data/ICLR_2023/reviews.jsonl.gz`: one row per ICLR 2023 review, sorted
  deterministically by `paper_id` and `review_id`.
- `data/ICLR_2023/anonymized_items.jsonl.gz`: one row per ICLR 2023
  anonymized pro/con rationale item, sorted deterministically by `paper_id`,
  `mapped_reviewer_id`, and `item_id`.
- `data/ICLR_2023/paper_text_index.jsonl.gz`: one row per ICLR 2023 paper PDF
  conversion record. This file stores `text_path`, page counts, character
  counts, and text-file metadata; it does not embed full text.
- `data/ICLR_2024/`: the same four files for ICLR 2024.
- `data/ICLR_2025/`: the same four files for ICLR 2025.
- `texts/ICLR_2023/{paper_id}.txt`: one extracted UTF-8 text file per ICLR
  2023 paper PDF.
- `texts/ICLR_2024/{paper_id}.txt`: one extracted UTF-8 text file per ICLR
  2024 paper PDF.
- `texts/ICLR_2025_1/{paper_id}.txt` and
  `texts/ICLR_2025_2/{paper_id}.txt`: extracted UTF-8 text files for ICLR
  2025 paper PDFs, split into two directories for repository hosting.
- `metadata/schema.json`: field definitions.
- `metadata/dataset_summary.json`: row counts, source roots, file sizes, and
  sha256 hashes.
- `metadata/safety_scan.txt`: release safety checks.
- `metadata/ICLR_*/conversion_report.json`: per-year PDF-to-text conversion
  summaries.
- `metadata/ICLR_*/paper_text_manifest.jsonl`: per-year PDF-to-text conversion
  manifest.

Only compressed `.jsonl.gz` files are kept in the yearly `data/ICLR_*`
directories. The extracted JSONL files are not distributed in this release. Full
paper text is distributed only as individual `.txt` files under `texts/ICLR_*`;
the ICLR 2025 text layer is split across `texts/ICLR_2025_1/` and
`texts/ICLR_2025_2/` to keep hosted directories within platform file-count
limits.

## Basic Statistics

Total release size:

- Papers: 22,177
- Reviews: 52,369
- Anonymized rationale items: 435,754
- Extracted paper text files: 22,675
- Covered subsets: `ICLR_2023`, `ICLR_2024`, `ICLR_2025`

Yearly subsets:

| Subset | Data directory | Papers | Reviews | Anonymized rationale items | Text files | Text index rows |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| `ICLR_2023` | `data/ICLR_2023/` | 3,507 | 13,248 | 62,915 | 3,791 | 3,791 |
| `ICLR_2024` | `data/ICLR_2024/` | 7,150 | 27,601 | 138,356 | 7,279 | 7,279 |
| `ICLR_2025` | `data/ICLR_2025/` | 11,520 | 11,520 | 234,483 | 11,605 | 11,606 |

`Papers` counts paper-level records, `Reviews` counts review-level records, and
`Anonymized rationale items` counts structured pro/con rationale units extracted
from the review data. The same per-year counts are also recorded in
`metadata/dataset_summary.json`.

## Loading The Data

The release is stored as compressed JSONL files separated by yearly ICLR subset.
A simple Python loading example is:

```python
import gzip
import json

# Select the yearly subset explicitly to avoid mixing conference years.
with gzip.open("data/ICLR_2024/papers.jsonl.gz", "rt", encoding="utf-8") as f:
    papers = [json.loads(line) for line in f]
```

To load paper text, read the yearly text index and then open the referenced
individual text file:

```python
from pathlib import Path
import gzip
import json

root = Path(".")
with gzip.open(root / "data/ICLR_2024/paper_text_index.jsonl.gz", "rt", encoding="utf-8") as f:
    first = json.loads(next(f))

text = (root / first["text_path"]).read_text(encoding="utf-8")
print(first["paper_id"], len(text))
```

Users who need schema-level details should refer to `metadata/schema.json`.

## Dataset Parts And Scoring Pipelines

The dataset comprises three yearly subsets, `ICLR_2023`, `ICLR_2024`, and
`ICLR_2025`, which are integrated under a unified annotation and scoring
protocol.

Across all three subsets, anchor scoring is conducted using **ChatGPT-4o**.
Accordingly, the released `expected_score` and `bias` fields should be
understood as outputs of a common LLM-based calibration framework applied
consistently across years.

This consistency is important for cross-year comparison: the three yearly
subsets are intended to be methodologically aligned rather than treated as
heterogeneous resources produced under incompatible scoring settings.

## Data Leakage Control

Because this dataset is intended for peer review analysis with an LLM-based
scoring framework, we explicitly considered the risk of potential data leakage
or prior model familiarity with individual papers.

To mitigate this concern, we used **ChatGPT-4o** to assess whether candidate
papers were recognizable to the model, and we selected papers judged as
unfamiliar for the downstream peer review analysis. This design choice helps
reduce the risk that anchor scores merely reflect memorized knowledge of specific
submissions rather than the review rationales provided in the evaluation
pipeline.

## Source

The release is derived from OpenReview-linked ICLR 2023, 2024, and 2025 review
data curated for the LLMscore project. The released package contains normalized
paper-level, review-level, and rationale-level records together with associated
metadata required for peer review analysis and reproducibility.

Paper text was extracted from the corresponding PDFs using `pdftotext` with a
layout-preserving UTF-8 configuration. The original PDFs are not redistributed
in this package.

## Limitations

- The release is intended for peer review analysis rather than general-purpose
  paper modeling.
- `expected_score` and `bias` are analysis variables derived under an
  LLM-calibration framework, not official venue annotations.
- The release excludes paper PDFs. Extracted text is provided for convenience,
  and the original OpenReview/PDF links are retained as source references.
- Although familiarity screening is used to mitigate potential data leakage, it
  should be treated as a risk-reduction step rather than an absolute guarantee.

## Acknowledgement

We thank Huazhong University of Science and Technology for supporting this work
and the preparation of this dataset release.

## License

This curated package is released under CC-BY-4.0. Original OpenReview content
should be attributed to OpenReview and the relevant ICLR venue, and users
should follow the original source terms.

## Paper Text Layer

Paper PDF text is stored as UTF-8 `.txt` files partitioned by year:

```text
texts/ICLR_2023/{paper_id}.txt
texts/ICLR_2024/{paper_id}.txt
texts/ICLR_2025_1/{paper_id}.txt
texts/ICLR_2025_2/{paper_id}.txt
```

Each year also contains a text index with paths and text metadata:

```text
data/ICLR_2023/paper_text_index.jsonl.gz
data/ICLR_2024/paper_text_index.jsonl.gz
data/ICLR_2025/paper_text_index.jsonl.gz
```

The index stores `text_path`, character counts, word-like token counts, and
page counts. Full text is stored only in the individual `.txt` files, not
embedded in a combined JSONL file.


## Redaction

Key-like tokens matching common API-key patterns in extracted PDF text are
replaced with `[REDACTED_API_KEY]` or redacted placeholders before release. The
current redaction report records 54 replacements across 42 files. See
`metadata/redaction_report.json` for details.
