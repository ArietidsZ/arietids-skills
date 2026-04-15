# Transcription Review Pipeline Design

## Goal

Update the handwritten PDF workflow so transcription automatically flows into transcription review, produces a single final Markdown file named after the source PDF, and removes all temporary intermediate artifacts after a successful review.

## Scope

In scope:
- Make `handwritten-pdf-transcription` explicitly orchestrate the review stage after raw transcription completes.
- Update `transcription-content-review` so it clearly supports being the second stage of that pipeline while remaining usable as a standalone review skill.
- Define the final naming, cleanup, and failure behavior for the combined workflow.
- Update the current branch, rebuild packages, and create a PR after verification.

Out of scope:
- Creating a brand new combined skill.
- Adding a separate runtime script or external automation layer.
- Changing the standalone review skill into a transcription-only helper.

## Architecture

The pipeline should stay split across the existing two skills, but the responsibility boundary becomes explicit:

- `handwritten-pdf-transcription` is the pipeline orchestrator.
- `transcription-content-review` is the second-stage reviewer that can also still be invoked on its own.

For a source file such as `notes.pdf`, the workflow becomes:

1. Create the temporary workspace `notes/` next to the PDF.
2. Produce raw page-level and merged transcription artifacts inside `notes/`.
3. Automatically invoke the review stage against that transcription output.
4. Produce a cleaned final Markdown document.
5. Rename or promote the final reviewed output to `notes.md` next to `notes.pdf`.
6. Delete the temporary workspace `notes/` and all generated intermediate files.
7. Report only the final `notes.md` path.

## Responsibility Split

### handwritten-pdf-transcription

This skill owns:

- creation of the temporary working folder
- raw transcription generation
- automatic handoff into review
- final output naming
- cleanup of intermediate artifacts
- final user-facing delivery path

This skill should no longer present `transcription.md` as the terminal output. A successful run ends only after review has completed and the final renamed Markdown file exists.

### transcription-content-review

This skill remains standalone, but it must also explicitly support a pipeline-stage mode where:

- the input is the raw transcription workspace from handwritten transcription
- the reviewed output is produced for handoff back to the transcription skill
- the transcription skill, not the review skill, owns the final rename and cleanup policy

Standalone review behavior should remain intact when the user invokes the review skill directly on an existing Markdown file.

## File And Behavior Changes

Files expected to change:

- `handwritten-pdf-transcription/SKILL.md`
- `handwritten-pdf-transcription/agents/openai.yaml`
- `handwritten-pdf-transcription/references/transcription-worker-prompt.md` only if worker-stage instructions need to mention the automatic review handoff or cleanup-sensitive conventions
- `transcription-content-review/SKILL.md`
- `transcription-content-review/agents/openai.yaml`

Required behavior updates:

### handwritten-pdf-transcription changes

- Replace the current completion behavior of “deliver `transcription.md`”.
- Require automatic invocation of the review stage immediately after transcription is assembled.
- Require the final reviewed output to be renamed to `<pdf-basename>.md` next to the original PDF.
- Require cleanup only after the review stage succeeds.
- Deliver only the final renamed Markdown path to the user.

### transcription-content-review changes

- Add explicit instructions for reviewing the freshly generated transcription workspace from the transcription skill.
- Clarify that in pipeline mode it writes a reviewed output for promotion into the final named file.
- Preserve standalone review behavior for direct invocations on existing Markdown files.

## Final Naming And Cleanup Rules

For `lecture.pdf`, the final successful artifact must be `lecture.md`.

After a successful review, remove all temporary artifacts from the working folder, including:

- `pages/`
- `parts/`
- `zoomed/`
- `transcription.md`
- `transcription_reviewed.md`
- `review_report.md`
- the temporary working folder itself once empty

The success state is exactly one final reviewed Markdown file next to the original PDF.

## Failure Behavior

- If transcription fails before review begins, the temporary workspace remains for inspection.
- If review fails after transcription succeeds, the temporary workspace remains and cleanup does not run.
- If review resolves most issues but some uncertainty remains, the final Markdown may still contain the visible review markers already allowed by the review skill, but cleanup still runs after successful completion.

## Standalone Review Compatibility

Direct use of `transcription-content-review` should remain supported:

- When reviewing a standalone Markdown file directly, it continues to act like a review skill.
- The automatic rename to `<pdf-name>.md` and deletion of the transcription workspace apply only when it is acting as the second stage of handwritten PDF transcription.

## Verification

Implementation is only complete when all of the following are verified:

- `handwritten-pdf-transcription` and `transcription-content-review` both validate successfully.
- Their rewritten text clearly describes:
  - automatic review handoff after transcription
  - final rename to source basename plus `.md`
  - deletion of all temporary artifacts after successful review
  - preservation of the temporary workspace when review fails
- Package archives are rebuilt and include the updated skill contents.
- The branch is pushed before PR creation.

## Risks And Guardrails

- Do not blur the boundary so much that the standalone review skill becomes unusable.
- Do not delete the temporary workspace on failure.
- Do not leave the final success state ambiguous; one final Markdown file should remain.
- Do not add a new combined skill when the existing two-skill pipeline can express the workflow clearly.
