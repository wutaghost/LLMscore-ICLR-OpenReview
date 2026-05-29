# ICLR 2024 Paper Full-Text Staging

This is the ICLR 2024 partition of the LLMscore full-text staging dataset.
It has not been merged into the released dataset.

## Files

- `data/ICLR_2024/paper_text_index.jsonl.gz`: one row per paper, pointing to an archive member.
- `texts/ICLR_2024.tar.gz`: one yearly archive containing the converted UTF-8 `.txt` members.
- `metadata/ICLR_2024/paper_text_manifest.jsonl`: conversion metadata and quality flags.
- `metadata/ICLR_2024/conversion_report.json`: aggregate quality report.

## Conversion

Command: `pdftotext -layout -enc UTF-8 -nopgbrk`.

## Current Counts

- total: 7279
- ok: 7233
- warning: 46
- error: 0

Rows with warnings or errors should be inspected before this staging dataset is
merged into the public release.
