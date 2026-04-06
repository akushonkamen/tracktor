# Plan: Gate Requirements + npm One-Click Deploy + Chinese Support

## Requirements Summary

1. **Gate Requirements**: Adapt the 3-level execution gate system from `cnfjlhj/ai-collab-playbook` (CLAUDE.md + AGENTS.md) into the Plane repository, customized for Plane's monorepo context
2. **npm One-Click Local Deployment**: Create a single-command local dev setup that orchestrates Docker infra + Django API + frontend dev servers
3. **Chinese Support**: Already sufficient in Plane's i18n (zh-CN/zh-TW) — no extra work needed
4. **Delivery**: Via GitHub issues and PRs

## Acceptance Criteria

- [ ] `CLAUDE.md` created at repo root with full gate system adapted for Plane
- [ ] `AGENTS.md` updated with gate requirements + existing dev guide preserved
- [ ] `npm run setup` installs deps, generates .env, starts Docker infra, runs migrations
- [ ] `npm run dev:full` starts all services (infra + API + frontend) in one command
- [ ] `npm run dev:infra` starts only Docker infrastructure
- [ ] `npm run dev:api` starts Django API server
- [ ] `npm stop` cleanly stops all services
- [ ] GitHub issues created for tracking
- [ ] PRs created with proper descriptions referencing issues

## Implementation Steps

### Phase 1: Gate Requirements (Issue #1 + PR #1)

**Step 1.1 — Create GitHub Issue**

- Title: `[INFRA] Add CLAUDE.md and update AGENTS.md with execution gate requirements`
- Body: Describe the 3-level gate system and why it matters for AI-assisted development

**Step 1.2 — Create `CLAUDE.md`** (new file at repo root)

Adapt from ai-collab-playbook with Plane-specific context:

- Work modes (default executable, solution-only, "一路畅行")
- Skills discovery (reference Plane's pnpm/turbo commands)
- Core behavior: deep reasoning, proactive exploration
- **Execution gates** (3-level system):
  - Level 1: Task-scoped local changes (pnpm dev, code edits within task scope)
  - Level 2: Config/deps changes (.env, package.json, Docker configs, turbo.json) — wait for confirmation
  - Level 3: Destructive ops (git force push, drop DB, production changes) — backup + double confirm
- Dangerous operation handling
- Result verification (verification-before-completion)
- Multi-agent rules
- Retry limits (max 3)
- Engineering standards adapted for Plane's stack
- Plane-specific: monorepo structure, pnpm workspace, Turborepo pipeline, Django backend

**Step 1.3 — Update `AGENTS.md`** (existing file at repo root)

Merge gate requirements into the existing content:

- Keep existing dev commands section
- Keep existing code style section
- Add gate system section (same 3-level structure as CLAUDE.md but platform-agnostic)
- Add core behavior guidelines
- Add engineering standards

**Step 1.4 — Create PR**

- Branch: `infra/gate-requirements`
- Title: `[INFRA] Add CLAUDE.md and update AGENTS.md with execution gate requirements`
- Reference the issue

### Phase 2: npm One-Click Local Deployment (Issue #2 + PR #2)

**Step 2.1 — Create GitHub Issue**

- Title: `[INFRA] Add npm one-click local deployment scripts`
- Body: Describe the developer experience goal — single command to get full dev environment running

**Step 2.2 — Create `scripts/setup.js`**

Node.js setup script that:

1. Checks prerequisites (Node.js >=22, pnpm, Docker)
2. Creates `.env` from `.env.example` with sensible local dev defaults
3. Runs `pnpm install`
4. Starts Docker infrastructure via docker-compose-local.yml
5. Waits for health checks (Postgres, Redis, RabbitMQ, MinIO)
6. Runs Django migrations
7. Creates superuser prompt (optional)
8. Prints ready message with URLs

**Step 2.3 — Create `scripts/dev.js`**

Orchestrator script that:

1. Starts Docker infra (if not running)
2. Starts Django API in background (with auto-reload)
3. Starts frontend dev servers via `pnpm dev`
4. Handles graceful shutdown (SIGINT/SIGTERM)
5. Color-coded output with service labels

**Step 2.4 — Create `scripts/stop.js`**

Script that:

1. Stops Docker Compose services
2. Kills any background processes started by dev.js
3. Cleans up PID files

**Step 2.5 — Create `scripts/lib/health-check.js`**

Shared utility:

- TCP port check with retries
- HTTP health endpoint polling
- Color output for status

**Step 2.6 — Create `scripts/lib/docker.js`**

Docker Compose helper:

- Check if Docker is running
- Start/stop services
- Check service health
- Get service URLs

**Step 2.7 — Update `package.json` scripts section**

Add new scripts:

```json
{
  "setup": "node scripts/setup.js",
  "dev:full": "node scripts/dev.js",
  "dev:infra": "docker compose -f docker-compose-local.yml up -d",
  "dev:api": "cd apps/api && python manage.py runserver",
  "stop": "node scripts/stop.js"
}
```

**Step 2.8 — Update `.env.example`**

Ensure all required env vars are documented with sensible defaults for local dev.

**Step 2.9 — Create PR**

- Branch: `infra/npm-one-click-deploy`
- Title: `[INFRA] Add npm one-click local deployment scripts`
- Reference the issue

### Phase 3: Create PRs and Link Issues

**Step 3.1 — Push branches and create PRs** targeting `preview` branch
**Step 3.2 — Link PRs to issues in descriptions**

## Key Files to Create/Modify

| File                          | Action                   | Phase   |
| ----------------------------- | ------------------------ | ------- |
| `CLAUDE.md`                   | Create                   | Phase 1 |
| `AGENTS.md`                   | Modify                   | Phase 1 |
| `scripts/setup.js`            | Create                   | Phase 2 |
| `scripts/dev.js`              | Create                   | Phase 2 |
| `scripts/stop.js`             | Create                   | Phase 2 |
| `scripts/lib/health-check.js` | Create                   | Phase 2 |
| `scripts/lib/docker.js`       | Create                   | Phase 2 |
| `package.json`                | Modify (scripts section) | Phase 2 |
| `.env.example`                | Modify (if needed)       | Phase 2 |

## Risks and Mitigations

| Risk                                                      | Impact                          | Mitigation                                                                            |
| --------------------------------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------- |
| Gate requirements too opinionated for open-source project | Contributors may resist         | Keep gates as guidelines, not enforced; clearly mark as "for AI-assisted development" |
| Docker health checks flaky on slow machines               | Setup script hangs              | Use generous timeouts (60s) with progress feedback; allow skip                        |
| Windows compatibility for Node.js scripts                 | Scripts fail on Windows         | Use cross-platform Node.js APIs; avoid shell-specific commands                        |
| `.env` generation overwrites existing config              | Developers lose custom settings | Prompt before overwriting; backup existing `.env`                                     |

## Verification Steps

1. Gate requirements: Read CLAUDE.md and AGENTS.md, verify all 3 levels documented
2. Setup: Run `npm run setup` on a clean clone, verify all services start
3. Dev: Run `npm run dev:full`, verify web:3000, admin:3001, api:8000 all accessible
4. Stop: Run `npm stop`, verify all processes and Docker containers stopped
5. PRs: Verify GitHub issues and PRs are created and linked
