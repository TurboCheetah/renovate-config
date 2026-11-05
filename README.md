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
- Refreshes every lockfile once a week (`lockFileMaintenance`), so transitive dependencies that no direct bump touches don't go stale. These PRs automerge once checks pass.
  - **No release-age wait.** Renovate hands lockfile regeneration to the package manager, which picks the newest version of every indirect dependency, so `minimumReleaseAge` [does not apply](https://docs.renovatebot.com/key-concepts/minimum-release-age/). An indirect release published hours before the Sunday run can automerge, and passing CI won't catch a malicious one.
  - **CI is the only gate.** These PRs can include indirect major bumps, so a repo's checks are what stand between them and the default branch.
  - Repos that want a human on these PRs, or that lack meaningful CI, should set `"lockFileMaintenance": { "automerge": false }`.
- Direct major bumps never automerge — they always need a human. Lock file maintenance (above) is the exception: it can carry indirect major bumps and still automerges.
- Waits 3 days after a release before picking it up (`minimumReleaseAge`), so brand-new releases get a chance to be yanked first. This does not cover lock file maintenance (see above).
- Runs on Sundays before noon, labels PRs `Meta: Dependencies`, pins GitHub Action digests, and enables vulnerability alerts.

## Per-repo overrides

Anything a repo needs on top (custom managers, extra grouping, stricter automerge) lives in that repo's own config file, which is applied after the preset. Later rules win.
