# git-submodules-upgrade

A GitHub Action that upgrades all git submodules to their latest remote commits and optionally opens a pull request with the changes.

## Usage

```yaml
- uses: itsapinhulk/git-submodules-upgrade@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Example workflow

```yaml
name: Upgrade Submodules

on:
  schedule:
    - cron: '0 6 * * 1'  # Every Monday at 6am
  workflow_dispatch:

jobs:
  upgrade:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
          token: ${{ secrets.GITHUB_TOKEN }}

      - uses: itsapinhulk/git-submodules-upgrade@v1
        with:
          create-pr: 'true'
          reviewer: octocat
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `target` | Path to the repository root containing submodules | No | `.` |
| `create-pr` | Set to `false` to skip pull request creation | No | `true` |
| `include` | Newline- or space-separated list of submodule paths to upgrade. When set, only these submodules are updated (takes priority over `skip`) | No | `` |
| `skip` | Newline- or space-separated list of submodule paths to skip. Ignored when `include` is set | No | `` |
| `pr-branch` | Branch name used for the upgrade PR | No | `auto/upgrade-submodules` |
| `pr-title` | Title of the pull request | No | `chore: update git submodules` |
| `pr-commit-message` | Commit message for the submodule update commit | No | `chore: upgrade git submodules` |
| `reviewer` | GitHub username to request a review from | No | `` |

## Behavior

1. Upgrades all submodules (or a filtered subset) to their latest remote commit.
2. Checks whether any submodule pointers changed.
3. If changes exist and `create-pr` is `true`:
   - Creates (or force-pushes to) the `pr-branch` branch.
   - Opens a pull request against the current branch, or updates the existing PR if one is already open.
   - Optionally requests a reviewer.

If `create-pr` is `false` and there are changes, the working tree is left dirty for the caller to handle.

## Filtering submodules

Use `include` to update only specific submodules:

```yaml
- uses: itsapinhulk/git-submodules-upgrade@v1
  with:
    include: |
      ext/foo
      ext/bar
```

Use `skip` to exclude specific submodules:

```yaml
- uses: itsapinhulk/git-submodules-upgrade@v1
  with:
    skip: ext/vendor
```

`include` takes priority over `skip` when both are set.

## Permissions

The action uses `github.token` for PR creation. Ensure your workflow has write permissions:

```yaml
permissions:
  contents: write
  pull-requests: write
```
