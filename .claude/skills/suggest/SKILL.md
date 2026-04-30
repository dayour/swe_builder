---
name: suggest
description: Submit a feature suggestion conversationally — Claude gathers details, previews, and creates an ADO work item via az CLI.
---

# Feature Suggestion

File a feature suggestion on Azure DevOps without leaving the dashboard.

## Process

1. **Auth & tooling check** — run `az account show`. If not authenticated, tell the user to run `az login` and stop. Then verify DevOps extension: `az extension show --name azure-devops`. If not installed, run `az extension add --name azure-devops`. Configure defaults if needed: `az devops configure --defaults organization=https://dev.azure.com/powercatteam project=FDE`.

2. **Account confirmation (MANDATORY)** — show the user the signed-in account from `az account show` output and ask them to confirm:
   > You're signed in as **[account name]** ([email]). Work items will be created in **powercatteam/FDE**. Is this correct?

   Do NOT proceed until the user confirms. If they say no, help them switch accounts with `az login`.

3. **Check for pre-filled context** — if args were passed (from the dashboard dialog), extract the context and skip the "ask" step. The format is: `Project: X | Agent: Y | Page: Z | ... | User says: "description"`. Use the "User says" portion as the suggestion description and the rest as auto-context.

4. **If no args**, ask ONE question:
   > What would you like to see added or improved?

5. **Auto-gather context** Claude already knows:
   - Current project or workflow area (from conversation or pre-filled args)
   - Related skills or components
   - Any prior discussion context from the session

6. **Generate work item**:
   - **Title**: `Suggestion: <concise summary>` (under 70 chars)
   - **Body** (markdown/HTML):
     ```
     <h2>Problem / motivation</h2>
     <p>[why this would help]</p>

     <h2>Proposed solution</h2>
     <p>[what the user wants]</p>

     <h2>Context</h2>
     <ul>
     <li>Area: [dashboard, skills, build workflow, etc.]</li>
     <li>Related: [any related features or components]</li>
     <li>Page: [if provided]</li>
     </ul>
     ```

7. **Preview** — show the full title + body to the user and ask:
   > Here's the suggestion I'll create. Want me to submit it, or would you like to change anything?

8. **On confirmation** — run:
   ```
   az boards work-item create --title "Suggestion: ..." --type Feature --description "$(cat <<'EOF'
   <body>
   EOF
   )" --org https://dev.azure.com/powercatteam --project FDE
   ```

9. **Return** the work item ID and URL to the user.

## Rules

- Org is always `https://dev.azure.com/powercatteam`, project is always `FDE`
- Work item type is always `Feature`
- ALWAYS confirm the user's signed-in account before creating anything
- NEVER submit without user confirmation of the work item preview
- Use HEREDOC for the description to preserve formatting
- Keep the title under 70 characters
- If the user's idea is vague, ask ONE follow-up — don't interrogate
- When invoked with pre-filled args from the dashboard, skip the description question but STILL confirm the account
