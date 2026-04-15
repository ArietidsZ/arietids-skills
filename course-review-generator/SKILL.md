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
- Read `references/output-format.md` before assembling the final guide.

## Workflow

1. Inventory every input file and classify it by type.
2. Group the files by topic, chapter, or lecture.
3. Read each file with the matching helper skill.
4. If multiple topics are independent and subagents are available, assign one topic per worker and have each worker write a draft Markdown file.
5. Merge the drafts into one review guide.
6. Stay grounded in the source materials only; do not import outside textbook knowledge.
7. Cover definitions, theorems, conditions, formulas, algorithms, comparisons, and exam-relevant diagram content when present.
8. Deduplicate repeated material and merge source tags.
9. Save one final Markdown review document and report its path.

## Non-negotiables

- Include speaker notes, footnotes, recap slides, and summary slides when present.
- Extract reusable general results from worked examples when the source material supports them.
- Mark reorganized content with `==整理==` and directly derived items with `==推得==`.
- Keep the final document readable as a standalone study guide.
