---
name: handwritten-pdf-transcription
description: Use when the user wants a handwritten PDF, notebook scan, or annotated handwritten pages transcribed into Markdown, especially when the document mixes prose, math, diagrams, or margin notes and needs high-fidelity extraction.
---

# Handwritten PDF Transcription

Transcribe handwritten PDFs into structured Markdown without dropping content. Preserve wording, math, hierarchy, annotations, and diagrams. The output language should match the source document.

## Required helpers

- Use `pdf` for visual PDF handling and page inspection.
- Choose a page-rendering method that preserves image objects for cropped zoom-ins and low-confidence re-renders, not a PNG-only flow that blocks in-memory crop work.
- If subagents are available, have page workers write one Markdown file per page under `parts/`; do not collect transcript text inline in worker responses.
- If subagents are available, use 1 page per batch for 2-5 pages, 2-3 pages per batch for 6-20 pages, and 4-5 pages per batch above 20 pages.
- Read `references/transcription-worker-prompt.md` before dispatching page workers.

## Working folder

Create a working folder next to the source PDF using the PDF filename without `.pdf`.

```text
notes/
  pages/
  parts/
  zoomed/
  transcription.md
```

- `pages/` stores full-page PNG renders.
- `parts/` stores one Markdown file per page.
- `zoomed/` stores cropped re-renders for low-confidence regions.

## Workflow

1. Create `pages/`, `parts/`, and `zoomed/` inside the working folder before rendering pages or dispatching page workers.
2. Render every page to PNG at 300 DPI or higher with a method that preserves image objects for later cropped zoom work.
3. Transcribe each page into `parts/page_NNN.md` with best-effort reading; page workers must write one file per page under `parts/`, not return transcript text inline, and must never skip content or use generic omission placeholders such as `[illegible]`.
4. Keep handwritten structure when it is real: headings, lists, tables, and section breaks.
5. Format math with `$...$` and `$$...$$`.
6. Represent diagrams with Mermaid, ASCII, or a precise structured description.
7. Append a `LOW_CONFIDENCE` comment block to the page file whenever any region is below 99% confidence.
8. Re-open every low-confidence region with cropped zoomed images and update the page Markdown.
9. Before merge, every page file must have its `LOW_CONFIDENCE` audit comments removed: resolve the text directly, or convert any still-unresolved mark into the final inline `[?]` form and then delete the comment block.
10. Merge page files in order into `transcription.md`, keeping explicit page markers such as `<!-- Page N -->` between concatenated page files. The merged transcript must not contain any `LOW_CONFIDENCE` comments.
11. Run one final sweep against the rendered page images before delivering the output path.

## Output rules

- Never summarize or paraphrase.
- Keep margin notes and inline annotations.
- Use `[?]` only after the zoomed re-check still cannot resolve the mark.
- Never use `[illegible]`, `[unreadable]`, or other omission placeholders.
- Deliver the final `transcription.md` path.

## Completion checklist

- All pages rendered to `pages/`
- All page files written to `parts/`
- All low-confidence regions rechecked
- `transcription.md` merged and reviewed
