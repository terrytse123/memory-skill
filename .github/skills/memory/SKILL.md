---
name: memory
description: "Portable memory skill for any agent workflow. Use to query relevant past experience before a run and to build/update memory after a run so the next execution improves. Triggers: start of task, end of task, user correction, 'remember this', 'what have you learned'."
---

# Memory Skill (Workflow-Agnostic)

This skill gives any agent a simple **query → act → build** memory loop so experience compounds across runs.

## Memory layout (relative to project root)
```
memory/
├── log.md              # Chronological session notes (episodic)
├── lessons.md          # Distilled insights & patterns
├── preferences.md      # Stable preferences & rules
└── decisions.md        # Important decisions + rationale
```

## 1. Query (start of every significant run)
Before acting, the agent must:

1. Read the most relevant files:
   - `memory/lessons.md`
   - `memory/preferences.md`
   - Recent entries in `memory/log.md`
2. Mentally filter for items that match the current task.
3. Explicitly note any applicable lessons or preferences (or state “no relevant memory”).

If the workflow has a planning step, include the retrieved memory in the plan.

## 2. Act
Do the work normally. While working, notice:
- User corrections
- Non-obvious gotchas
- Approaches that worked especially well or failed
- New preferences or decisions

## 3. Build (end of every significant run — mandatory)
After the work is done (or when the user asks to remember something):

1. Append a short dated entry to `memory/log.md`:
   ```md
   - YYYY-MM-DD: [1–2 sentence summary of what was done and the outcome]
   ```

2. Distill any reusable knowledge into the appropriate file:
   - Lessons / patterns / gotchas → `memory/lessons.md`
   - Preferences / hard rules → `memory/preferences.md`
   - Important decisions → `memory/decisions.md`

   Lesson format:
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

### Minimal workflow pseudocode
```text
memory = query_memory(current_task)
plan   = create_plan(task, memory)
result = execute(plan)
build_memory(result, observations, corrections)
```

## Quality rules
- Memory is curated, not a transcript dump.
- Only store what will help a future run.
- The next run must be able to benefit from what was just written.

## When the user says
- “Remember this” / “Don’t do that again” → immediately update the appropriate memory file.
- “What have you learned?” → summarize relevant lessons and preferences.
- “Update memory” → run the Build step.
