---
name: transcription-content-review
description: "Use when the user wants a transcribed handwritten note or `transcription.md` reviewed for correctness, transcription artefacts, mathematical or factual errors, or polished into a standalone document. Especially applicable after `handwritten-pdf-transcription`, or when the user asks to check notes for mistakes, verify the math, fact-check the transcription, or clean up transcribed handwritten material."
---

# Transcription Content Review

Scrutinise a transcribed handwritten note and produce a **standalone, correct, readable document** that a reader can use independently — without access to the original handwriting, without guessing what the author meant, and without encountering errors of any kind.

## Governing philosophy

The transcription is a **draft**. It may contain errors the author made while writing, errors the transcription process introduced, or passages that are technically correct but hard to follow. **None of these distinctions matter for the output.** The only question is: *does the final document say the right thing clearly?*

This means:

- A garbled symbol from OCR/transcription gets the same treatment as a mistake the author made — find the correct expression and use it.
- An awkward sentence that faithfully reproduces awkward handwriting still gets improved — the reader wasn't there; they need the text to stand on its own.
- A `[?]` marker left by the transcription process is not acceptable in the final output. Resolve it using context, domain knowledge, and the original page images if available. If it truly cannot be resolved, replace the marker with the best reasonable interpretation and add a footnote.
- The output is judged by how good it is as a **document**, not by how close it is to the handwriting.

---

## Scope — what this skill checks and fixes

| Category | Examples |
|---|---|
| **Mathematical errors** | Algebra mistakes, wrong derivatives/integrals, sign errors, incorrect limits, flawed proofs, off-by-one in combinatorics, wrong index ranges |
| **Transcription artefacts** | Garbled symbols ($\partial$ misread as $d$, $\tau$ as $t$, $\Sigma$ as $E$), broken LaTeX, nonsensical fragments, `[?]` markers, duplicated or dropped words from OCR |
| **Definitional errors** | Imprecise or incorrect definitions of technical terms, missing conditions (e.g. "continuous" where "uniformly continuous" is required) |
| **Logical errors** | Non-sequiturs, circular reasoning, unstated assumptions, incorrect implication direction, affirming the consequent |
| **Factual errors** | Wrong dates, misattributed theorems/quotes, incorrect physical constants, wrong chemical formulas, historical inaccuracies |
| **Notational inconsistencies** | Same symbol used for different things, switching between conventions mid-document, inconsistent indexing |
| **Unit / dimensional errors** | Dimensional analysis failures, missing units, incompatible unit conversions |
| **Structural / cross-reference errors** | References to equations/theorems/sections that don't match, misnumbered items, dangling references |
| **Terminological misuse** | Confusing related-but-distinct concepts (e.g. "continuous" vs "differentiable", "correlation" vs "causation") |
| **Readability issues** | Sentence fragments that don't parse, ambiguous pronoun references, missing logical connectives between steps, paragraphs that assume context the reader doesn't have |

---

## Step 0 — Determine the source material

Identify what needs to be reviewed:

1. **If a `transcription.md` file exists in a handwritten transcription working folder** (output of the handwritten-pdf-transcription skill): use it as the primary input.
2. **If the user provides or points to a different markdown file**: use that.
3. **If the user pastes content directly in chat**: work with the pasted content.
4. **If the original PDF page images are available in the sibling `pages/` folder created by that workflow**: keep them accessible. They are essential for resolving transcription artefacts — when a passage looks wrong, checking the original image often reveals what the author actually wrote.

Read the entire transcription before beginning review. Build a mental model of the document's subject, structure, and intent before touching anything.

---

## Step 1 — Subject identification and context calibration

Before reviewing, establish:

