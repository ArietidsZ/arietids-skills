---
name: course-review-generator
description: "Use when the user wants exam-oriented review notes, cram sheets, flashcards, or a study guide from course slides, PDFs, lecture notes, or handouts. Trigger for requests like '复习资料', '知识集锦', '考前冲刺', 'review notes', 'study sheet', 'cheat sheet from slides', 'help me study for', 'make flashcards from', or when uploaded lecture materials need a condensed, study-focused summary. Output a single Markdown review document with definitions, theorems, formulas, algorithms, comparisons, source annotations, and LaTeX math."
---

# Course Review Material Generator

Produce a **single, exhaustive, exam-oriented knowledge compendium** from uploaded course materials (slides, PDFs, lecture notes). The document must be complete enough to serve as the reader's **only** review resource before an exam — every testable definition, theorem, formula, algorithm, and comparison must be captured.

## Step 0 — Ingest and organise source material

### 0a. Locate the files

Read all files from the upload directory (or the directory the user specifies). List every file found and classify each by type.

### 0b. Read each file using the appropriate skill

Choose the reading method based on file type:

| File type | How to read |
|---|---|
| PDF (`.pdf`) | Use the **pdf** skill. Follow its guidance for text-heavy vs scanned vs slide-deck PDFs. |
| PowerPoint (`.pptx`) | Use the **pptx** skill to extract slide content **and** speaker notes. |
| Word (`.docx`) | Use the **docx** skill to extract body text, tables, headers, and footnotes. |
| Spreadsheet (`.xlsx`, `.csv`) | Use the **xlsx** skill or read CSV directly. |
| Markdown / plain text (`.md`, `.txt`) | Read directly. |
| Images (`.png`, `.jpg`) | Inspect visually; extract any text or diagram content (see §Visual content below). |

Ensure you capture **all** content including speaker notes, footnotes, and page-footer annotations — these often contain exam-critical details.

### 0c. Group files by topic

After reading, group the files by topic or chapter. Use filenames, slide titles, heading structure, and content overlap to determine grouping. A single topic may correspond to one file or span several files. If the grouping is ambiguous, ask the user for clarification before proceeding.

### 0d. Process topics — subagent strategy

When subagents are available (e.g. in Claude Code or Cowork), delegate each topic group to a separate subagent. Each subagent:

1. Receives only the files (or extracted content) for its assigned topic.
2. Follows the full pipeline below (Governing Constraints → Content Coverage → Output Structure → Quality Self-Check).
3. Saves its output as an intermediate Markdown file (e.g. `_draft_ch3.md`) in the working directory.

The **main agent** then:

1. Collects all intermediate Markdown drafts.
2. Runs the deduplication pass (see §Deduplication below).
3. Applies cross-chapter references.
4. Assembles and delivers the final output.

This approach conserves context for the main agent — it reads compact Markdown drafts rather than raw source material.

**When subagents are not available** (e.g. on claude.ai), process topics sequentially yourself. After finishing each topic, save the draft Markdown to disk before moving to the next, so that earlier source material can be released from context.

If the user specifies a particular chapter or lecture, process only that subset. If the user uploads an entire course without specifying, ask which chapters to cover or process them sequentially (the user can request the next chapter with `下一章`).

---

## Governing constraints

1. **The uploaded documents are the sole content source.** Do not introduce external textbook material, online resources, or your own supplementary knowledge.
2. **Exception — trivial derivations only.** You may include a conclusion that is logically immediate from document content (a simple rewrite, rearrangement, or direct corollary). Mark any such addition with `==整理==` or `==推得==` (see Source Annotation below).
3. **Lean toward inclusion.** When uncertain whether something is exam-relevant, include it and tag it `[可选记忆]`. Omitting a core result is worse than including a marginal one.

---

## Content coverage framework

Work through **every** layer below systematically across the entire document set. If a layer does not apply to this course, skip it.

