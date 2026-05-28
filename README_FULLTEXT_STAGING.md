# LLMscore ICLR Full-Text Staging

This directory contains one full-text staging dataset for PDF-to-text
conversions used by LLMscore. Files are partitioned by year inside the same
dataset so 2023, 2024, and 2025 can be inspected independently before merging.
This staging dataset has not been merged into `open_dataset_llmscore`.

Conversion command: `pdftotext -layout -enc UTF-8 -nopgbrk`.

Files:
- `data/ICLR_{year}/paper_text_index.jsonl.gz`: one row per paper with `text_path`, not full text.
- `texts/ICLR_{year}/{paper_id}.txt`: one extracted UTF-8 text file per paper.
- `metadata/ICLR_{year}/paper_text_manifest.jsonl`: conversion metrics.
- `metadata/ICLR_{year}/conversion_report.json`: aggregate report.
- `metadata/staging_summary.json`: cross-year summary.

Inspect the manifest and samples before deciding whether to update the dataset.
