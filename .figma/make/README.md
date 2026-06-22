# Figma Make

These scripts run a fast Shopware setup for Figma Make. They target the Administration watcher at `http://localhost:5173`, matching the default Shopware watcher port, and intentionally skip full storefront and production asset builds.

## Commands

- `.figma/make/setup` pulls the Figma Docker images.
- `.figma/make/install` starts services, installs PHP dependencies when `composer.json` or `composer.lock` changes, installs the database when missing, and installs only Administration npm dependencies when needed. Composer post-install scripts are skipped because the repository-wide dev tooling setup is not required for the Figma Make preview.
- `.figma/make/dev` starts the Administration watcher. It passes `$FIGMA_INTERNAL_APP_URL` to the watcher so Vite proxies Administration API requests to the PHP app inside the container.
- `.figma/make/verify` waits for `$FIGMA_MAKE_URL` to return a successful HTTP response and checks that Administration snippets load through the Vite proxy.

## Environment

`.figma/make/env` defines the preview URL and timeout:

- `FIGMA_MAKE_URL` defaults to `http://localhost:5173`.
- `FIGMA_INTERNAL_APP_URL` defaults to `http://localhost:8000` and is used only inside `.figma/make/dev` for Vite's `/api` proxy target.
- `BOOTSTRAP_TIMEOUT` is in milliseconds and defaults to `600000`.
- `FIGMA_FORCE_RESET=1 .figma/make/install` forces `composer init:db` even when the database already looks installed.

Warm-run markers are stored in `var/figma-make/`. Delete that directory if you need to force dependency checks without resetting the database.

If Administration text renders as snippet keys such as `sw-login.index.headlineMain`, the watcher is usually running but its API proxy cannot reach Shopware. In this Docker setup, the host-facing URL and the in-container watcher proxy both use `http://localhost:8000`.

## Figma Make Context

Figma Make can use plan mode, Figma library/style context, web fetch, and MCP connectors as extra context when generating prototypes. Custom MCP connectors must be reachable over public HTTPS; Figma Make does not connect to local `localhost` or `stdio` MCP servers.
