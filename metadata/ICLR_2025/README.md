# ICLR 2025 Paper Text

This directory stores metadata for the ICLR 2025 paper-text layer in the
LLMscore-ICLR-OpenReview release.

## Files

- `data/ICLR_2025/paper_text_index.jsonl.gz`: one row per paper, pointing to an archive member.
- `texts/ICLR_2025.tar.gz`: one yearly archive containing the converted UTF-8 `.txt` members.
- `metadata/ICLR_2025/paper_text_manifest.jsonl`: conversion metadata and quality flags.
- `metadata/ICLR_2025/conversion_report.json`: aggregate quality report.

## Conversion

Command: `pdftotext -layout -enc UTF-8 -nopgbrk`.

## Current Counts

- text index rows: 11606
- text files: 11605
- text bundle: `texts/ICLR_2025.tar.gz`
