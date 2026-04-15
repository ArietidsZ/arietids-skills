# Arietids Skills

Custom skills that extend the local `~/.agents/skills` skill base.

## Included skills

- `course-review-generator/` - build exam-focused review guides from source course materials
- `handwritten-pdf-transcription/` - transcribe handwritten PDFs into high-fidelity Markdown
- `transcription-content-review/` - review a transcription and produce a corrected standalone document

## Repository layout

- Root skill folders are the editable source of truth.
- `packages/` contains packaged `.skill` archives built from those folders.
- Each skill folder includes `SKILL.md` and `agents/openai.yaml`.

## Local install

From the repository root:

```bash
repo_root="$(pwd)"
mkdir -p "$HOME/.agents/skills"
ln -sfn "$repo_root/course-review-generator" "$HOME/.agents/skills/course-review-generator"
ln -sfn "$repo_root/handwritten-pdf-transcription" "$HOME/.agents/skills/handwritten-pdf-transcription"
ln -sfn "$repo_root/transcription-content-review" "$HOME/.agents/skills/transcription-content-review"
```

## Rebuild packages

From the repository root:

```bash
rm -f "packages/"*.skill
zip -rq "packages/course-review-generator.skill" "course-review-generator"
zip -rq "packages/handwritten-pdf-transcription.skill" "handwritten-pdf-transcription"
zip -rq "packages/transcription-content-review.skill" "transcription-content-review"
```
