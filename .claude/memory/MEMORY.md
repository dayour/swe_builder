# MCS Automation Memory

> Index of persistent learnings. Detailed project history in `projects.md`.
> CLAUDE.md has full workflow, skills, patterns, frameworks — don't duplicate here.

## Quality Check Triggers (Every Response)

| What Just Happened | Action Required |
|-------------------|----------------|
| Edited 3+ files or any code | Spawn **Repo Checker** in background |
| Schema/workflow/architecture decision | Spawn **QA Challenger** (blocks response) |
| Simple edit (1-2 files, docs only) | Self-check: grep for broken refs |
| No file changes | Nothing needed |

---

## Session Startup

- Do NOT ask account/environment on session start
- Account/env is selected once during first build and persisted in brief.json `buildStatus` + `session-config.json` `sessionDefaults`
- All subsequent operations (build, eval, fix) use **silent verification**: navigate → snapshot → compare against persisted config → proceed on match, alert on mismatch
- `session-config.json.sessionDefaults` serves as cross-project fallback: pre-fills and asks one yes/no for new agents
- User can always override by saying "switch to [account/env]"
- Auto-refresh stale Tier 1 knowledge cache (> 7 days → refresh via MS Learn MCP + WebSearch)
- Report: "Cache: N/18 fresh, M refreshed, K flagged stale"

---

## Machine-Specific Details

### Installed Tools & Versions
- **PAC CLI**: v2.1.2 (MSI, no MCP) + v2.2.1 (NuGet via dnx, has MCP)
- **.NET SDKs**: 8.0.418, 9.0.311, 10.0.102
- **Az.Accounts**: NOT installed (was wrongly listed as v5.3.2). Use `az account get-access-token --resource https://<org>.crm.dynamics.com` instead.
- **Pandoc**: `%LOCALAPPDATA%\Pandoc\pandoc.exe` (resolve via `where pandoc` or `gcm pandoc`)
- **WorkIQ CLI**: v0.2.8 at `%APPDATA%\npm\workiq` (resolve via `where workiq` or `gcm workiq`)

### WorkIQ MCP
- **Auth**: One-time interactive browser auth per tenant (`workiq ask -q "test"`)
- **Auth tenant**: kimdennis@microsoft.com (Microsoft tenant)
- **Data sources**: Emails, Meetings, Docs, Teams chats, Teams channels, People, External connectors
- **Key constraint**: Only sees data the authenticated user has permissions to view

### Dataverse Access Methods (All 3 Installed)
- **Dataverse MCP Server** (v0.2.310025): 11 tools, 20-row limit, needs PPAC admin enable
- **PAC CLI MCP Server** (v2.2.1): 52 tools via dnx, `copilot_publish` + `env_fetch` (FetchXML, no row limit)
- **PowerShell Web API** (`tools/dataverse-helper.ps1`): Full CRUD + bound actions, no limits
- **CRITICAL**: `pac auth create-token` does NOT exist. Use `az account get-access-token` instead (Az.Accounts not installed).
- **CRITICAL**: Raw `POST /botcomponents` creates records but MCS doesn't see them. Use Playwright Code Editor for new topics, Dataverse PATCH only for existing components.
- **`cr3f1_stagedescription` MaxLength = 100**: Check column limits before bulk inserts

---

## Account / Tenant / Environment Map

| Account | Tenant | PAC Profile | Environments | Dataverse URL |
|---------|--------|-------------|-------------|---------------|
| dennis@testtesttoltest | TestTestTOLTest | — | Test_Test_TOL_Test (default) | orgccf4f9a1.crm.dynamics.com |
| admin@M365CPI15209943 | M365CPI15209943 | [2] | dktest (org04723bf3), Contoso (org39d3f1ca) | — |
| kimdennis@microsoft.com | Microsoft | [1] | TBD | — |

Account+env is persisted and verified silently — see CLAUDE.md "MCS Browser Preflight — Silent Verification".

---

## Source Control

- **Repo**: Azure DevOps — `powercatteam/FDE` (https://dev.azure.com/powercatteam/_git/FDE)
- **Remote**: `origin` → ADO (old GitHub remotes removed)
- **No GitHub repos** — all issues, PRs, and pushes go to ADO

---

## Hard-Won Lessons (Not in CLAUDE.md)

### Environment Awareness
- PAC CLI "dktest" (org04723bf3) ≠ MCS UI "Test_Test_TOL_Test (default)"
- Builder PM RoB Manager lives in Test_Test_TOL_Test, NOT dktest
- PAC CLI can't see agents in a different environment — use browser if env mismatch

### Agent Creation Methods (Feb 2026 Research)
- 9 creation paths: MCS UI, Agent Builder M365, VS Code Extension, M365 Agents SDK, Teams AI Library, Azure AI Foundry, Power Apps Plan Designer, SharePoint Agents, PAC CLI
- Foundry → MCS: Connected agent (preview) — runtime bridge, NOT import
- VS Code Extension (GA Jan 2026): Clone/edit/sync YAML, but clone/apply are GUI-only
- For full Power Platform features: Agent must be native MCS
- `pac copilot create`: Template-based, captures topics/instructions only, NOT tools/knowledge/model

### Workflow Corrections
- **7 MCS skills** + 2 utility (bug, suggest) = 9 skill folders total
- `/mcs-analyze` is deprecated — merged into `/mcs-research`
- `/mcs-update` is removed — Phase 0 incremental in `/mcs-research` covers its use case

---

## Project History

See `projects.md` for completed and active project details.
