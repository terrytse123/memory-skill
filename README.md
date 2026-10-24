# Memory Skill Starter (Workflow-Agnostic)

A portable memory system that any agent workflow can use with a simple **query → act → build** loop.

## Structure
```
.github/skills/memory/SKILL.md   ← Skill instructions
memory/
├── log.md                       ← Dated session notes
├── lessons.md                   ← Distilled insights
├── preferences.md               ← Stable preferences & rules
└── decisions.md                 ← Important decisions + rationale
```

## How any agent workflow should use it

### At the start of a run (Query)
1. Read `memory/lessons.md`, `memory/preferences.md`, and recent `memory/log.md`.
2. Extract anything relevant to the current task.
3. Include those items in the plan or context.

### During the run (Act)
Work normally. Notice corrections, gotchas, and successful patterns.

### At the end of a run (Build)
1. Append a short dated summary to `memory/log.md`.
2. Distill reusable knowledge into `lessons.md`, `preferences.md`, or `decisions.md`.
3. Prefer updating existing entries over creating duplicates.

## Adding it to an agent

### Generic (any agent that can read files)
Add this to the agent’s system / instruction prompt:

```md
## Memory Protocol
Before any significant task:
- Read memory/lessons.md, memory/preferences.md, and recent entries in memory/log.md.
- Use any relevant items.

After the task (or when the user asks to remember something):
- Append a 1–2 sentence dated entry to memory/log.md.
- Update memory/lessons.md, memory/preferences.md, or memory/decisions.md with any reusable knowledge.
- Follow the format defined in the memory skill.
```

### Claude Code / agents that support skills
1. Copy `.github/skills/memory` to `.claude/skills/memory` (or the tool’s skills folder).
2. Add the Memory Protocol block above to `CLAUDE.md` / `AGENTS.md`.

## Getting started
1. Place this structure in your repository.
2. Add the Memory Protocol instruction to your agent.
3. Run normally. Each completed run improves the memory for the next one.
