# github-actions

Reusable workflows and composite actions shared across my repos.

Callers own the triggers (`schedule`, `push`, ...); these workflows only define `workflow_call`.
Pin by commit SHA:

```yaml
jobs:
  govulncheck:
    uses: oskarrn93/github-actions/.github/workflows/govulncheck.yml@<sha> # v1
    with:
      packages: ./internal/... ./cmd/...
```

| Workflow | Inputs |
|---|---|
| `govulncheck.yml` | `packages` (`./...`), `version` |
| `go-test.yml` | `packages` (`./...`), `race` (false), `gofmt` (true) |
| `actionlint.yml` | none |
| `terraform.yml` | `working-directory` (`.`), `version` (root module's `.terraform-version`, else latest) |
| `semver-release.yml` | `dry-run` (false), `move-major-tag` (true), `client-id` (none), secret `app-private-key` (optional) |

`semver-release.yml` infers the next `vMAJOR.MINOR.PATCH` from conventional commits since the latest tag (`!`/`BREAKING CHANGE` → major, `feat` → minor, `fix`/`perf` → patch, anything else → no release), creates the GitHub release, and moves `vMAJOR`. The caller's job needs `permissions: contents: write`. Releases created with `GITHUB_TOKEN` don't trigger other workflows (e.g. a build on tag push). Set `create-release: false` to only push the tag when the caller's tag-triggered workflow publishes the release itself (e.g. after building assets). Pass a GitHub App's `client-id` and `app-private-key` to create the release with an App token, which does.

Releases: run the `Release` workflow (`release.yml`, manual, dry run by default) to cut one from conventional commits, or publish a GitHub release tagged `vMAJOR.MINOR.PATCH`; `major-tag.yml` then moves the floating `vMAJOR` tag to that commit.

Composite actions go in `actions/<name>/action.yml`, used as `oskarrn93/github-actions/actions/<name>@<sha>`.
