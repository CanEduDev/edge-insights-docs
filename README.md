# Edge Insights Documentation

Live site: <https://docs.cededgeinsights.com>

This repository is the single source of truth for Edge Insights user documentation.

## Development

Prerequisite: [uv](https://docs.astral.sh/uv/).

Install dependencies:

```bash
uv sync
```

Live preview at <http://localhost:8000>:

```bash
uv run mkdocs serve
```

Build the site (CI runs the same command):

```bash
uv run mkdocs build --strict
```

Deployment happens automatically via GitHub Actions on every push to `main`.
