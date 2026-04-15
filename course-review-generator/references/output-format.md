# Course Review Output Format

Use this reference when assembling the final review guide.

Adapt headings, labels, and examples to the source or course language. Do not force English section names when the materials are in another language unless the user asks for translation.

## Output skeleton

```markdown
# Review Title

## Key Takeaways / Key Points

1. Define the core ideas before memorizing formulas.
2. Keep every formula attached to its conditions of use.

---

## Topic 1 / Chapter 1 / Lecture 1

### Definitions and Terms
- **Uniform continuity**: continuity with one delta that works for every point in the domain.<sup>Lec 3 p12</sup>

### Theorems and Laws
- **Mean Value Theorem**: if `f` is continuous on `[a,b]` and differentiable on `(a,b)`, then some `c` in `(a,b)` satisfies $f'(c)=\frac{f(b)-f(a)}{b-a}$.<sup>Lec 4 p7</sup>

### Conditions and Criteria
- Use the theorem only when both continuity on the closed interval and differentiability on the open interval are stated.<sup>Lec 4 p8</sup>

### Formulas and Algorithms
- Newton iteration: $x_{n+1}=x_n-\frac{f(x_n)}{f'(x_n)}$.<sup>Lec 6 p5</sup>

```python
def push(stack, value):
    stack.append(value)
```

Brief note on when this pattern is used.<sup>Lec 8 p4</sup>

### Comparisons and Pitfalls
- Continuity does not imply differentiability; the converse fails at sharp corners.<sup>Lec 4 p10</sup>

### Visuals and Diagrams
- [diagram] Function-growth sketch with axes, curve labels, and shaded feasible region preserved in text form.<sup>Lec 5 p9</sup>
```

## Per-item rules

- Keep each bullet to one to three lines.
- Put source tags at the end of the line as `<sup>Lec 3 p12</sup>`.
- Use `==整理==<sup>Lec 5 p3</sup>` for reorganized source content.
- Use `==推得==<sup>依据：Lec 3 p12 + Lec 4 p8</sup>` only for direct derivations that are clearly supported by the material.
- Keep the final headings and item wording in the source or course language unless the user requests translation.
- When later material repeats an earlier entry, point back to the original item instead of duplicating it, and fold refinements or addenda into the original entry.
- For diagram-derived content, make the visual origin explicit with a marker such as `[diagram]` or `[图示]`.
- Do not flatten an exam-relevant figure into a vague summary; preserve labels, annotations, axes, legends, and spatial relationships when present.
- Keep exam-relevant code as concise fenced code blocks with the correct language tag instead of flattening it into prose.
- Turn caveat-style notes such as `However`, `Note that`, and `思考` into explicit propositions or exam reminders when they convey testable content.

## Coverage checklist

- Definitions, notation, and units
- Theorems, laws, propositions, and corollaries
- Equivalent-condition sets and necessary or sufficient condition families, listed completely when present
- Applicability, boundary, sign, and edge-case conditions
- Formulas, algorithm steps, and inference-rule sets, listed completely when present, plus complexity or stability notes
- Comparisons, counterexamples, and common confusions
- Diagram information that matters for the exam
- Uncertain but plausibly testable items marked as `[可选记忆]`

## Diagram handling order

1. Mermaid when the structure is naturally a graph or flow.
2. ASCII diagrams when spatial layout matters and Mermaid is awkward.
3. Markdown tables when the visual is mostly tabular.
4. Structured text description when the figure cannot be represented well any other way.

When representing a figure, retain all exam-relevant labels, annotations, axes, legends, and directional or spatial relationships that appear in the source.
