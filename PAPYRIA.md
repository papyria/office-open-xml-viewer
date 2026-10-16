# Papyria fixes

Papyria builds and deploys this fork's `papyria` branch: `main` with every
open Papyria fix branch merged in.

- `main` mirrors upstream `main`.
- Each fix is a branch on `main` (`fix/<topic>`) and an upstream pull request.
- `papyria-release` holds this file and `.github/workflows/papyria-release.yml`.
  It is merged into `papyria` but never proposed upstream.

## Adding a fix

Branch from `main`, fix it test-first, push, and open the upstream pull request.
Then rebuild `papyria`.

## Refreshing stale pull requests

```bash
git fetch upstream
git push origin upstream/main:main
git rebase origin/main fix/<topic>            # for each open fix branch
git push --force-with-lease origin fix/<topic>
```

Drop a fix branch once upstream has merged it.

## Rebuilding `papyria`

```bash
git checkout -B papyria origin/main
for branch in papyria-release fix/<topic> ...; do   # every open fix branch
  git merge --no-ff --no-edit "${branch}"
done
git push --force-with-lease origin papyria
```

Each push to `papyria` runs the `Papyria release` workflow: it builds, tests
and packs the branch head, and attaches `silurus-ooxml-<version>-papyria.<commit>.tgz`
to a GitHub release named `papyria-<commit>`. Papyria depends on that
tarball's URL, and its lockfile pins the tarball's integrity hash. Point
Papyria at the new release after every rebuild.
