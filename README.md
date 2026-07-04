# Agent Wiki Toolkit

Adaptive prompt toolkit for choosing the smallest sufficient Agent Wiki workflow.

The toolkit is not one giant prompt. It is a GitHub-hosted set of small routing,
intake, scenario, and core protocol files:

```text
project input
-> minimal diagnosis
-> minimal file fetch
-> bootstrap_tier or daily loop
-> role_mode
-> Agent_Wiki workspace
-> manual handoff or API/orchestrated workflow
```

## Core Rules

- Do not load `core/PRINCIPLES_MAX.md` by default.
- `core/PRINCIPLES_MAX.md` is the bootstrap/recovery/high-risk core.
- `core/PRINCIPLES_MINI.md` is only the daily loop for an already deployed `Agent_Wiki`.
- `core/LITE.md` is for small, low-risk new projects.
- `USER_PROFILE.md` stores durable intake defaults; if unavailable, ask all questions.
- MCP and RAG are optional support layers, not requirements.
- Agent_Wiki files are the durable project memory; chat is transport.
- No secrets in prompts, repo files, logs, screenshots, or Agent_Wiki outputs.

## Entry Modes

Use `intake/ROUTER.md` when the user only describes the project in words.

Use `intake/ADAPTIVE_INTAKE.md` when files, a folder, a repository, an archive,
uploaded documents, datasets, scripts, reports, or an existing `Agent_Wiki` are
available.

## Usage Modes

1. Manual chat handoff
   - Separate Claude/Codex/OpenCode chats coordinate through shared Agent_Wiki files.

2. Simple API flow
   - One agent/API run fetches the correct prompt files and works with Agent_Wiki.

3. Orchestrated API flow
   - Multiple agent/API runs coordinate through Change_Log, cursors, inboxes, and review flags.

## File Map

```text
core/
  LITE.md
  PRINCIPLES_MAX.md
  PRINCIPLES_MINI.md

USER_PROFILE.md

intake/
  ROUTER.md
  ADAPTIVE_INTAKE.md

schemas/
  ROUTER_OUTPUT_SCHEMA.json
  SOURCE_INVENTORY_SCHEMA.json

orchestration/
  AGENT_FETCH_PROMPT.md
  API_ORCHESTRATION_SPEC.md
  MCP_INTEGRATION.md
  STORAGE_RETRIEVAL_STRATEGY.md

scenarios/
  MANUAL_CHAT_HANDOFF.md
  API_SIMPLE_FLOW.md
  API_ORCHESTRATED_FLOW.md

context/
  GPT_CONTEXT.md

project_notes/
  BOOK_PROJECT_NOTES.md
```

## Minimal Fetch Rule

1. Fetch `MANIFEST.json`.
2. Choose entry mode:
   - description only -> `intake/ROUTER.md` + `schemas/ROUTER_OUTPUT_SCHEMA.json`
   - files/repo/archive -> `intake/ADAPTIVE_INTAKE.md` + `schemas/SOURCE_INVENTORY_SCHEMA.json`
3. Ask only blocking questions.
4. Fetch only `files_to_fetch`.
5. Prepend the selected runtime header and run the workflow.

Do not paste MAX everywhere. Start small and fetch only what the task needs.
