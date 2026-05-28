# LLMscore-ICLR-OpenReview

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
anonymized pro/con reason items, anchor scores, and bias values.

The package intentionally does not include paper PDFs or extracted paper full
text. Use `openreview_url` and `pdf_url` to access the original OpenReview
pages.

## Release Scope

This open release is prepared to support both:

- archival artifact release alongside the associated paper; and
- public dataset distribution on platforms such as Hugging Face.

Accordingly, the package is organized as a self-contained lightweight release:
it includes compressed JSONL data, schema information, dataset-level summary
statistics, and a safety note, while excluding redistributed PDFs and extracted
paper full text.

## Intended Use

This release is intended for:

- peer review calibration analysis;
- reviewer rationale and score-consistency analysis;
- paper-level and review-level residual/bias analysis;
- reproducing or extending the experiments in *Position: Peer Review Should Be
  Calibrated via LLM Scoring*.

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
- `data/ICLR_2024/`: the same three files for ICLR 2024.
- `data/ICLR_2025/`: the same three files for ICLR 2025.
- `metadata/schema.json`: field definitions.
- `metadata/dataset_summary.json`: row counts, source roots, file sizes, and
  sha256 hashes.
- `metadata/safety_scan.txt`: release safety checks.

Only the compressed `.jsonl.gz` files are kept in the yearly `data/ICLR_*`
directories. The extracted JSONL files are not distributed in this release.

## Basic Statistics

- Papers: 22,177
- Reviews: 52,369
- Anonymized rationale items: 435,754
- Covered subsets: `ICLR_2023`, `ICLR_2024`, `ICLR_2025`

Per-year counts are also listed in `metadata/dataset_summary.json`.

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

## Recommended Citation

If you use this dataset, please cite the associated paper using the **official
ICML bibliographic entry** for:

- *Position: Peer Review Should Be Calibrated via LLM Scoring*
- Zijin Chen, Lesui Yu, Xiaofei Liao, Hai Jin, and Qinbin Li

To avoid introducing a non-official or incorrectly formatted reference, this
repository does not provide a hand-written BibTeX entry. Users should adopt the
final citation released with the official ICML proceedings record.

## Limitations

- The release is intended for peer review analysis rather than general-purpose
  paper modeling.
- `expected_score` and `bias` are analysis variables derived under an
  LLM-calibration framework, not official venue annotations.
- The release excludes paper PDFs and extracted full text.
- Although familiarity screening is used to mitigate potential data leakage, it
  should be treated as a risk-reduction step rather than an absolute guarantee.

## Acknowledgement

We thank Huazhong University of Science and Technology for supporting this work
and the preparation of this dataset release.

## License

This curated package is released under CC-BY-4.0. Original OpenReview content
should be attributed to OpenReview and the relevant ICLR venue, and users
should follow the original source terms.
