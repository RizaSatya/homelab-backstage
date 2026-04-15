# AGENTS.md

## Stack

- **Runtime**: Node 22 or 24
- **Package Manager**: Yarn 4.4.1 (`.yarnrc.yml` sets `nodeLinker: node-modules`)
- **Backstage Version**: 1.50.0
    - always use this version when looking documentation 
- **CLI**: `@backstage/cli` — use `backstage-cli` for package-level scripts

## Workspaces

```
packages/app      # Frontend (role: frontend)
packages/backend  # Backend (role: backend)
plugins/*        # Plugins (discovered by Backstage)
```

## Key Commands

| Command              | Description                                                        |
| -------------------- | ------------------------------------------------------------------ |
| `yarn install`       | Install deps (use `--immutable` in CI)                             |
| `yarn start`         | Start full app (frontend + backend via `backstage-cli repo start`) |
| `yarn start app`     | Start frontend only (port 3000)                                    |
| `yarn start backend` | Start backend only (port 7007)                                     |
| `yarn build:backend` | Build backend only                                                 |
| `yarn build:all`     | Build all packages                                                 |
| `yarn tsc`           | Type-check (short-circuit, uses `skipLibCheck`)                    |
| `yarn tsc:full`      | Full type-check without skipping lib checks                        |
| `yarn test`          | Run tests via `backstage-cli repo test`                            |
| `yarn test:e2e`      | Run Playwright e2e tests                                           |
| `yarn lint`          | Lint since origin/main                                             |
| `yarn lint:all`      | Lint everything                                                    |
| `yarn fix`           | Auto-fix lint issues                                               |
| `yarn new`           | Scaffold new package/plugin                                        |

## Development

- Config: `app-config.yaml` (dev), `app-config.production.yaml` (prod)
- `GITHUB_TOKEN` env var required for GitHub integrations
- `BACKEND_SECRET` env var for backend auth (default: `dev-secret-key`)
- In-memory SQLite DB for local dev (`database.client: better-sqlite3`)
- Guest auth enabled by default (`auth.providers.guest: {}`)

## CI / Release

- On tag push (`v*`), CI builds Docker image via `packages/backend/Dockerfile`
- Image tagged: `ghcr.io/rizasatya/homelab-backstage:<tag>`
- CI steps: `yarn install --immutable` → `yarn tsc` → `yarn build:backend`

## Architecture Notes

- Backend is the actual app entrypoint; frontend (`packages/app`) is bundled into it via `@backstage/plugin-app-backend`
- Plugin discovery: set `app.packages: all` in config, packages are read from `packages/*/package.json`
- tsconfig extends `@backstage/cli/config/tsconfig.json`, compiles to `dist-types/`
