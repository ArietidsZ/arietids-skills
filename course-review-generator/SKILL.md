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
7. Merge the drafts into one review guide for the chosen scope.
8. Stay grounded in the source materials only; do not import outside textbook knowledge.
9. Keep the guide in the source or course language unless the user explicitly asks for a different output language.
10. Cover definitions, theorems, equivalent conditions, necessary and sufficient conditions, applicability, boundary, and sign conditions when present.
11. Cover formulas, algorithm steps, inference rules, complexity, stability, comparisons, and exam-relevant diagram content when present.
12. For uncertain but possibly testable items, lean toward inclusion and mark them with `[可选记忆]` instead of dropping them.
13. Deduplicate repeated material and merge source tags.
14. Save one final Markdown review document and report its path.

## Non-negotiables

- Include speaker notes, footnotes, recap slides, and summary slides when present.
- Extract reusable general results from worked examples when the source material supports them.
- Preserve exam-relevant diagram content with enough fidelity to retain labels, annotations, axes, legends, and structure when present, and make it clear that the item came from a visual source.
- Mark reorganized content with `==整理==` and directly derived items with `==推得==`.
- Lean toward inclusion when exam relevance is uncertain and mark borderline items with `[可选记忆]`.
- Keep the final document readable as a standalone study guide.
