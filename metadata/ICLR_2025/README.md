# ICLR 2025 Paper Text

This directory stores metadata for the ICLR 2025 paper-text layer in the
LLMscore-ICLR-OpenReview release.

## Files

- `data/ICLR_2025/paper_text_index.jsonl.gz`: one row per paper, pointing to its txt file.
- `texts/ICLR_2025_1/*.txt` and `texts/ICLR_2025_2/*.txt`: UTF-8 text files for converted PDFs.
- `metadata/ICLR_2025/paper_text_manifest.jsonl`: conversion metadata and quality flags.
- `metadata/ICLR_2025/conversion_report.json`: aggregate quality report.

## Conversion

Command: `pdftotext -layout -enc UTF-8 -nopgbrk`.

## Current Counts

- text index rows: 11606
- text files: 11605
- text directories: `texts/ICLR_2025_1/`, `texts/ICLR_2025_2/`
