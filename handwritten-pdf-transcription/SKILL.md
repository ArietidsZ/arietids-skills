---
name: handwritten-pdf-transcription
description: Use when the user wants a handwritten PDF, notebook-scan PDF, or annotated PDF pages read, digitized, or converted into reviewed Markdown/text, especially when the document mixes prose, math, diagrams, or margin notes and needs high-fidelity extraction.
---

# Handwritten PDF Transcription

Transcribe handwritten PDFs into structured Markdown without dropping content, then automatically run transcription review so the final result is a cleaned Markdown file named after the source PDF. Preserve wording, math, hierarchy, annotations, and diagrams. The output language should match the source document unless the user asks otherwise.

## Required helpers

- Invoke the `pdf` skill for visual PDF handling and page inspection.
- Choose a page-rendering method that keeps crop-capable zoom review possible, for example `pdf2image` or an equivalent renderer that preserves image objects or produces saved renders that can be reopened for cropping and low-confidence re-renders.
- If subagents are available, have page workers write one Markdown file per page under `parts/`; do not collect transcript text inline in worker responses.
- Use 1 page for a single-page document; if subagents are available, use 1 page per batch for 2-5 pages, 2-3 pages per batch for 6-20 pages, and 4-5 pages per batch above 20 pages, and dispatch all batches in parallel in the same turn rather than serially.
- Read this skill's bundled worker-prompt file, `references/transcription-worker-prompt.md`, before dispatching page workers.
- After raw transcription is assembled, immediately invoke `transcription-content-review` on the generated workspace instead of stopping at `transcription.md`.

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
- `zoomed/` stores cropped re-renders for low-confidence regions, for example `zoomed/page_NNN_region_MM.png`.

## Workflow

1. Create `pages/`, `parts/`, and `zoomed/` inside the working folder before rendering pages or dispatching page workers.
2. Render every page to PNG at 300 DPI or higher with a renderer that still allows later crop and re-render zoom work, whether by keeping image objects in memory or by reopening saved page PNGs for cropping.
3. Transcribe each page into `parts/page_NNN.md` with best-effort reading; page workers must write one file per page under `parts/`, not return transcript text inline, and must never skip content or use generic omission placeholders such as `[illegible]`. During page-level transcription and sequential fallback, unresolved marks may be carried inline as temporary `[?]` markers so they can be revisited in the zoom pass. If subagents are unavailable, write the same per-page files sequentially yourself instead of switching output formats: keep each `parts/page_NNN.md` file to page content only, append low-confidence regions in the exact `<!-- LOW_CONFIDENCE ... -->` block shape, for example `<!-- LOW_CONFIDENCE\n- Page 3, upper-left note: "lemma [?]"\n-->`, and compare each page file back against the page image line by line before finalizing it.
4. Keep handwritten structure when it is real: headings, lists, tables, and section breaks.
5. Format math with `$...$` and `$$...$$`.
6. Represent diagrams with ASCII, a precise textual description, or a coordinate-style description that preserves the original relationships.
7. Append a `LOW_CONFIDENCE` comment block to the page file whenever any region is below 99% confidence.
8. Re-open every low-confidence region with cropped zoomed images saved under `zoomed/page_NNN_region_MM.png` and update the page Markdown.
9. Before merge, every page file must have its `LOW_CONFIDENCE` audit comments removed: resolve the text directly, or keep `[?]` only for marks that are still unresolved after the zoom re-check and then delete the comment block.
10. Merge page files in order into `transcription.md`, keeping explicit page markers such as `<!-- Page N -->` between concatenated page files. The merged transcript must not contain any `LOW_CONFIDENCE` comments.
11. Immediately run `transcription-content-review` against the generated transcription workspace.
12. If review succeeds, promote the reviewed output to `<pdf-basename>.md` next to the original PDF, remove `pages/`, `parts/`, `zoomed/`, `transcription.md`, `transcription_reviewed.md`, `review_report.md`, and delete the temporary working folder.
13. If review fails, keep the full temporary workspace for inspection and report that cleanup did not run.
14. Deliver only the final `<pdf-basename>.md` path after a successful review.

## Output rules

- Never summarize or paraphrase during transcription.
- Keep margin notes and inline annotations.
- Use `[?]` only after the zoomed re-check still cannot resolve the mark.
- Never use `[illegible]`, `[unreadable]`, or other omission placeholders.
- The final success state is one reviewed Markdown file next to the source PDF and no temporary workspace.

## Completion checklist

- All pages rendered to `pages/`
- All page files written to `parts/`
- All low-confidence regions rechecked
- `transcription.md` merged and reviewed
- Final reviewed file renamed to `<pdf-basename>.md`
- Temporary workspace deleted after successful review, or preserved on review failure
