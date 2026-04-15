# Arietids Skills Rewrite Design

## Goal

Rewrite `arietids-skills` so the repository behaves like a clean extension of the current local skill base in `~/.agents/skills`, with source folders that are easy to maintain, packaged artifacts that are consistent, and local installation that keeps the repo and active skills in sync.

## Scope

In scope:
- Rewrite the three existing skills to match the conventions used by the current local and superpowers skill sets.
- Normalize repository layout and package locations.
- Add `agents/openai.yaml` metadata for each skill.
- Remove machine-specific path assumptions from skill content.
- Validate the rewritten skills.
- Install the repo versions into `~/.agents/skills` so they are active locally.
- Commit the resulting changes and push them to GitHub.

Out of scope:
- Creating brand new skills beyond the three already in the repo.
- Renaming skills unless a compatibility issue forces it.
- Building a separate publishing pipeline or GitHub Actions setup.

## Repository Architecture

The repository source of truth will be one root folder per skill:

- `course-review-generator/`
- `handwritten-pdf-transcription/`
- `transcription-content-review/`

Each skill folder should use the same baseline structure:

- `SKILL.md`
- `agents/openai.yaml`
- `references/` only when large prompt templates or detailed rubrics should be kept out of `SKILL.md`
- `scripts/` only when deterministic tooling is genuinely worth bundling

Distribution artifacts will live only under `packages/`:

- `packages/course-review-generator.skill`
- `packages/handwritten-pdf-transcription.skill`
- `packages/transcription-content-review.skill`

The root `README.md` should be kept short and should explain:
- which skills are included
- that root skill folders are the editable source
- that `packages/` contains packaged artifacts
- how local installation is done

## Skill Writing Conventions

All rewritten skills should align with the current skill base:

- YAML frontmatter contains only `name` and `description`.
- `description` is optimized for triggering conditions rather than long workflow summaries.
- `SKILL.md` focuses on workflow, guardrails, and non-obvious rules.
- Large templates, rubrics, or detailed reference material move into `references/` when doing so makes the main skill easier to scan.
- Hardcoded absolute paths such as `/Users/...` are removed.
- References to related capabilities use skill names and purposes rather than local filesystem paths where possible.

The existing skill names should remain stable:

- `course-review-generator`
- `handwritten-pdf-transcription`
- `transcription-content-review`

## Per-Skill Rewrite Intent

### handwritten-pdf-transcription

Keep this as a rigid workflow skill. Preserve the strong fidelity requirements, but reduce bulk in `SKILL.md` by moving any oversized transcription prompt template or detailed formatting rubric into `references/` if that improves readability.

### transcription-content-review

Keep this as a high-standard review skill. Rewrite it so the trigger description is tighter and the body is easier to scan, while preserving the requirement to produce both a corrected document and an audit-style review report.

### course-review-generator

This is the largest cleanup target. Separate the core workflow from detailed formatting policy so the skill remains comprehensive without forcing all detail into the main file. Preserve its course-material-only constraint and study-guide output goal.

## Local Installation Model

The repo should be installed into `~/.agents/skills` via symlinks, not copies.

Target links:

- `~/.agents/skills/course-review-generator -> <repo>/course-review-generator`
- `~/.agents/skills/handwritten-pdf-transcription -> <repo>/handwritten-pdf-transcription`
- `~/.agents/skills/transcription-content-review -> <repo>/transcription-content-review`

This keeps the editable repo and the active local skill set aligned with no copy drift.

## Validation And Packaging

After the rewrite:

1. Validate each skill folder with the local `skill-creator` validator.
2. Check that each skill has valid frontmatter, `agents/openai.yaml`, and no broken machine-specific path assumptions.
3. Build or rebuild the three `.skill` artifacts into `packages/`.
4. Verify that all expected package files exist in the normalized location.

## Verification

Completion evidence should include:

- successful validation output for all three rewritten skills
- confirmation that the normalized package artifacts exist
- confirmation that the local symlinks in `~/.agents/skills` point to the repo folders
- git status showing the intended repository changes before commit

## Git Completion

Once the rewrite, validation, packaging, and local installation are complete:

1. Review the final diff.
2. Commit the repository changes with a message that reflects the rewrite and normalization.
3. Push the branch to GitHub so the remote repository matches the local source of truth.

## Risks And Guardrails

- Do not rewrite so aggressively that trigger wording or core behavior drifts unintentionally.
- Do not keep stale package artifacts outside `packages/`.
- Do not leave absolute local paths in committed skill files.
- Prefer the smallest rewrite that still makes the repo feel native to the current skill ecosystem.
