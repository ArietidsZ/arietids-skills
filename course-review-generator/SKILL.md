---
name: course-review-generator
description: Use when the user wants exam review notes, cram sheets, flashcards, or study guides generated from course materials such as PDFs, slides, lecture notes, or handouts, especially when the output should stay grounded in the source material.
---

# Course Review Generator

Generate an exam-focused Markdown study guide from course materials without importing outside content. The result should be complete enough to review directly before an exam.

## Required helpers

- Use `pdf` for PDF sources.
- Use `slides` for `.pptx` sources.
- Use `doc` for `.docx` sources.
- Use `spreadsheet` for `.xlsx`, `.csv`, and `.tsv` sources.
- Read `.md` and `.txt` materials directly.
- For image-based handouts or notes such as `.png`, `.jpg`, and `.jpeg`, inspect the visual content and capture exam-relevant text, labels, and diagram structure.
- Read `references/output-format.md` before assembling the final guide.

## Workflow

1. Inventory every input file and classify it by type.
2. Group the files by topic, chapter, or lecture.
3. If the grouping is ambiguous or the user uploads a full course without clear scope, clarify the target subset before proceeding.
4. When the material is too broad for one useful guide, process one chapter, lecture, or topic subset at a time instead of flattening everything into one massive document.
5. Read each file with the matching helper skill or direct-reading method.
6. If multiple topics are independent and subagents are available, assign one topic per worker and have each worker write a draft Markdown file.
7. When subagents are unavailable or you are processing one subset at a time, save each intermediate topic or chapter draft as a Markdown file before moving on.
8. Merge the drafts into one review guide for the chosen scope.
9. Run a final self-check before delivery to confirm source tags are merged correctly, recap, summary, and learning-objective material is covered, later refinements are deduplicated into original entries, and uncertain-but-testable items are either included or marked `[可选记忆]`.
10. Stay grounded in the source materials only; do not import outside textbook knowledge.
11. Keep the guide in the source or course language unless the user explicitly asks for a different output language.
12. Cover definitions, theorems, equivalent-condition sets, necessary and sufficient condition families, and applicability, boundary, and sign conditions exhaustively when present.
13. Cover formulas, algorithm steps, inference-rule sets, complexity, stability, comparisons, exam-relevant code fragments or code patterns, and exam-relevant diagram content exhaustively when present.
14. For uncertain but possibly testable items, lean toward inclusion and mark them with `[可选记忆]` instead of dropping them.
15. Deduplicate repeated material and merge source tags; when later material repeats an earlier entry, point back to the original item instead of re-listing it, and fold later refinements or addenda into the original item rather than duplicating it.
16. Save one final Markdown review document and report its path.

## Continuation and updates

- If the user asks for the next chapter or lecture, continue from the existing work and generate the next scoped draft or review section.
- If the user asks to supplement a topic, update the existing guide with the requested material instead of regenerating unrelated sections.
- If the user asks to check for omissions, compare the existing guide against the source materials and add missing exam-relevant items.
- Preserve existing drafts and guides as working artifacts for multi-part review sessions.

## Non-negotiables

- Include speaker notes, footnotes, page-footer or footer-style annotations, recap slides, summary slides, and learning-objective slides when present.
- Extract reusable general results from worked examples when the source material supports them.
- Reformulate caveat-style source material such as `However`, `Note that`, `思考`, and similar side remarks into explicit propositions or exam-relevant reminders when they carry testable meaning.
- Preserve exam-relevant diagram content with enough fidelity to retain labels, annotations, axes, legends, and structure when present, and make it clear that the item came from a visual source.
- Preserve exam-relevant code fragments or code patterns as concise fenced code blocks with the correct language tag when the source material includes them.
- Mark reorganized content with `==整理==` and directly derived items with `==推得==`.
- Lean toward inclusion when exam relevance is uncertain and mark borderline items with `[可选记忆]`.
- Keep the final document readable as a standalone study guide.
