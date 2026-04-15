---
name: transcription-content-review
description: Use when the user wants a transcription, lecture-note Markdown file, or `transcription.md` checked for correctness, cleaned into a standalone document, or reviewed for math, logic, notation, factual, or readability issues.
---

# Transcription Content Review

Review a transcription and turn it into a corrected standalone document. Fix transcription artefacts, mathematical or logical mistakes, notation drift, and unclear prose while preserving the author's intent.

## Inputs

- Prefer `transcription.md` when reviewing the output of handwritten transcription.
- If sibling page images are available, use them to resolve ambiguous passages.
- If the user points to another Markdown file, review that file instead.

## Workflow

1. Read the whole document before editing anything.
2. Identify the subject, level, notation, and language so corrections stay context-aware.
3. Review sequentially for transcription artefacts, mathematical errors, logical errors, factual issues, notation inconsistencies, and unclear fragments.
4. Record each issue in `review_report.md` with location, category, severity, original text, problem, correction, and enough explanation or working to show why the correction is right.
5. Write the corrected document to `transcription_reviewed.md` in the same folder.
6. Keep the reviewed document clean; keep reasoning and audit details in `review_report.md`.

## Correction standard

- Fix every known issue.
- Prefer the smallest correction that makes the document accurate and readable.
- Remove `[?]`, `LOW_CONFIDENCE`, and HTML comments from the final reviewed document.
- If the final text depends on reconstruction rather than certainty, mark it with a footnote or another visible inline indicator and explain the reconstruction in the report.
- If a passage remains ambiguous, choose the best interpretation, mark it visibly in the final document, and explain that choice in the report.

## Deliverables

- `transcription_reviewed.md`
- `review_report.md`

Report both paths back to the user and call out any heavily reconstructed passages.
