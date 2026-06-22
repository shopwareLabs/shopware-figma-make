# Shopware Figma Make Overlay

This repository is an overlay for a Shopware checkout. Do not run the Figma Make install/dev commands unless the user explicitly asks.

## Codex Bootstrap

When a user asks to set up, test, or install this overlay:

1. Run `scripts/codex-bootstrap --non-interactive`.
2. If it finds a recent Shopware `trunk` checkout, it installs the overlay there and prints the next commands.
3. If it exits because no recent checkout exists, ask the user before cloning Shopware.
4. Only after user approval, run `scripts/codex-bootstrap --non-interactive --clone-if-missing`.

The bootstrap script only installs overlay files. It does not run:

- `.figma/make/setup`
- `.figma/make/install`
- `.figma/make/dev`

Those commands are for Figma Make or for an explicit user request.
