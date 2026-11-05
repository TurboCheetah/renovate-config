# renovate-config

Shared Renovate preset for TurboCheetah repos. Repos adopt it with:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>TurboCheetah/renovate-config"]
}
```

## What `default.json` does

- Groups all non-major updates into one PR per run (`group:allNonMajor`), so dependency PRs stop colliding with each other.
- Automerges minor, patch, pin, and digest bumps once checks pass.
- Refreshes every lockfile once a week (`lockFileMaintenance`), so transitive dependencies that no direct bump touches don't go stale. These PRs automerge once checks pass too, which means a repo's CI is what stands between a transitive major bump and the default branch. Repos without meaningful CI should set `"lockFileMaintenance": { "automerge": false }`.
- Major bumps never automerge — they always need a human.
- Waits 3 days after a release before picking it up (`minimumReleaseAge`), so brand-new releases get a chance to be yanked first.
- Runs on Sundays before noon, labels PRs `Meta: Dependencies`, pins GitHub Action digests, and enables vulnerability alerts.

## Per-repo overrides

Anything a repo needs on top (custom managers, extra grouping, stricter automerge) lives in that repo's own config file, which is applied after the preset. Later rules win.
