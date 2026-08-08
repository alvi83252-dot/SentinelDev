# AGENTS.md

## Cursor Cloud specific instructions

SentinelDev is a single **Next.js 15 (App Router)** web app plus a **terminal CLI**, both in this repo. It scans codebases for exposure risks and is "propose-only" (never modifies code). See `README.md` for full feature/command docs.

### Node version (important gotcha)
- The npm scripts run node with `--use-system-ca`, which requires **Node >= 22.15**. The VM's default `/exec-daemon/node` is 22.14 and will fail with `node: bad option: --use-system-ca`.
- A newer Node (22.x, via `nvm`) is installed and prepended to `PATH` in `~/.bashrc`, so shells already resolve the correct `node`. Verify with `node -v` (should be >= 22.15). If you ever see the `--use-system-ca` error, run `nvm use default` (or `nvm install 22`).

### Services
- **Web app**: `npm run dev` (Next.js dev server on http://localhost:3000). Landing page at `/`, interactive scan console at `/dashboard`. API routes: `POST /api/scan`, `POST /api/chat`, `POST /api/pdf`, `GET /api/fix-logs`, `GET /api/supabase/health`.
- **CLI**: `npm run cli -- <cmd>` (e.g. `npm run cli -- scan --kind secrets --root ./fixtures`). `fixtures/vulnerable-sample.ts` is an intentional test target.

### External services are all optional
- Supabase / NVIDIA / OpenAI / Modal keys (see `.env.example`) are **optional**. With none set, scans still run end-to-end; fix-log rows are just returned in the JSON response instead of being persisted, and NL answers fall back to keyword routing. Copy `.env.example` to `.env.local` only if you want to wire up those integrations.

### Lint / typecheck
- Use `npm run typecheck` (`tsc --noEmit`) as the static-analysis gate.
- `npm run lint` (`next lint`) is **deprecated and interactive**: the repo ships no ESLint config, so it prompts to create one and cannot run non-interactively. Prefer `typecheck` unless/until an ESLint config is added.
