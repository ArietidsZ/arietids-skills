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
- If the user pastes transcription content directly in chat, review that content as the source text. Only write `transcription_reviewed.md` and `review_report.md` when there is a real source-backed folder for the transcription; otherwise return the corrected standalone document and the full review report directly in chat.

## Workflow

1. Read the whole document before editing anything.
2. Identify the subject, level, notation, and language so corrections stay context-aware.
3. Review sequentially for transcription artefacts, mathematical errors, logical errors, definitional errors, factual issues, notation inconsistencies, unit or dimensional errors, cross-reference errors, terminological misuse, and unclear fragments.
4. Start `review_report.md` with an overview, then record each issue in document order with location, category, severity, original text, problem, correction, and enough explanation or working to show why the correction is right.
5. If there is a real source-backed folder for the transcription, write the corrected document to `transcription_reviewed.md` in that folder; otherwise return the corrected standalone document directly in chat.
6. Keep the reviewed document clean; keep reasoning and audit details in `review_report.md`, or in the full review report returned in chat-only runs, including recurring patterns, reconstructed passages, readability or structural changes if applicable, and a final verdict.

## Correction standard

- Fix every known issue.
- Prefer the smallest correction that makes the document accurate and readable.
- Preserve the author's structure and voice; fix and clarify rather than substantially reorganizing or adding content beyond what is needed for correctness and readability.
- Make only minimal structural adjustments when they are necessary to keep the document coherent or usable.
- Remove `[?]`, `LOW_CONFIDENCE`, and stray editorial HTML comments from the final reviewed document, but keep page-marker comments when they help navigation or auditing against source pages.
- If the final text depends on reconstruction rather than certainty, mark it with a footnote or another visible inline indicator and explain the reconstruction in the report.
- If a passage remains ambiguous, choose the best interpretation, mark it visibly in the final document, and explain that choice in the report.

## Review report requirements

- Include an overview with subject, level, a brief summary of the review outcome, and an issue-count breakdown by category and severity.
- Use a consistent severity scale for every issue: `Critical` for errors that make the notes unreliable or materially wrong, `Significant` for corrections that affect meaning or correctness in a limited scope, and `Minor` for readability, notation, or presentation fixes that do not materially change the claim.
- List issues in document order.
- Include a recurring-patterns section when similar issues appear more than once.
- Include a reconstructed-passages section listing every place where the final text depends on reconstruction or ambiguity resolution.
- Include a readability or structural changes summary when those edits were made.
- End with a verdict on the overall reliability of the reviewed notes.

## Deliverables

- `transcription_reviewed.md` and `review_report.md` for file-backed runs
- The corrected standalone document and full review report returned directly in chat for chat-only runs

Report both file paths back to the user for file-backed runs, or clearly label both outputs in chat-only runs, and call out any heavily reconstructed passages.

## Completion checklist

- The full source text was read before edits, whether it came from `transcription.md`, another Markdown file, or pasted chat content.
- Subject, level, notation, and language were identified well enough to review contextually.
- Transcription artefacts, mathematical issues, logical issues, definitional errors, factual issues, notation inconsistencies, unit or dimensional errors, cross-reference errors, terminological misuse, and unclear passages were reviewed and corrected.
- Reconstructed or ambiguous passages are visibly marked in the final reviewed document and explained in the report.
- `transcription_reviewed.md` is clean, readable, and free of unresolved placeholders or hidden review markup.
- The author's structure was preserved unless a minimal structural adjustment was necessary for correctness or readability.
- `review_report.md` includes the required overview, per-issue entries, severity labels, and closing sections.
- Both deliverables were saved in the source-backed folder when one existed and both were presented back to the user, or both were returned directly in chat when only pasted content was available without a source-backed folder.
