---
name: memory
description: "Portable memory skill for any agent workflow, including non-interactive and self-healing ones. Use to query past issues and lessons before a run and to record errors, self-heal results, and fixes so the next run avoids the same problems. Triggers: start of task, end of task, error/failure, self-heal attempt, user correction."
---

# Memory Skill (Workflow-Agnostic + Self-Heal)

This skill gives any agent a simple **query → act → build** memory loop so experience compounds across runs. It is especially useful for **non-interactive / self-healing workflows** that should remember issues and avoid repeating the same failures.

## Memory layout (relative to project root)
```
memory/
├── log.md              # Chronological session notes (episodic)
├── lessons.md          # Distilled insights, patterns, and gotchas
├── issues.md           # Specific errors, failures, and their resolutions
├── preferences.md      # Stable preferences & rules
└── decisions.md        # Important decisions + rationale
```

## Auto-create memory files
If any of the memory files do not exist, create them on first use with the templates below. Do this before the Query step if needed.

**memory/log.md**
```md
# Session Log

Append-only chronological notes. Keep each entry to 1–2 sentences.

## Entries
```

**memory/lessons.md**
```md
# Lessons Learned

Distilled insights, patterns, and gotchas that should influence future work.
Prefer updating an existing lesson over adding a near-duplicate.

## Lessons
```

**memory/issues.md**
```md
# Issues & Errors

Record of errors, failures, and self-heal outcomes so future runs can avoid or handle them.

## Issues
```

**memory/preferences.md**
```md
# Preferences & Rules

Stable preferences, coding style, communication style, and hard rules the agent should follow.

## Preferences
```

**memory/decisions.md**
```md
# Decisions

Important decisions with short rationale so future sessions understand context.

## Decisions
```

## 1. Query (start of every significant run)
Before acting, the agent must:

1. Ensure the memory files exist (create them with the templates above if missing).
2. Read the most relevant files:
   - `memory/issues.md` (especially important for self-heal workflows)
   - `memory/lessons.md`
   - `memory/preferences.md`
   - Recent entries in `memory/log.md`
3. Mentally filter for items that match the current task or potential failure points.
4. Explicitly note any applicable past issues, lessons, or preferences (or state “no relevant memory”).

If the workflow has a planning or self-heal step, include the retrieved memory so the agent can avoid known issues.

## 2. Act
Do the work normally. While working, especially notice:
- Errors, exceptions, and failures
- Self-heal attempts and whether they succeeded
- Non-obvious gotchas
- Approaches that worked especially well or failed
- User corrections (if any)

## 3. Build (end of every significant run — mandatory)
After the work is done (or when an error/self-heal occurs):

1. Append a short dated entry to `memory/log.md`:
   ```md
   - YYYY-MM-DD: [1–2 sentence summary of what was done and the outcome]
   ```

2. Distill any reusable knowledge:
   - Specific errors / failures / self-heal results → `memory/issues.md`
   - Broader lessons / patterns / gotchas → `memory/lessons.md`
   - Preferences / hard rules → `memory/preferences.md`
   - Important decisions → `memory/decisions.md`

   **Issue format** (for `issues.md`):
   ```md
   ### [Short error/issue title]
   - **When**: Situation or trigger
   - **Symptom**: What failed or what error appeared
   - **Resolution**: What fixed it (or “unresolved”)
   - **Avoidance**: How future runs should prevent or handle it
   - **First seen**: YYYY-MM-DD
   ```

   **Lesson format** (for `lessons.md`):
   ```md
   ### [Short title]
   - **When**: Situation or trigger
   - **Lesson**: What to do (or avoid)
   - **Why**: Reason or evidence
   - **Confidence**: low / medium / high
   - **First seen**: YYYY-MM-DD
   ```

3. Prefer updating an existing entry over creating a near-duplicate.
4. Keep everything short and actionable. Delete or mark outdated items.

## Integration pattern for any agent workflow
```text
┌────────────┐     ┌────────────┐     ┌────────────┐
│   Query     │────▶│    Act      │────▶│    Build    │
│  (read      │     │  (do the    │     │  (write     │
│   memory)   │     │   work)     │     │   memory)   │
└────────────┘     └────────────┘     └────────────┘
```

Especially useful for self-healing workflows:
- Query past issues before attempting a step that previously failed.
- After a self-heal, immediately record the issue + resolution so the next run can avoid or handle it faster.

### Minimal workflow pseudocode
```text
memory = query_memory(current_task)          # includes past issues
plan   = create_plan(task, memory)
result = execute(plan)
if error or self_heal:
    record_issue(error, resolution)
build_memory(result, observations)
```

## Quality rules
- Memory is curated, not a transcript dump.
- Prioritize recording issues and their resolutions so future runs do not face the same problem.
- Only store what will help a future run.
- The next run must be able to benefit from what was just written.

## When the user says (or the workflow detects)
- Error / failure / self-heal → immediately update `memory/issues.md`.
- “Remember this” / “Don’t do that again” → update the appropriate memory file.
- “What have you learned?” → summarize relevant lessons and issues.
- “Update memory” → run the Build step.
