# Shopware Figma Make

Drop-in Figma Make support for a Shopware source checkout.

This repository is an overlay. It contains the files Figma Make expects in a Shopware project, but it does not vendor or fork Shopware itself.

## Contents

- `compose.figma.yaml` starts the Figma-only Docker stack.
- `.figma/make/setup` pulls Docker images and starts Docker Desktop on macOS when needed.
- `.figma/make/install` performs a fast, idempotent Shopware setup for Figma Make.
- `.figma/make/dev` starts the Administration watcher at `http://localhost:9173`.
- `.figma/make/verify` checks the preview and Administration snippet loading.
- `scripts/install-overlay` copies the overlay into a Shopware checkout.

## Install Into Shopware

From this repository:

```bash
scripts/install-overlay /path/to/shopware
```

Or from the Shopware checkout:

```bash
/path/to/shopware-figma-make/scripts/install-overlay .
```

After installing the overlay into a Shopware checkout:

```bash
.figma/make/setup
.figma/make/install
.figma/make/dev
```

Then verify the running preview:

```bash
.figma/make/verify
```

## Environment

The overlay writes `.figma/make/env` with these defaults:

- `FIGMA_MAKE_URL=http://localhost:5173`
- `FIGMA_INTERNAL_APP_URL=http://localhost:8000`
- `BOOTSTRAP_TIMEOUT=600000`

Use `FIGMA_FORCE_RESET=1 .figma/make/install` to force `composer init:db`.

## Watcher Proxy Note

The overlay follows Shopware's default local ports: Shopware itself is available at `http://localhost:8000`, and the Administration watcher is available at `http://localhost:5173`.

The Administration watcher runs inside the Docker `web` container. Vite's internal `/api` proxy must target `http://localhost:8000` from inside that container.

If Administration text renders as snippet keys such as `sw-login.index.headlineMain`, the watcher is usually running but the Vite API proxy cannot reach Shopware.

## Publish

Suggested initial repository setup:

```bash
git init
git add .
git commit -m "Add Shopware Figma Make overlay"
gh repo create shopwareLabs/shopware-figma-make --public --source=. --remote=origin --push
```
