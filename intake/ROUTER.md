# ROUTER.md - Agent Wiki Questionnaire Router

Purpose: choose the smallest sufficient Agent Wiki files when the user describes
a project in words and there are no project files to inspect.

`router_version: 2`
`protocol_family: Agent Wiki`
`compatible_protocol_version: 3`

Do not fetch `core/PRINCIPLES_MAX.md` by default.

Read `USER_PROFILE.md` first when available; skip any question it answers; apply
its overrides. If it is absent, continue and ask all needed questions.

## Routing Model

Use separate fields:

- `bootstrap_mode`: `new_project`, `existing_agent_wiki`, or `recovery_needed`
- `bootstrap_tier`: `LITE`, `MAX`, or `null`
- `daily_loop_file`: `core/PRINCIPLES_MINI.md` or `null`
- `role_mode`: `SOLO`, `DUAL`, or `FULL`

`MINI` is not a bootstrap tier. It is only the daily loop file for an already
deployed Agent Wiki.

## Bootstrap Files

| Case | Fetch |
|---|---|
| New low-risk project | `core/LITE.md` |
| New high-risk/heavy project | `core/PRINCIPLES_MAX.md` |
| Existing deployed Agent_Wiki daily work | `core/PRINCIPLES_MINI.md` |
| Recovery, corrupt context, missing adapter | `core/PRINCIPLES_MAX.md` |

## Role Modes

| Mode | Meaning |
|---|---|
| `SOLO` | One agent covers all lanes (implement + review + audit), self-checking. |
| `DUAL` | Implementer + one reviewer (review+audit folded). |
| `FULL` | Separate agents for implement / review / audit lanes. Default binding Codex/Claude/OpenCode; may bind to local-model agents instead. |

Role mode is runtime configuration. Do not create separate prompt files for role
counts. role_mode is lane coverage, not specific products. See USER_PROFILE.md
when available for the lane->agent binding.

## Questions

Ask only missing questions. If the user already answered, score directly.

### Bootstrap Mode

1. Does an `Agent_Wiki/` already exist?
   - no -> `new_project`
   - yes and healthy -> `existing_agent_wiki`
   - yes but corrupt/missing context/old schema -> `recovery_needed`

### Strictness / Risk Score

Score each answer and cap total at 100.

1. Project duration
   - one-off / throwaway = 0
   - days/weeks = 5
   - months/long-running = 10

2. Source volume / complexity
   - under 20 files/items, simple notes = 0
   - 20-500 files/items or mixed formats = 8
   - 500+ files/items, DB/API/pipeline, many dependencies = 15

3. Expected corpus size
   - under 100 MB = 0
   - 100 MB to 5 GB = 5
   - 5 GB to 100 GB = 12
   - over 100 GB / potentially 1 TB+ = 20

4. Destructive operations
   - none; read-only = 0
   - possible file generation/overwrite = 10
   - move/rename/delete/source mutation/DB writes = 25

5. Data loss / money / legal / business risk
   - no meaningful risk = 0
   - inconvenient if wrong = 10
   - serious loss, legal/financial/reputational impact = 25

6. Need for provenance / citations / audit trail
   - casual notes = 0
   - useful to track sources = 10
   - mandatory evidence, source vs inference, review trail = 20

7. Automation / API / DB / repeated pipeline
   - manual chat only = 0
   - repeated process or simple scripts = 10
   - API/DB/expensive LLM calls/checkpoints/idempotency needed = 20

8. Contradictions / freshness / research uncertainty
   - stable and obvious = 0
   - some uncertainty = 8
   - research/OSINT/legal/current facts/conflicting sources = 15

9. Stakeholders / external use
   - only user, private scratch = 0
   - shared with team / later reuse = 5
   - submitted/published/used for decisions = 10

### Agent Independence Score

Score each answer and cap total at 100.

1. Implementation needed
   - no code/files generated = 0
   - files/reports generated = 10
   - scripts, DB/API, exports, destructive manifests = 25

2. Semantic judgement needed
   - no, just mechanical = 0
   - some classification/recommendation = 15
   - architecture/legal/research meaning decisions = 25

3. Independent audit needed
   - no = 0
   - useful = 15
   - required due to destructive/high-risk operations = 35

4. Different engines available
   - only one model/tool available = 0
   - two engines available = 10
   - three-role workflow available = 15

5. User can manually verify everything
   - yes, easy = 0
   - partly = 5
   - no, too large/technical = 10

6. Token/cost sensitivity
   - extreme token saving required = -15
   - normal = 0
   - accuracy/audit more important than tokens = 10

If `USER_PROFILE.md` sets token/cost sensitivity to extreme token saving, use -15
by default, but override to +10 when the project has data-loss, legal, financial,
or government-submission risk. Token preference must never silently drop a
high-stakes project to a weaker role_mode tier.

## Decision Rules

If `bootstrap_mode` is `existing_agent_wiki`, fetch only
`core/PRINCIPLES_MINI.md` unless recovery/bootstrap/domain adapter is needed.

If `bootstrap_mode` is `recovery_needed`, fetch `core/PRINCIPLES_MAX.md`.

For `new_project`:

- strictness `0-29` -> `bootstrap_tier: LITE`
- strictness `30-100` -> `bootstrap_tier: MAX`

Override upward to `MAX` if any of these are true:

- destructive source operations are planned;
- legal/financial/high-stakes evidence is required;
- DB/API writes or expensive LLM batches are planned;
- corpus is huge and needs a corpus intake plan before analysis;
- user explicitly requests strict audit.

Role mode:

- independence `0-24` -> `SOLO`
- independence `25-59` -> `DUAL`
- independence `60-100` -> `FULL`

Override `FULL` if destructive operations plus the user cannot manually inspect
the outputs. If only one model/tool is available, use `SOLO` and set
`independence_gap: true`.

## Output Format

Output JSON first, then a short human explanation.

```json
{
  "router_version": 2,
  "protocol_version": 3,
  "entry_mode": "questionnaire",
  "bootstrap_mode": "new_project",
  "bootstrap_tier": "LITE",
  "daily_loop_file": null,
  "role_mode": "SOLO",
  "strictness_score": 0,
  "independence_score": 0,
  "corpus_size_hint": "unknown",
  "files_to_fetch": ["core/LITE.md"],
  "do_not_fetch": ["core/PRINCIPLES_MAX.md"],
  "reason": [],
  "token_policy": "fetch smallest sufficient prompt; do not fetch MAX unless required",
  "independence_gap": false,
  "next_action": "fetch selected files and continue"
}
```

## Hard Routing Rules

- Never fetch all files just in case.
- Never fetch `core/PRINCIPLES_MAX.md` when `core/LITE.md` is enough.
- Never treat `project_notes/BOOK_PROJECT_NOTES.md` as universal.
- If GitHub is unavailable, ask the user for the exact selected file only.
- If answers are incomplete, ask the missing router questions; do not guess.
