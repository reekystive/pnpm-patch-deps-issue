# pnpm Patch Dependencies Issue

This repo is a minimal repro for [pnpm/pnpm#9684](https://github.com/pnpm/pnpm/issues/9684).

> **Update**: According to pnpm documentation, patching should not be used to modify the package.json file of dependencies. For overriding dependencies, use [overrides](https://pnpm.io/package_json#pnpmoverrides) or a [package hook](https://pnpm.io/hooks) instead. The behavior described below is expected, not a bug.

## Problem

After patching `vite@6.3.5` and deleting **all** of its dependencies, those deleted packages are still kept in `pnpm-lock.yaml` and inside `node_modules/` after a fresh install.

## Quick Repro

```bash
# install deps (already installed in the repo)
pnpm install vite@6.3.5

# patch package and remove all dependencies (already prepared in patches/vite@6.3.5.patch)
pnpm patch vite@6.3.5

# run again to apply the patch listed in lockfile
pnpm install

# now inspect the lockfile / node_modules
pnpm why rebuild
```

Current output:

```plaintext
Legend: production dependency, optional only, dev only

dependencies:
vite 6.3.5
└── esbuild 0.25.5
```

## Expected Behavior (Based on Incorrect Assumption)

Packages removed in the patch **should not** be in the lockfile nor in `node_modules/`.

## What Actually Happens

With pnpm `10.12.1` the deleted dependencies are still present. This is expected behavior since patching is not intended for modifying package.json dependencies.

## Correct Approach

Instead of using patches to modify dependencies, use [overrides](https://pnpm.io/package_json#pnpmoverrides) or a [package hook](https://pnpm.io/hooks) instead.

## Environment

- macOS 26.0
- Node.js 22.15.0
- pnpm 10.12.1

## License

MIT
