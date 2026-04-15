# setup-hot

GitHub Action to install the [Hot](https://hot.dev) programming language CLI.

Use this action to set up Hot in your GitHub Actions workflows for deploying, testing, or running Hot projects.

## Usage

### Basic deploy

```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: hotdev/setup-hot@v1
      - run: hot deploy
        env:
          HOT_API_KEY: ${{ secrets.HOT_API_KEY }}
```

### Pin a specific version

```yaml
      - uses: hotdev/setup-hot@v1
        with:
          version: '1.2.3'
```

### Pass the API key through the action

```yaml
      - uses: hotdev/setup-hot@v1
        with:
          api-key: ${{ secrets.HOT_API_KEY }}
      - run: hot deploy
```

### Run tests before deploying

```yaml
name: Test and Deploy
on:
  push:
    branches: [main]

jobs:
  test-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: hotdev/setup-hot@v1
      - run: hot check
      - run: hot test
      - run: hot deploy
        env:
          HOT_API_KEY: ${{ secrets.HOT_API_KEY }}
```

### Deploy on release

```yaml
name: Deploy on Release
on:
  release:
    types: [published]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: hotdev/setup-hot@v1
      - run: hot deploy
        env:
          HOT_API_KEY: ${{ secrets.HOT_API_KEY }}
```

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `version` | Hot version to install (e.g. `1.2.3`). | No | `latest` |
| `api-key` | Hot API key for deployment. Alternative to setting `HOT_API_KEY` env var. | No | |

## How it works

1. Detects the runner OS and architecture
2. Downloads the appropriate Hot installer (`.pkg` for macOS, `.deb` for Linux)
3. Installs Hot system-wide
4. Optionally sets `HOT_API_KEY` in the environment
5. Verifies the installation succeeded

## Prerequisites

- **Runner OS:** `ubuntu-latest` or `macos-latest` (Linux and macOS are supported)
- **Hot project:** Your repository should contain a `hot.hot` configuration file
- **API key:** Set `HOT_API_KEY` as a [repository secret](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions) for deployment

### Setting up your API key

1. Get your API key from [hot.dev](https://hot.dev)
2. Go to your GitHub repo → **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `HOT_API_KEY`, Value: your API key

Your `hot.hot` config file reads this automatically:

```
hot.remote.hot-dev.key ::env/get("HOT_API_KEY", "")
```

## Supported platforms

| Runner | OS | Architecture |
|--------|-----|-------------|
| `ubuntu-latest` | Linux | x86_64 |
| `ubuntu-24.04-arm` | Linux | arm64 |
| `macos-latest` | macOS | arm64 |
| `macos-13` | macOS | x86_64 |

## License

Apache 2.0 — see [LICENSE](LICENSE).
