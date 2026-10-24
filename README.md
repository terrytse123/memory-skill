# Memory Skill (Workflow-Agnostic)

A portable memory system that lets any AI agent **learn and improve across runs** using a simple **query → act → build** loop.

The agent:
1. **Queries** relevant past experience before starting work
2. **Acts** on the task
3. **Builds** new memory so the next run is better

Memory files are created automatically on first use if they do not exist.

## Repository structure

```
.github/skills/memory/SKILL.md   ← Skill instructions (copy this)
memory/                          ← Created automatically by the agent
├── log.md                       ← Dated session notes (episodic)
├── lessons.md                   ← Distilled insights & patterns
├── preferences.md               ← Stable preferences & rules
└── decisions.md                 ← Important decisions + rationale
```

## How to add this skill to another repository

### Step 1: Copy the skill file
Copy `.github/skills/memory/SKILL.md` into your project.

Common locations:
- Claude Code: `.claude/skills/memory/SKILL.md`
- General / other tools: `.github/skills/memory/SKILL.md`

You do **not** need to copy the `memory/` folder. The agent will create it.

### Step 2: Add the Memory Protocol to your agent
Paste the following block into the agent’s main instruction file (`CLAUDE.md`, `AGENTS.md`, system prompt, etc.):

```md
## Memory Protocol
Before any significant task:
- Ensure the memory files exist (create them if missing, following the memory skill).
- Read memory/lessons.md, memory/preferences.md, and recent entries in memory/log.md.
- Use any relevant items in your plan.

After the task (or when the user asks to remember something):
- Append a 1–2 sentence dated entry to memory/log.md.
- Update memory/lessons.md, memory/preferences.md, or memory/decisions.md with any reusable knowledge.
- Follow the formats defined in the memory skill.
```

### Step 3: Use it
Run your agent normally. On the first significant task it will create the memory files. On every subsequent run it will query them at the start and update them at the end.

## What the agent does on each run

| Phase | When | What happens |
|-------|------|--------------|
| **Query** | Start of task | Creates missing memory files if needed, then reads lessons, preferences, and recent log |
| **Act** | During work | Does the task while noticing corrections, gotchas, and patterns |
| **Build** | End of task | Writes a short log entry and distills reusable knowledge into the appropriate file |

## Memory file formats

### `memory/log.md`
```md
- YYYY-MM-DD: Short summary of what was done and the outcome.
```

### `memory/lessons.md`
```md
### Short title
- **When**: Situation or trigger
- **Lesson**: What to do (or avoid)
- **Why**: Reason or evidence
- **Confidence**: low / medium / high
- **First seen**: YYYY-MM-DD
```

### `memory/preferences.md` and `memory/decisions.md`
Plain markdown lists or short dated notes.

## Tips for good results
- The Memory Protocol instruction is required. Without it the agent has the skill but will not use it automatically.
- Prefer updating existing lessons over creating near-duplicates.
- Keep entries short and actionable.
- Periodically review `lessons.md` and delete low-value or outdated items.

## Example first run
1. Agent sees the Memory Protocol.
2. Memory files do not exist → agent creates the four template files.
3. Agent does the task.
4. Agent appends a log entry and adds any lessons or preferences it discovered.
5. Next run starts by reading those files.

That’s it. Each completed run improves the memory for the next one.