### A. Foundational concepts

- **Definitions** — precise symbolic statement + name
- **Quantities** — every introduced symbol, its meaning, and units (if applicable)
- **Notation conventions** — sign rules, abbreviations, English terms with Chinese translations

### B. Theorems and laws

- **Theorems / Laws / Propositions** — full statement with **preconditions** and **conclusion (mathematical form)**; preserve names
- **Lemmas / Corollaries** — especially those reused across examples
- **Axioms / Postulates** — foundational assumptions

### C. Models and structures

- **Idealised models / Abstract structures** — physical simplifications or ADTs
- **State / Process equations** — mathematical relationships governing systems
- **Storage structures / Implementations** — sequential, linked, adjacency matrix, etc.

### D. Conditions and criteria

- **Applicability conditions** — when a formula / algorithm / law may be used
- **Necessary-and-sufficient conditions** — all equivalent forms, listed exhaustively
- **Discriminants / Tests** — existence, integrability, continuity, empty/full checks
- **One-way conditions** — sufficient-only or necessary-only implications

### E. Computation and algorithms

- **Formulas** — computational methods for each scenario
- **Key relations** — identities and equalities used repeatedly
- **Algorithm steps** — pseudocode or key steps, with input/output and scope
- **Code** — critical code fragments (preserve language tag, add concise comments)
- **Complexity** — best / worst / average time and space; stability; comparative efficiency
- **Inference / Transformation rules** — formal rules
- **Named constants / Standard values** — frequently needed numbers

### F. Processes and flows

- **Canonical processes** — characteristics and analysis of each type
- **Cycles / Iterations** — thermodynamic cycles, algorithmic iteration analysis
- **Diagrams** — P-V, T-S, flowcharts — interpretation and application

### G. Comparisons and distinctions

- **Concept contrasts** — differences and connections between similar concepts (prefer table form)
- **Counterexamples** — used in the document to show "the converse fails"
- **Intuitive meaning** — physical / geometric / visual interpretation of abstractions
- **Model / Structure comparisons** — applicability, pros, cons side by side

### H. Mandatory inclusions — do not skip these

1. Every bullet in any **summary / recap / learning objectives** slide — full coverage.
2. All content in **Notes / speaker notes**, including exercise answers and supplementary remarks.
3. Statements phrased as questions or caveats ("However…", "Note that…", "思考：") that are actually important conclusions — **reformulate as explicit propositions**.
4. **General results** derived inside worked examples (not just the numeric answer for that problem) — extract and state as standalone propositions.
5. **Key techniques** embedded in proofs — distil into memorable items.

---

## Visual content handling

Course slides are often diagram-heavy. Since the output is a Markdown document, represent visual content with best efforts using text-based methods:

### Representation strategies (in order of preference)

1. **Mermaid diagrams** — For flowcharts, sequence diagrams, state diagrams, class diagrams, and trees. Use fenced `mermaid` code blocks. This is the preferred method whenever the diagram has a clear graph/flow structure.

2. **ASCII / text diagrams** — For simple spatial layouts, circuit-like diagrams, or structures that Mermaid cannot express well. Use fenced code blocks to preserve spacing.

3. **Markdown tables** — For matrix-form visuals, truth tables, comparison grids, or any diagram that is fundamentally tabular.

4. **Structured textual description** — When none of the above can faithfully represent the visual, write a precise structured description that captures all the information the diagram conveys: nodes, edges, labels, directions, spatial relationships, and any annotations.

### Rules

- **Every diagram/figure that contains exam-relevant information must be represented.** Do not skip a diagram simply because it is hard to render in text.
- Tag each visual representation with `[图示]` (or `[diagram]` for English-medium courses) at the start so the reader knows the original was a visual element.
- Capture **all** labels, annotations, axis titles, data points, and legends from the original figure.
- For graphs/plots, describe the key relationships, trends, intercepts, and notable features rather than individual data points (unless the data points are exam-relevant).
- When a diagram is referenced by surrounding text or formulas, cross-reference clearly (e.g. "see diagram above" or "如上图所示").
- Include the source tag as usual: `<sup>source</sup>`.

