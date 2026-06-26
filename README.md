# trelix-index-action

Index any repository with [trelix](https://github.com/sairam0424/trelix) for AI-powered code search. Caches the index between runs keyed to the commit SHA, so subsequent jobs on the same commit skip the indexing step entirely.

## Install

Add the action to any workflow step:

```yaml
- uses: sairam0424/trelix-index-action@v1
```

No extra setup required. Python 3.11 is bootstrapped automatically.

## Usage

### Basic — index the current repo with the default local provider

```yaml
jobs:
  index:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Index with trelix
        id: trelix
        uses: sairam0424/trelix-index-action@v1

      - name: Show index path
        run: echo "Index written to ${{ steps.trelix.outputs.index-path }}"
```

### Advanced — index a subdirectory with the OpenAI provider

```yaml
jobs:
  index:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Index with trelix (OpenAI embeddings)
        id: trelix
        uses: sairam0424/trelix-index-action@v1
        with:
          repo-path: services/api
          provider: openai
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}

      - name: Upload index artifact
        uses: actions/upload-artifact@v4
        with:
          name: trelix-index
          path: ${{ steps.trelix.outputs.index-path }}
```

## Inputs

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `repo-path` | Path to the repository root to index (relative to `$GITHUB_WORKSPACE`) | `.` | No |
| `provider` | Embedding provider: `local`, `openai`, `azure`, or `voyage` | `local` | No |

## Outputs

| Output | Description |
|--------|-------------|
| `index-path` | Absolute path to the generated `.trelix/index.db` file |

## Caching

The action caches `.trelix/` keyed to `github.sha`. Re-runs on the same commit restore the cache and skip re-indexing, keeping CI fast.

## License

MIT
