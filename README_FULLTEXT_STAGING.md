# LLMscore ICLR Full-Text Staging

This directory contains one full-text staging dataset for PDF-to-text
conversions used by LLMscore. Files are partitioned by year inside the same
dataset so 2023, 2024, and 2025 can be inspected independently before merging.
This staging dataset has not been merged into `open_dataset_llmscore`.

Conversion command: `pdftotext -layout -enc UTF-8 -nopgbrk`.

Files:
- `data/ICLR_{year}/paper_text_index.jsonl.gz`: one row per paper with an `archive::member` text locator, not full text.
- `texts/ICLR_2023.tar.gz`, `texts/ICLR_2024.tar.gz`, `texts/ICLR_2025.tar.gz`: yearly archives containing extracted UTF-8 `.txt` members.
- `metadata/ICLR_{year}/paper_text_manifest.jsonl`: conversion metrics.
- `metadata/ICLR_{year}/conversion_report.json`: aggregate report.
- `metadata/staging_summary.json`: cross-year summary.

Inspect the manifest and samples before deciding whether to update the dataset.
