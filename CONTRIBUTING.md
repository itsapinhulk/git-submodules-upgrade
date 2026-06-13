# Contributing

The upgrade logic lives in [`itsapinhulk/shellutils`](https://github.com/itsapinhulk/shellutils)
(`bash/upgrade-git-submodules`). It is **vendored** into this repository at
`scripts/upgrade-git-submodules`, pinned to a specific upstream commit recorded
in the file's header, so a released `@vN` tag always contains the exact script
it runs.

| Task | Command |
|------|---------|
| Vendor a specific commit | `scripts/vendor-upgrade-script <sha>` |
| Vendor the tip of a branch | `scripts/vendor-upgrade-script --branch main` |
| Check the vendored copy is in sync | `test/check-vendored-script` |

CI enforces this:

- **Verify** runs `check-vendored-script` on every push and pull request, and
  the release/tag path runs it before tagging — a drifted or hand-edited copy
  fails the build.
- **Update Vendored Script** runs on a schedule (and on demand), re-vendors from
  `shellutils` `main`, and opens a PR when the script changed.
