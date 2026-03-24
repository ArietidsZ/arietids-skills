---
name: handwritten-pdf-transcription
description: "Transcribe handwritten notes from PDF files into well-structured, high-fidelity Markdown. Use this skill whenever the user uploads a handwritten PDF and wants it transcribed, digitized, or converted to text/Markdown. Also trigger when the user mentions 'handwriting', 'handwritten notes', 'lecture notes', 'scanned notes', or asks to 'read my notes' from a PDF. This skill handles the full pipeline: PDF-to-image rendering, visual transcription with zoom-in verification for hard-to-read regions, and parallel subagent processing for multi-page documents. The output matches the language of the handwriting and preserves all content including math, diagrams, and margin annotations."
---

# Handwritten PDF Transcription

Transcribe handwritten notes from a PDF into well-structured, high-fidelity Markdown. Every word, symbol, annotation, and diagram must be captured. The output language must match the language of the handwriting. No content may be summarized, paraphrased, or omitted.

## Step 1 — Read dependencies

Before anything else, read the local PDF skill:

```
/Users/arietids/.agents/skills/pdf/SKILL.md
```

Use `pdf2image` (`convert_from_path`) for page rendering, not `pdftoppm`, because you need PIL Image objects for cropping during the zoom-in pass.

## Step 2 — Set up the working folder

Create a single folder that holds **everything** — rendered images, zoomed crops, per-page markdown files, and the final merged output. The folder shares the PDF's name (minus `.pdf`) and lives next to the source file.

```
# Example: source is /Users/arietids/Downloads/notes.pdf
#
# Working folder layout:
# /Users/arietids/Downloads/notes/                    ← working root
# /Users/arietids/Downloads/notes/pages/              ← full-page images
# /Users/arietids/Downloads/notes/pages/page_001.png
# /Users/arietids/Downloads/notes/pages/page_002.png
# /Users/arietids/Downloads/notes/zoomed/             ← cropped re-renders
# /Users/arietids/Downloads/notes/zoomed/page_002_region_01.png
# /Users/arietids/Downloads/notes/parts/              ← per-page markdown from subagents
# /Users/arietids/Downloads/notes/parts/page_001.md
# /Users/arietids/Downloads/notes/parts/page_002.md
# /Users/arietids/Downloads/notes/transcription.md    ← final merged output
```

Create all subdirectories (`pages/`, `zoomed/`, `parts/`) up front.

## Step 3 — Render PDF pages to images

Convert every page at **300 DPI minimum** using `pdf2image`:

```python
from pdf2image import convert_from_path

images = convert_from_path(pdf_path, dpi=300)
for i, img in enumerate(images):
    img.save(f"{working_folder}/pages/page_{i+1:03d}.png")
```

## Step 4 — Dispatch parallel subagents for transcription

Always prefer parallel subagents over sequential processing. This saves context window and improves throughput. Divide pages into batches and dispatch one subagent per batch.

### Batching strategy

- **≤ 5 pages:** 1 page per subagent (full parallelism)
- **6–20 pages:** 2–3 pages per subagent
- **> 20 pages:** 4–5 pages per subagent

### Subagent contract

Each subagent **writes its output to markdown files** in the `parts/` subfolder — one `.md` file per page. Subagents must never return markdown content inline; they must write files. This makes merging reliable regardless of output length.

Every subagent prompt must include **all of the following** verbatim or in equivalent detail:

---

**START OF SUBAGENT PROMPT TEMPLATE**

You are transcribing handwritten notes from PDF page images into Markdown.

**Core goal:** Transcribe with 100% fidelity. Every word, symbol, annotation, diagram, and margin note must be captured. The output language must match the language of the handwriting. Do not summarize, paraphrase, or omit any content.

**Your assigned pages:** [LIST PAGE NUMBERS]

**Image paths:** [LIST FULL PATHS TO PAGE IMAGES]