---

## Deduplication

Courses frequently repeat definitions, formulas, and key results across multiple lectures (recap slides, review sessions, repeated references). To avoid bloat:

1. **Include each concept exactly once**, under the most relevant section or chapter where it is first introduced or most thoroughly treated.
2. **Merge source tags** — when the same item appears in multiple source files, list all sources in a single `<sup>` tag: e.g. `<sup>Lec3 p5; Lec7 p2; Review p1</sup>`.
3. **Do not repeat the same item across sections.** If a later chapter needs to reference an already-covered result, use a cross-chapter reference: "参见第 XX 章/讲" (or "See Ch. XX" for English-medium courses).
4. **Distinguish genuine repetition from refinement.** If a later lecture adds conditions, caveats, or extensions to an earlier result, include the refined version as an update or addendum under the original item — do not treat it as a duplicate.

---

## Output structure

Use this template:

```
# 第X章/第X讲 Title

## 关键考点总览

1. …
2. …
… (≤ 10 items)

---

## §X.1 Section name

### 一、Topic block

#### 1. 定义 / 术语
- …

#### 2. 定理 / 定律 / 命题
- …

#### 3. 充要条件 / 判定准则
- …

(further sub-categories as needed)

---

## §X.2 Section name
…
```

### Per-item formatting rules

- Each item fits in **1–3 lines**.
- Math: LaTeX — inline `$…$`, display `$$…$$`.
- Code: fenced code blocks with language tag.
- Quantities: **symbol + meaning + unit**.
- Multiple equivalent forms: numbered sub-list.
- Preserve key English terms alongside Chinese translations.
- Mark high-frequency / high-importance items with ⭐ at the start.

### Sub-categories within each topic block (use only those that have content)

1. 定义 / 术语
2. 定理 / 定律 / 命题
3. 推论 / 引理
4. 充要条件 / 等价条件 / 判定准则
5. 计算公式 / 算法 / 推理规则
6. 存储结构 / ADT 描述 / 模型方程
7. 复杂度分析
8. 易错点 / 概念辨析 / 反例
9. 重要例题结论 / 常用技巧

---

## Source annotation

Every item gets a superscript source tag at the end of the line so the reader can trace it back to the original material.

**Format:** `<sup>source description</sup>` immediately after the item text.

```
- ⭐ 热力学第一定律：$Q = \Delta E + A$，适用于任意热力学过程。<sup>§9.2 p11</sup>
- **栈（Stack）**：后进先出（LIFO）的线性表。<sup>第3章 p3</sup>
```

**Non-original content** — when an item is your reorganisation or logical derivation rather than verbatim from the document, prepend a highlight tag:

| Situation | Tag |
|---|---|
| Rewritten / reorganised (same meaning) | `==整理==<sup>source</sup>` |
| Logically derived but not stated in original | `==推得==<sup>依据：sourceA + sourceB</sup>` |

Never include content that has no traceable basis in the documents.

---

## Special-content templates

Use the appropriate template when the course material fits. Skip any that don't apply.

### Process / flow comparison table

```markdown
| 过程 | 特征 | 过程方程 | 量1 | 量2 | 量3 |
|---|---|---|---|---|---|
| A | … | … | … | … | … |
```

### Limit / existence criteria system

```markdown
1. 定义（ε-δ 语言）：…
2. 存在的充分/必要条件：…
3. 不存在的判定方法：…
4. 各类极限间的关系：…
5. 典型反例：…
```

### Equivalent-condition enumeration

```markdown
第 I 充要条件：…
第 II 充要条件：…
第 III 充要条件：…
```

### Computation method catalogue

```markdown
1. 方法 A（适用情形 + 公式）
2. 方法 B（适用情形 + 公式）
```

