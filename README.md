# gh-projects-actions

GitHub Actions that automates iteration rollover for GitHub Projects using the [gh-projects CLI tool](https://github.com/kriscoleman/gh-projects).

## Usage

### Basic Usage

```yaml
name: 'Weekly Iteration Rollover'

on:
  schedule:
    # Run every Monday at 9 AM UTC
    - cron: '0 9 * * 1'

jobs:
  rollover:
    runs-on: ubuntu-latest
    steps:
      - name: Run Iteration Rollover
        uses: kriscoleman/gh-projects-action@v1
        with:
          project_url: 'https://github.com/users/YOUR_USERNAME/projects/YOUR_PROJECT_NUMBER'
```

### Advanced Usage

```yaml
name: 'Custom Iteration Rollover'

on:
  schedule:
    # Run every other Monday at 10 AM UTC
    - cron: '0 10 */14 * 1'
  workflow_dispatch:
    inputs:
      project_url:
        description: 'GitHub Project URL'
        required: true

jobs:
  rollover:
    runs-on: ubuntu-latest
    steps:
      - name: Run Iteration Rollover
        uses: kriscoleman/gh-projects-action@v1
        with:
          project_url: ${{ github.event.inputs.project_url || 'https://github.com/users/YOUR_USERNAME/projects/YOUR_PROJECT_NUMBER' }}
          gh_projects_version: 'latest'
          github_token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `project_url` | GitHub Project URL | Yes | - |
| `gh_projects_version` | Version of gh-projects to install | No | `v0.0.0` |
| `github_token` | GitHub token for authentication | No | `${{ github.token }}` |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Whether the iteration rollover completed successfully |

## Cron Schedule Examples

- **Weekly (Monday 9 AM UTC)**: `0 9 * * 1`
- **Bi-weekly (every other Monday)**: `0 9 */14 * 1`  
- **Monthly (1st of month)**: `0 9 1 * *`
- **Daily (9 AM UTC)**: `0 9 * * *`

## Requirements

- Repository must have access to a GitHub Project
- GitHub token must have appropriate permissions for the project
- Project must have iterations configured

## How it Works

1. Sets up Go environment
2. Installs specified version of gh-projects CLI
3. Authenticates with GitHub using provided token
4. Executes `gh-projects iteration rollover --project <url> --silent`
5. Reports success or failure
