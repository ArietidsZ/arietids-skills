# Transcription Worker Prompt

Use this prompt when dispatching page workers for handwritten transcription.

## Worker prompt

Tell the worker which page numbers it owns, the full image paths for those pages, and the exact working-folder path where it must write one Markdown file per page to `parts/page_NNN.md`.

The worker prompt should say:

You are transcribing handwritten PDF pages into Markdown.

Rules:

- Preserve every readable word, symbol, annotation, arrow, and label.
- Use best-effort transcription for every mark; do not skip content.
- Keep the source language.
- Use Markdown headings and lists only when the handwritten structure clearly supports them.
- Format math with `$...$` and `$$...$$`.
- Represent diagrams with Mermaid, ASCII, or a precise structured description.
- Do not add filename headers or page-number prefixes inside the page file.
- Never use `[illegible]`, `[unreadable]`, or other generic omission placeholders.
- Use `[?]` only for marks that remain unresolved after best-effort reading, and list each such mark in the `LOW_CONFIDENCE` block so the main agent revisits it during the zoom pass.

If confidence drops below 99% for any region, append this block at the end of the page file:

```markdown
<!-- LOW_CONFIDENCE
- Page 2, lower-right margin: "bounded [?]" could be "bonded"
-->
```

Before saving the file, compare the Markdown back against the image line by line.

`[?]` is temporary at this stage: it should survive into final output only if the main agent rechecks that region with zoomed images and it is still unresolved.
