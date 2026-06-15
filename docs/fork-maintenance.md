# Fork maintenance

This fork installs on comma devices from the `comma3x` branch. Keep `comma3x`
stable and only promote it after `bp-dev` has been reviewed and tested.

For the feature inventory that should be preserved during upstream syncs, see
[`blue-dragon-feature-set.md`](blue-dragon-feature-set.md).

## Branch roles

- `upstream/bp-dev`: BluePilot upstream from `BluePilotDev/bluepilot`.
- `origin/bp-dev`: this fork's integration branch.
- `origin/comma3x`: comma3x install branch.

## Update flow

The `Sync BluePilot upstream` workflow runs weekly and can also be started
manually. It merges `BluePilotDev/bluepilot:bp-dev` into this fork's `bp-dev`
and opens or updates a pull request.

After that PR is merged and tested, run the `Promote comma3x install branch`
workflow manually. It fast-forwards `comma3x` to `bp-dev`.

## Local setup

Use these remotes locally:

```bash
git remote add origin https://github.com/crogers2287/bluedragon.git
git remote add upstream https://github.com/BluePilotDev/bluepilot.git
git config rerere.enabled true
```

If the remotes already exist:

```bash
git remote set-url origin https://github.com/crogers2287/bluedragon.git
git remote set-url upstream https://github.com/BluePilotDev/bluepilot.git
git branch --set-upstream-to=origin/bp-dev bp-dev
```