1. **Subject domain(s):** What field(s) do these notes cover? (e.g. real analysis, organic chemistry, microeconomics, European history)
2. **Level:** Introductory, intermediate, advanced, or research-level?
3. **Notation conventions:** What conventions does the author use? (e.g. $f'$ vs $\dot{f}$ for derivatives, $\subset$ meaning proper subset or subset-or-equal, $\log$ meaning $\ln$ or $\log_{10}$)
4. **Language:** What language are the notes in?

Document these findings internally. This prevents false positives — judge correctness relative to the conventions the author is using, not your own defaults. But if the author's conventions are genuinely non-standard in ways that would confuse a reader, normalise them in the final output and note the change in the review report.

---

## Step 2 — Systematic line-by-line review

Work through the transcription **sequentially**, section by section. For each content unit (a statement, equation, derivation step, definition, example, or diagram description), evaluate the following:

### 2a. Transcription integrity

This comes first because downstream checks are pointless if the text itself is garbled.

- **Resolve all `[?]` markers.** Use surrounding context, domain knowledge, and original page images (if available) to determine the intended text. Replace the marker with the correct expression.
- **Detect and fix garbled symbols.** Look for symbols or words that don't make sense in context. Common transcription confusions: $\partial \leftrightarrow d$, $\tau \leftrightarrow t$, $\zeta \leftrightarrow \xi$, $\Sigma \leftrightarrow E$, $\in \leftrightarrow \epsilon$, $l \leftrightarrow 1$, $O \leftrightarrow 0$, $\rho \leftrightarrow p$. When you spot one, determine the correct symbol from context and substitute it.
- **Fix broken LaTeX.** Unclosed delimiters, malformed commands, mismatched brackets — repair them so every equation renders correctly.
- **Recover dropped or duplicated content.** If a sentence is obviously missing a word, or a phrase is repeated due to OCR stutter, fix it.

### 2b. Mathematical content

- **Verify every algebraic manipulation.** Do not skim; re-derive each step mentally. Check sign errors, missing terms, incorrect factoring, wrong applications of rules (product rule, chain rule, integration by parts, etc.).
- **Check boundary / edge cases.** Does a stated inequality hold at the boundary? Is a "$<$" actually a "$\leq$"? Is an "$n \geq 1$" actually "$n \geq 0$"?
- **Verify named results.** If the notes state a theorem (e.g. "By the Mean Value Theorem..."), confirm the theorem is being stated and applied correctly, with all necessary hypotheses satisfied.
- **Dimensional / type checking.** Do both sides of an equation have the same dimensions? Is a scalar being equated to a vector? Is a function being evaluated at an argument of the right type?
- **Check convergence claims.** If a series, integral, or limit is claimed to converge/diverge, verify.
- **Verify computed values.** If the notes compute a numerical result (e.g. "= 42"), re-compute it.

### 2c. Definitions and terminology

- **Correctness.** Is the definition correct? Are all necessary conditions included?
- **Precision.** Is the definition precise enough to be unambiguous? (e.g. "a function is continuous" — on what set? at a point or everywhere?) If the author left precision implicit and a reader would be confused, make it explicit.
- **Consistency.** Is the same term defined differently in different places? Unify.

### 2d. Logical structure

- **Implication direction.** Verify that "$A \Rightarrow B$" is not confused with "$B \Rightarrow A$" or "$A \Leftrightarrow B$".
- **Quantifier correctness.** "$\forall$" vs "$\exists$", order of quantifiers.
- **Proof validity.** If a proof or argument is given, check each logical step. Does the conclusion actually follow from the premises?
- **Stated vs actual conditions.** If the notes say "since $f$ is continuous, we have..." — does continuity actually suffice, or is a stronger condition needed?

### 2e. Factual claims

- **Named facts.** Dates, attributions, physical constants, chemical formulas — verify against your knowledge.
- **Citations and references.** If the notes reference a specific theorem number, equation, or section, check that the reference matches.

### 2f. Notational consistency

- **Unify notation throughout the document.** If the author switches between equivalent notations (e.g. $f'(x)$ and $\frac{df}{dx}$) inconsistently, pick the one they use more frequently and standardise. Note the change in the report.
- **Resolve overloaded symbols.** If the same symbol is used for different things in different sections, disambiguate.

### 2g. Readability and standalone usability

- **Fill in implicit context.** If a derivation jumps from step A to step C and a reader would be lost, add the missing connective (e.g. "Applying the chain rule to both sides gives..."). Keep additions minimal and clearly serving comprehension.
- **Fix sentence fragments.** Handwritten notes often use telegraphic phrasing that works when you were in the lecture but is opaque later. If a fragment is ambiguous or unparseable on its own, expand it into a clear sentence — but stay faithful to the author's intent.
- **Clarify ambiguous references.** "Substituting this into the above equation" — which equation? If identifiable, make it explicit.
- **Ensure structural coherence.** Do sections flow logically? Are heading levels consistent? Is there a sensible hierarchy? Adjust heading levels if they're inconsistent, and add brief transitional phrases between sections if the document reads as disconnected fragments.

---

## Step 3 — Classify and record each finding

For each issue found, record it with the following structure:

```
### Issue [N]

- **Location:** [Section/heading, approximate line or equation, page marker if present]
- **Category:** [One of: Mathematical error | Transcription artefact | Definitional error | Logical error | Factual error | Notational inconsistency | Unit/dimensional error | Cross-reference error | Terminological misuse | Readability improvement]
- **Severity:** [Critical | Significant | Minor]
- **Original text:** [The exact text from the transcription]
- **Problem:** [Clear explanation of what is wrong and why]
- **Correction:** [The corrected text, ready to substitute in]
```

**Severity definitions:**

- **Critical:** The error produces a wrong result, invalidates a proof, states something factually false, or renders a passage incomprehensible. The document cannot be trusted without this fix.
- **Significant:** The error is wrong or misleading but is unlikely to propagate (e.g. a self-contained numerical slip, a single garbled symbol with obvious intent), or involves missing conditions that matter in edge cases.
- **Minor:** Notational inconsistency, imprecise but not wrong phrasing, readability improvement, or a cosmetic issue.

**All issues are fixed in the output regardless of severity.** The severity classification exists for the review report so the user knows what matters most — not to decide whether to apply a fix.

---

## Step 4 — Produce the final document

Create a new file — `transcription_reviewed.md` — that is the **definitive, corrected, readable version** of the notes. This is the primary deliverable.

### Rules for the final document

1. **Every identified issue is corrected.** No exceptions. No "left as-is because low confidence." If you are unsure of the correct expression, use the best reasonable interpretation and add a footnote: `[^n]: Reconstructed from unclear source; original may have intended [alternative].`
2. **No `[?]` markers, no `<!-- LOW_CONFIDENCE -->` blocks, no `<!-- CORRECTED -->` annotations.** The final document is clean. All editorial apparatus goes in the review report, not here.
3. **No HTML comments of any kind.** The document should render as a clean, readable Markdown file.
4. **Notation is consistent throughout.** If you standardised notation, apply it everywhere.
5. **LaTeX is valid and renders correctly.** Every `$...$` and `$$...$$` block must be syntactically correct.
6. **Readability improvements are applied.** Missing connectives, expanded fragments, clarified references — all in the final version.
7. **Preserve the author's structure and intent.** You are fixing and polishing, not rewriting. The document should still feel like the author's notes — just the best possible version of them. Do not reorganise sections, merge topics, or add substantial new content beyond what is needed for clarity and correctness.
8. **Page markers** (`<!-- Page N -->`) may be kept or removed depending on whether they aid navigation. If the document is short, remove them. If long, keep them.

### The litmus test

A reader who has never seen the original handwriting should be able to pick up `transcription_reviewed.md` and:
- Understand every passage without guessing
- Trust every equation and factual claim
- Follow every logical argument from premise to conclusion
- Never encounter a garbled symbol, broken formula, or mysterious `[?]`

---

## Step 5 — Produce the review report

Create a separate file — `review_report.md` — that documents everything that was changed and why. This is the audit trail. It contains:

1. **Overview:** Subject domain, level, total number of issues found broken down by category and severity.
2. **All issues** in the format from Step 3, ordered by location in the document (sequential, following the document's flow). For each mathematical correction, include enough working to demonstrate that the correction is itself correct.
3. **Patterns:** If the same type of error recurs (e.g. the author consistently drops a negative sign, or the transcription consistently confuses $\partial$ with $d$), call this out explicitly. Patterns are the most actionable feedback.
4. **Readability changes:** Summarise structural and readability improvements (expanded fragments, added connectives, unified notation) in a separate section so the user can review them distinctly from correctness fixes.
5. **Reconstructed passages:** Any passage where the correction involved significant interpretation (i.e. the original was badly garbled and you had to infer intent), listed with the reasoning behind the chosen reconstruction. These are the items the user should double-check.
6. **Verdict:** A brief overall assessment — how reliable are these notes? How many substantive corrections were needed? Is this a case of minor slips in otherwise solid work, or are there systemic issues?

---

## Step 6 — Deliver

1. Save `transcription_reviewed.md` in the same folder as the original transcription.
2. Save `review_report.md` in the same folder.
3. Present both files to the user. **Lead with the reviewed transcription** — that is the primary output.
4. Give the user a brief conversational summary: how many issues were found, what the most important corrections are, and any reconstructed passages they should verify.

---

## Principles

- **The output is the product.** The final document is not a comparison artefact — it is a standalone reference. Judge it by whether a reader can use it cold, not by how close it is to the original handwriting.
- **Fix everything.** Do not leave known errors in the document for any reason. If something is wrong, fix it. If you're not sure what's right, use the best interpretation and flag it in the report.
- **Show your work in the report, not the document.** The reviewed transcription is clean. All reasoning, alternatives considered, and editorial comments go in the review report.
- **Minimal necessary intervention.** Fix what is broken; improve what is unclear; leave everything else alone. The document should still read like the author's notes — just the best possible version of them.
- **Mathematical corrections must be demonstrated.** For every equation you change, include enough derivation in the review report that the user can verify your fix. Do not just assert "this is wrong."
- **Transcription artefacts get no special status.** A wrong symbol is a wrong symbol, whether the author wrote it wrong or the OCR read it wrong. The correction is the same either way. In the review report, you may note when you suspect an error originated from the transcription process (this is useful feedback for improving the transcription skill), but the error is still fixed unconditionally.

---

## Completion checklist

The job is complete only when all of these are true:

- Subject domain, level, and notation conventions identified
- Every section of the transcription reviewed line-by-line
- All issues classified by category and severity
- All `[?]` markers resolved
- All transcription artefacts corrected
- All mathematical, logical, factual, and definitional errors corrected
- Notation unified throughout the document
- Readability improvements applied where needed
- Final document (`transcription_reviewed.md`) is clean, correct, and standalone-usable with no editorial markup
- Review report (`review_report.md`) documents every change with reasoning
- Both files saved and presented to the user
- Conversational summary delivered
