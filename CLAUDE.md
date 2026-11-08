# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Reusable GitHub Actions workflows (`.github/workflows/*.yml`) shared across the owner's other repos. There is no build or test tooling here; `ci.yml` runs actionlint on PRs and pushes to main, and behavior is otherwise validated by running the workflows from a caller repo. Reusable workflows only define `workflow_call` — callers own the triggers (`schedule`, `push`, ...). `ci.yml` and `release.yml` are this repo's own, non-reusable workflows. Composite actions, if added, go in `actions/<name>/action.yml`.

The README has a table of each workflow's inputs and defaults; update it when adding or changing inputs.

## Conventions

- Third-party actions are pinned by full commit SHA with a trailing `# vN` comment (Dependabot updates them weekly via `.github/dependabot.yml`).
- Workflows set `permissions: contents: read` at the top level.
- Go version comes from the caller's `go.mod` (`go-version-file: go.mod`), so callers must have one at the repo root.
- Inputs are passed to `run:` steps through `env:` (not interpolated with `${{ }}` into the script) to avoid shell injection. Package-pattern inputs are intentionally left unquoted in the shell so multiple space-separated patterns expand into separate args.
- `release.yml` is not reusable: on a published release (`vX.Y.Z`) it force-moves the floating `vX` tag to the release commit.
- Callers are expected to pin these workflows by commit SHA (`@<sha> # v1`).
