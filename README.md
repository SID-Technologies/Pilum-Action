# Pilum Deploy Action

Build and deploy services with [Pilum](https://github.com/SID-Technologies/Pilum) — the cloud-agnostic deployment CLI.

## Usage

### Deploy on release

```yaml
name: Deploy
on:
  release:
    types: [published]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: SID-Technologies/pilum-action@v1
        with:
          tag: ${{ github.event.release.tag_name }}
        env:
          # Add credentials for your provider
          GOOGLE_APPLICATION_CREDENTIALS: ${{ secrets.GCP_SA_KEY }}
```

### Deploy specific services

```yaml
- uses: SID-Technologies/pilum-action@v1
  with:
    command: deploy
    tag: v1.0.0
    services: api-gateway worker-service
```

### Build and push only (no deploy)

```yaml
- uses: SID-Technologies/pilum-action@v1
  with:
    command: publish
    tag: ${{ github.sha }}
```

### Validate configs in PR

```yaml
name: Validate
on: [pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: SID-Technologies/pilum-action@v1
        with:
          command: check
```

### Dry-run preview

```yaml
- uses: SID-Technologies/pilum-action@v1
  with:
    command: dry-run
    tag: ${{ github.event.release.tag_name }}
```

### Publish npm package

```yaml
- uses: SID-Technologies/pilum-action@v1
  with:
    command: deploy
    tag: ${{ github.event.release.tag_name }}
  env:
    NODE_AUTH_TOKEN: ${{ secrets.NODE_AUTH_TOKEN }}
```

### Deploy from a subdirectory

```yaml
- uses: SID-Technologies/pilum-action@v1
  with:
    command: deploy
    tag: v1.0.0
    working-directory: ./services
```

### Pin a specific Pilum version

```yaml
- uses: SID-Technologies/pilum-action@v1
  with:
    version: v0.3.1
    command: deploy
    tag: v1.0.0
```

### Advanced flags

```yaml
- uses: SID-Technologies/pilum-action@v1
  with:
    command: deploy
    tag: v1.0.0
    flags: --only-tags=deploy --debug --no-deps
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `version` | No | `latest` | Pilum version to install |
| `command` | No | `deploy` | Pilum command (`deploy`, `build`, `publish`, `push`, `check`, `dry-run`, `list`) |
| `tag` | No | | Version tag passed to `--tag` |
| `services` | No | | Space-separated list of services to target |
| `working-directory` | No | `.` | Directory to run Pilum from |
| `flags` | No | | Additional CLI flags |

## Environment Variables

Pass cloud provider credentials and tokens as environment variables:

```yaml
env:
  # GCP
  GOOGLE_APPLICATION_CREDENTIALS: ${{ secrets.GCP_SA_KEY }}

  # AWS
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

  # npm / GitHub Packages
  NODE_AUTH_TOKEN: ${{ secrets.NODE_AUTH_TOKEN }}

  # Homebrew
  GH_TOKEN: ${{ secrets.GH_TOKEN }}
```

## Platform Support

The action automatically detects the runner's OS and architecture:

| Runner | OS | Arch |
|--------|-----|------|
| `ubuntu-latest` | linux | amd64 |
| `ubuntu-24.04-arm` | linux | arm64 |
| `macos-latest` | darwin | arm64 |
| `macos-13` | darwin | amd64 |

## License

BSL 1.1 — See [LICENSE](LICENSE).
