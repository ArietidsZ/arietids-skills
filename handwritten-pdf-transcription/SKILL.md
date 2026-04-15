---
name: handwritten-pdf-transcription
description: Use when the user wants a handwritten PDF, notebook scan, or annotated handwritten pages transcribed into Markdown, especially when the document mixes prose, math, diagrams, or margin notes and needs high-fidelity extraction.
---

# Handwritten PDF Transcription

Transcribe handwritten PDFs into structured Markdown without dropping content. Preserve wording, math, hierarchy, annotations, and diagrams. The output language should match the source document.

## Required helpers

- Use `pdf` to render pages and inspect layout.
- Use a page-rendering method that yields image objects suitable for cropped zoom-ins and re-renders of low-confidence regions, not a PNG-only flow that blocks in-memory crop work.
- If the document has multiple pages and subagents are available, split page batches across workers instead of transcribing sequentially.
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

1. Render every page to PNG at 300 DPI or higher with a method that preserves image objects for later cropped zoom work.
2. Transcribe each page into `parts/page_NNN.md`.
3. Keep handwritten structure when it is real: headings, lists, tables, and section breaks.
4. Format math with `$...$` and `$$...$$`.
5. Represent diagrams with Mermaid, ASCII, or a precise structured description.
6. Append a `LOW_CONFIDENCE` comment block to the page file whenever any region is below 99% confidence.
7. Re-open every low-confidence region with cropped zoomed images and update the page Markdown.
8. Merge page files in order into `transcription.md`, keeping explicit page markers such as `<!-- Page N -->` between concatenated page files.
9. Run one final sweep against the rendered page images before delivering the output path.

## Output rules

- Never summarize or paraphrase.
- Keep margin notes and inline annotations.
- Use `[?]` only after the zoomed re-check still cannot resolve the mark.
- Deliver the final `transcription.md` path.

## Completion checklist

- All pages rendered to `pages/`
- All page files written to `parts/`
- All low-confidence regions rechecked
- `transcription.md` merged and reviewed
