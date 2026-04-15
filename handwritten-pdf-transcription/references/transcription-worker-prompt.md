# Transcription Worker Prompt

Use this prompt when dispatching page workers for handwritten transcription.

## Worker prompt

Tell the worker which page numbers it owns, the full image paths for those pages, and the exact working-folder path where it must write one Markdown file per page to `parts/page_NNN.md`.

The worker prompt should say:

You are transcribing handwritten PDF pages into Markdown.

Rules:

- Account for every visible mark and content region on the page, including hard-to-read text, symbols, annotations, arrows, labels, and diagram elements.
- Use best-effort transcription for every visible mark or region; do not drop difficult content or omit uncertain material.
- Keep the source language.
- Use Markdown headings and lists only when the handwritten structure clearly supports them.
- Format math with `$...$` and `$$...$$`.
- Represent diagrams with Mermaid, ASCII, or a precise structured description.
- Do not add filename headers or page-number prefixes inside the page file.
- Never use `[illegible]`, `[unreadable]`, or other generic omission placeholders.
- Use `[?]` only for marks or regions that remain unresolved after best-effort reading, preserve that uncertainty inline instead of omitting it, and list each such `[?]` case in the `LOW_CONFIDENCE` block so the main agent revisits it during the zoom pass.
- Even when you make a best-guess transcription without inline `[?]`, every region below 99% confidence must still be listed in the `LOW_CONFIDENCE` block for zoom-pass review.

If confidence drops below 99% for any region, append this block at the end of the page file. Include both unresolved `[?]` cases and sub-99% best guesses:

```markdown
<!-- LOW_CONFIDENCE
- Page 2, lower-right margin: "bounded [?]" could be "bonded"
-->
```

Before saving the file, compare the Markdown back against the image line by line.

`[?]` is temporary at this stage: it should survive into final output only if the main agent rechecks that region with zoomed images and it is still unresolved.
