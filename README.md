# pnpm Patch Dependencies Issue

This repo is a minimal repro for [pnpm/pnpm#9684](https://github.com/pnpm/pnpm/issues/9684).

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

## Expect Behavior

Packages removed in the patch **should not** be in the lockfile nor in `node_modules/`.

## What Actually Happens

With pnpm `10.12.1` the deleted dependencies are still present.

## Environment

- macOS 26.0
- Node.js 22.15.0
- pnpm 10.12.1

## License

MIT
