# BOOK_PROJECT_NOTES — book project only (NOT part of the universal toolkit)

This file holds legacy and project-specific notes for the **book library project** that the Agent Wiki protocol originally evolved from. It is intentionally kept OUT of `PRINCIPLES_MAX.md` / `PRINCIPLES_MINI.md` so the universal toolkit stays domain-agnostic and portable to any machine.

**Where this file lives:** inside the book project, next to its `Agent_Wiki/Project/`. It travels with the book project (it is part of that git repo), not with the toolkit. A new, non-book project does not need it. Keep a link to it from the book project's `00_START_HERE.md` (or `Agent_Protocol.md`) so the book project never loses this context — when you deploy on another machine, clone the book project and this file comes with it.

---

## Legacy instance name map (book project)

Earlier instances of the book project used different filenames. The protocol's canonical names map as follows:

```text
Изменения_Codex.md        = Change_Log.md
Obsidian_Book_Wiki        = Agent_Wiki
Claude_Codex_Protocol.md  = Agent_Protocol.md
```

When operating on the legacy book project, treat the left-hand names as the right-hand canonical ones.

## Stage 1 normalization exception (book project)

`stage1_prepare_metadata.py` already writes LLM normalization directly to `normalized_title`, instead of the v3-preferred separation (`raw_*` vs `normalized_*_candidate` + `normalization_confidence`). Changing this requires a schema change and is deferred for the existing book project. **New projects use the `raw_*` / `normalized_*_candidate` separation per §16.** This exception applies ONLY to the existing book pipeline; do not carry it into any other domain.

---

## v2 → v3 migration delta (book project)

The book project currently runs on protocol_version 2. It keeps working as-is; migrate only when convenient. To bring it to v3:

1. **protocol_version:** bump `00_START_HERE.md` and `Agent_Protocol.md` to `protocol_version: 3`. (Until you do, a v3 agent reading a v2 header will flag an old schema — expected.)
2. **Core events:** the book project gains the new core events — **Query** (answer + file reusable findings back to the wiki as agent_inference with source links), **Lint** (Codex mechanical pass + Claude semantic judgement), and explicit **contradiction handling** during ingest (flag, link both, route to Claude, never overwrite). These are additive; nothing existing breaks.
3. **Adapter:** the book adapter is already captured as the filled reference in `PRINCIPLES_MAX.md` Appendix B1. Reconcile the project's `Agent_Protocol.md` against it.
4. **This file:** keep it linked from the book project's `00_START_HERE.md`; the name map and the Stage 1 exception above remain in force for the book pipeline only.
