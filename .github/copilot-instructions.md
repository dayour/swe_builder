# Copilot Cloud Agent Onboarding

## What this repository does
- Builds and operates **swe_builder**, a dashboard + CLI system for Microsoft Copilot Studio agent automation.
- Main stack:
  - Node.js scripts/CLI at repo root (`start.js`, `bin/`)
  - Python FastAPI backend (`app/server.py`)
  - React + TypeScript frontend (`app/frontend/`)
  - Claude skills/configuration (`.claude/`)

## Fast orientation (read this first)
1. Read `/home/runner/work/swe_builder/swe_builder/README.md` for product workflow and commands.
2. Check `/home/runner/work/swe_builder/swe_builder/package.json` scripts for the canonical commands.
3. Avoid changing generated/derived output unless needed:
   - `app/dist/` (frontend build output)
   - `Build-Guides/` (runtime project workspace, gitignored)

## High-value directories
- `/home/runner/work/swe_builder/swe_builder/app/frontend/src/` — React UI pages, components, stores
- `/home/runner/work/swe_builder/swe_builder/app/server.py` — backend API + static hosting
- `/home/runner/work/swe_builder/swe_builder/app/terminal-server.js` — terminal websocket server
- `/home/runner/work/swe_builder/swe_builder/tools/` — automation/validation utilities
- `/home/runner/work/swe_builder/swe_builder/.claude/skills/` — workflow skills (research/build/eval/fix/etc.)
- `/home/runner/work/swe_builder/swe_builder/templates/brief.json` — baseline brief schema

## Standard development commands
Run from `/home/runner/work/swe_builder/swe_builder`.

```bash
npm ci
npm run frontend:build
npm test -- --token-endpoint <endpoint>
```

Notes:
- `npm ci` triggers `postinstall` (`bin/postinstall.js`), which installs Python deps and builds the frontend.
- `npm test` maps to `node tools/direct-line-test.js` and needs auth input.

## Change strategy for cloud agents
- Keep changes **surgical** and limited to the issue scope.
- Prefer editing existing files over creating new abstractions.
- For UI work, confine changes to the smallest relevant component/store/page.
- For backend work, keep API contracts stable unless the issue explicitly requires contract changes.

## Validation expectations
- Always run at least the most relevant existing command(s) for changed area.
- Minimum safe check for most changes:
  - `npm run frontend:build`
- If touching eval/test tooling, run `npm test` with required token args.

## Errors encountered in onboarding and workarounds
1. **`npm test` fails without auth args**
   - Error: `--token or --token-endpoint is required`
   - Workaround: run tests with `--token` or `--token-endpoint`.
2. **CI run can appear as `action_required` with zero jobs/logs**
   - Observed on workflow run `CI` where the run had no jobs listed.
   - Workaround: check workflow run state/approvals first, then re-run after required approval/config is satisfied.

## Practical tips for future agents
- Use absolute paths when referencing files during analysis.
- Prefer `rg`/`glob`/`view` for discovery before editing.
- Do not assume external services (PAC, Direct Line, Azure auth) are available in sandbox; detect and report clearly.
