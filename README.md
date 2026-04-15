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

From the repository root in a POSIX shell:

```sh
repo_root="$(pwd)"
mkdir -p "$HOME/.agents/skills"
rm -rf "$HOME/.agents/skills/course-review-generator"
rm -rf "$HOME/.agents/skills/handwritten-pdf-transcription"
rm -rf "$HOME/.agents/skills/transcription-content-review"
ln -s "$repo_root/course-review-generator" "$HOME/.agents/skills/course-review-generator"
ln -s "$repo_root/handwritten-pdf-transcription" "$HOME/.agents/skills/handwritten-pdf-transcription"
ln -s "$repo_root/transcription-content-review" "$HOME/.agents/skills/transcription-content-review"
```

## Rebuild packages

From the repository root in a POSIX shell:

```sh
mkdir -p "packages"
rm -f "packages/course-review-generator.skill"
rm -f "packages/handwritten-pdf-transcription.skill"
rm -f "packages/transcription-content-review.skill"
zip -rq "packages/course-review-generator.skill" "course-review-generator"
zip -rq "packages/handwritten-pdf-transcription.skill" "handwritten-pdf-transcription"
zip -rq "packages/transcription-content-review.skill" "transcription-content-review"
```