**Output:** For each assigned page, write a markdown file to:
`[working_folder]/parts/page_NNN.md`

Each file should contain only the transcribed content for that page (no filename headers, no page-number prefixes — just the content). Use `---` as a page separator only if the page has a clear section break in the handwriting itself.

**Formatting rules:**

| Handwritten Element | Markdown Representation |
|---|---|
| Main titles | `# Heading 1` |
| Subsections | `## Heading 2` or `### Heading 3` |
| Outline / nested structures | Nested bullet points (`-`) or numbered lists (`1.`) |

- Preserve the exact wording, sequence, and organizational hierarchy as written.
- Include all content: definitions, examples, side annotations, margin notes.
- Format key terms, underlined words, or emphasized concepts using **bold** or *italics*.
- Use LaTeX syntax for all math: inline `$...$`, display `$$...$$`.
- Preserve all subscripts (`$a_n$`), superscripts (`$x^2$`), Greek letters (`$\alpha, \epsilon, \delta$`), and operators (`$\int, \sum, \forall, \exists, \in$`).
- For diagrams/figures: use ASCII art, textual descriptions, or coordinate-based explanations — whatever is most faithful. Capture all arrows, connectors, and visual relationships.
- Make every effort to decipher difficult handwriting using context. Mark genuinely uncertain transcriptions with `[?]` only as a last resort. Never use `[illegible]` or skip content.

**Confidence tracking:** After transcription, identify any region where your confidence is below 99%. For each such region, note the page number, approximate location, and the uncertain text in a comment block at the very end of the markdown file:

```markdown
<!-- LOW_CONFIDENCE
- Line 5: "∂f/∂x" might be "df/dx" — strokes ambiguous
- Margin note near bottom-right: word after "hence" unclear, transcribed as "bounded [?]"
-->
```

**Validation:** Before writing each file, compare your transcription against the source image line by line. Confirm character-level accuracy for text and structural accuracy for equations, lists, and diagrams.

**END OF SUBAGENT PROMPT TEMPLATE**

---

### Launching subagents

Dispatch all subagents in the **same turn** so they run in parallel. Do not wait for one batch to finish before starting the next.

## Step 5 — Zoom-in pass on low-confidence regions

After all subagents complete, scan every `parts/page_NNN.md` file for `<!-- LOW_CONFIDENCE` blocks. For each flagged region:

1. Crop the relevant area from the full-page image.
2. Re-render at 2×–4× magnification and save to `zoomed/page_NNN_region_MM.png`.
3. Re-examine the zoomed crop visually.
4. Update the markdown file in `parts/` with corrections.
5. Remove or update the `LOW_CONFIDENCE` comment.

If a region remains unresolvable after zoomed inspection, keep the `[?]` marker.

## Step 6 — Merge into final output

Concatenate all `parts/page_NNN.md` files in numerical order into a single `transcription.md` at the working folder root. Insert page-break markers between pages:

```markdown
<!-- Page 1 -->
[content of page_001.md]

---

<!-- Page 2 -->
[content of page_002.md]

...
```

The merge is a simple file concatenation — this is why subagents write files instead of returning content inline.

## Step 7 — Final validation sweep

Perform a full-document comparison: read `transcription.md` and compare against the rendered page images. Check for:

- Missing content (skipped lines, dropped margin notes)
- Structural errors (wrong heading levels, broken list nesting)
- Math formatting issues (unclosed `$`, malformed LaTeX)
- Remaining `[?]` markers that can now be resolved in context

Fix any discrepancies directly in `transcription.md`.

## Step 8 — Deliver

Leave `transcription.md` in the working folder and present its full path to the user.

## Completion checklist

The job is complete only when all of these are true:

- All pages rendered to images in `pages/`
- All pages transcribed via parallel subagents to `parts/`
- All low-confidence regions zoomed and re-examined
- All per-page files merged into `transcription.md`
- Final validation sweep passed with no remaining discrepancies
- Output delivered to the user