### Algorithm comparison table

```markdown
| 算法 | 时间复杂度（最好/最坏/平均） | 空间复杂度 | 稳定性 | 适用场景 |
|---|---|---|---|---|
```

### Structure / model comparison table

```markdown
| 特征 | 结构A | 结构B |
|---|---|---|
| 定义 | … | … |
| 适用范围 | … | … |
| 关键区别 | … | … |
```

### Proof / derivation extract

Do not copy full proofs. Distil as: "证明要点：利用……构造……，关键在于……"

### Algorithm steps extract

Capture: **input / output → key steps (pseudocode) → correctness conditions → complexity**.

### Cross-chapter references

When referencing a result already covered in a previous chapter's review sheet, write "参见第 XX 章/讲" instead of repeating.

---

## Priority rules

| Priority | Content type | Action |
|---|---|---|
| **Must** | Summary / recap / learning-objective bullets | Cover every one |
| **Must** | Named theorems / laws / principles / tests | Full statement |
| **Must** | Repeatedly-used formulas, conclusions, code patterns | Standalone item |
| **Must** | Sign conventions, applicability conditions, boundary conditions | Explicitly noted |
| **Must** | Notes / speaker-note content (including exercise answers) | Include directly |
| **Must** | Numbered equivalent conditions / inference rules / algorithm steps | List completely |
| **Must** | "However" / "Note that" caveats and counterexamples | Reformulate as propositions |
| **Must** | Diagrams / figures containing exam-relevant information | Represent with best-effort text rendering |
| **Should** | General conclusions from worked examples | Abstract into propositions |
| **Should** | Physical / geometric intuition, microscopic interpretation | Brief note |
| **Should** | Key proof techniques | Distil into memorable item |
| **Optional** | One-off details | Merge or tag `[可选记忆]` |
| **Optional** | Pure computation steps | Keep only key steps or techniques |

---

## Interaction protocol

| User input | Behaviour |
|---|---|
| Uploads documents + specifies chapter/lecture | Run the full pipeline; output the review sheet |
| Uploads documents without specifying scope | Ask which chapters/topics to cover, or process all sequentially |
| **下一章** or **下一讲** | Repeat the full pipeline for the next chapter |
| **补充 XXX** | Add the specified content to the existing sheet |
| **检查遗漏** | Re-read the source and cross-check for missed items |

---

## Formatting rules

1. **Language:** Match the source — Simplified Chinese for Chinese materials; preserve key English terms.
2. **Math:** LaTeX.
3. **Code:** Fenced blocks with language tag.
4. **Diagrams:** Mermaid fenced blocks, ASCII art in code blocks, or structured descriptions (see §Visual content handling).
5. **Heading hierarchy:** `#` → `##` → `###` → `####`, no skipping.
6. **Lists:** Ordered or unordered; blank line between items.
7. **Tables:** No blank line between header row and separator row; one blank line before and after table.

---

## Quality self-check

Before delivering, verify every item on this list:

- All named theorems / laws / principles / tests included
- All key formulas / algorithms / inference rules included
- All equivalent-condition sets listed completely
- Applicability conditions, sign rules, boundary conditions explicit
- General conclusions from examples extracted as propositions
- Summary / recap / learning-objective slides fully covered
- Notes / speaker-note content included
- Every item has a `<sup>` source tag
- Non-original items tagged with `==整理==` or `==推得==` + basis
- Symbols, meanings, and units annotated for all quantities
- Counterexamples included for concept distinctions
- Code has key-step comments
- Comparison tables compiled where applicable
- All exam-relevant diagrams and figures represented in text form with `[图示]` tag
- No duplicate items across sections — each concept appears exactly once with merged source tags
- Cross-chapter references used instead of repetition

---

## Delivery

Save the review sheet as a Markdown file named after the chapter (e.g. `第3章_复习资料.md`) and present it to the user.
