# Shopware Figma Make

Drop-in Figma Make support for a local Shopware checkout.

This repository is an overlay. It contains the files Figma Make expects in a Shopware project, but it does not vendor or fork Shopware itself.

## Requirements

- A local `shopware/shopware` source checkout, preferably on `trunk`
- Docker Desktop with Docker Compose
- Figma Make

The included Docker Compose stack is intended only for local development and uses local default credentials.

## Contents

- `compose.figma.yaml` starts the Figma-only Docker stack.
- `.figma/make/setup` pulls Docker images and starts Docker Desktop on macOS when needed.
- `.figma/make/install` performs a fast, idempotent Shopware setup for Figma Make.
- `.figma/make/dev` starts the Administration watcher at `http://localhost:5173`.
- `.figma/make/verify` checks the preview and Administration snippet loading as a health signal.
- `scripts/install-overlay` copies the overlay into a Shopware checkout.
- `scripts/codex-bootstrap` helps Codex find or create a Shopware checkout and install the overlay.

## Codex-Assisted Setup

### From This Overlay Repository

Open this repository in Codex and use the prompt below to set up the overlay.

```text
Set up this Shopware Figma Make overlay for me. Find a recent local Shopware trunk checkout and install the overlay there. If no checkout exists, ask me before cloning a clean trunk checkout. Do not run the Figma Make install or dev commands yet.
```

Codex should then run:

```bash
scripts/codex-bootstrap --non-interactive
```

The bootstrap script searches common user folders for a recent Shopware `trunk` checkout. When it finds one, it installs the overlay there and prints the Figma Make commands.

After a successful setup, Codex should tell the user they can now open the local Shopware project in Figma Make to start local Shopware development.

If no recent checkout exists, Codex will ask before cloning a clean `trunk` checkout. After approval it can run:

```bash
scripts/codex-bootstrap --non-interactive --clone-if-missing
```

By default, a clean checkout is cloned to:

```text
~/Developer/Shopware/shopware
```

Override that location with `SHOPWARE_DEFAULT_CLONE_DIR`.

The bootstrap script only copies overlay files. It does not run Docker setup, Shopware installation, or the watcher.

### From an Existing Shopware Checkout

Open the Shopware checkout in Codex and use the prompt below to install the overlay without keeping a second checkout around.

```text
Install the public Shopware Figma Make overlay into this existing Shopware checkout. Download https://github.com/shopwareLabs/shopware-figma-make into a temporary directory, run its scripts/install-overlay command against this Shopware root, and clean up the temporary download afterwards. Do not run .figma/make/setup, .figma/make/install, or .figma/make/dev yet.
```

Codex can perform the same flow with:

```bash
tmpdir="$(mktemp -d)"
git clone --depth 1 https://github.com/shopwareLabs/shopware-figma-make "$tmpdir/shopware-figma-make"
"$tmpdir/shopware-figma-make/scripts/install-overlay" .
rm -rf "$tmpdir"
```

This copies only the overlay files into the current Shopware root.

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

- `PORT=5173`
- `FIGMA_MAKE_URL=http://localhost:5173`
- `FIGMA_INTERNAL_APP_URL=http://localhost:8000`
- `BOOTSTRAP_TIMEOUT=600000`

Use `FIGMA_FORCE_RESET=1 .figma/make/install` to force `composer init:db`.

## Watcher Proxy Note

The overlay follows Shopware's default local ports: Shopware itself is available at `http://localhost:8000`, and the Administration watcher is available at `http://localhost:5173`.

The Compose stack also exposes Shopware's default helper ports for the database, mailer, Adminer, and development services.

The Administration watcher runs inside the Docker `web` container. Vite's internal `/api` proxy must target `http://localhost:8000` from inside that container.

## License

MIT. See [LICENSE](LICENSE).
