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

### Comparisons and Pitfalls
- Continuity does not imply differentiability; the converse fails at sharp corners.<sup>Lec 4 p10</sup>
```

## Per-item rules

- Keep each bullet to one to three lines.
- Put source tags at the end of the line as `<sup>Lec 3 p12</sup>`.
- Use `==整理==<sup>Lec 5 p3</sup>` for reorganized source content.
- Use `==推得==<sup>依据：Lec 3 p12 + Lec 4 p8</sup>` only for direct derivations that are clearly supported by the material.
- Keep the final headings and item wording in the source or course language unless the user requests translation.

## Coverage checklist

- Definitions, notation, and units
- Theorems, laws, propositions, and corollaries
- Conditions, criteria, and edge cases
- Formulas, algorithms, and complexity notes
- Comparisons, counterexamples, and common confusions
- Diagram information that matters for the exam

## Diagram handling order

1. Mermaid when the structure is naturally a graph or flow.
2. ASCII diagrams when spatial layout matters and Mermaid is awkward.
3. Markdown tables when the visual is mostly tabular.
4. Structured text description when the figure cannot be represented well any other way.
