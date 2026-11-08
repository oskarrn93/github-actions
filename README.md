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

Composite actions go in `actions/<name>/action.yml`, used as `oskarrn93/github-actions/actions/<name>@<sha>`.
