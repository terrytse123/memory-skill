# Memory Skill (Workflow-Agnostic + Self-Heal)

A portable memory system that lets any AI agent **learn and improve across runs** using a simple **query → act → build** loop.

Especially useful for **non-interactive / self-healing workflows**: the agent records issues and their resolutions so the next run can avoid or handle the same problem.

The agent:
1. **Queries** relevant past experience (including past issues) before starting work
2. **Acts** on the task
3. **Builds** new memory (especially errors and self-heal results) so the next run is better

Memory files are created automatically on first use if they do not exist.

## Repository structure

```
.github/skills/memory/SKILL.md   ← Skill instructions (copy this)
memory/                          ← Created automatically by the agent
├── log.md                       ← Dated session notes (episodic)
├── lessons.md                   ← Distilled insights & patterns
├── issues.md                    ← Errors, failures, and self-heal resolutions
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
- Read memory/issues.md, memory/lessons.md, memory/preferences.md, and recent entries in memory/log.md.
- Use any relevant items (especially past issues) in your plan.

After the task (or when an error / self-heal occurs):
- Append a 1–2 sentence dated entry to memory/log.md.
- Update memory/issues.md with any errors and their resolutions.
- Update memory/lessons.md, memory/preferences.md, or memory/decisions.md with any other reusable knowledge.
- Follow the formats defined in the memory skill.
```

### Step 3: Use it
Run your agent normally. On the first significant task it will create the memory files. On every subsequent run it will query them at the start and update them at the end. Errors and self-heal results are recorded so the next run can avoid the same issue.

## What the agent does on each run

| Phase | When | What happens |
|-------|------|--------------|
| **Query** | Start of task | Creates missing memory files if needed, then reads issues, lessons, preferences, and recent log |
| **Act** | During work | Does the task while noticing errors, self-heal attempts, gotchas, and patterns |
| **Build** | End of task (or on error) | Writes a short log entry and records issues/resolutions plus any lessons |

## Memory file formats

### `memory/log.md`
```md
- YYYY-MM-DD: Short summary of what was done and the outcome.
```

### `memory/issues.md` (for errors & self-heal)
```md
### Short error/issue title
- **When**: Situation or trigger
- **Symptom**: What failed or what error appeared
- **Resolution**: What fixed it (or “unresolved”)
- **Avoidance**: How future runs should prevent or handle it
- **First seen**: YYYY-MM-DD
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
- For self-healing workflows, always record the issue + resolution in `issues.md` after a failure or heal.
- Prefer updating existing entries over creating near-duplicates.
- Keep entries short and actionable.
- Periodically review `issues.md` and `lessons.md` and delete low-value or outdated items.

## Example first run (with an error)
1. Agent sees the Memory Protocol.
2. Memory files do not exist → agent creates the five template files.
3. Agent does the task and hits an error.
4. Agent records the error + resolution in `issues.md` and adds a log entry.
5. Next run starts by reading `issues.md` and can avoid or handle the same problem.

That’s it. Each completed run (especially ones with errors or self-heals) improves the memory for the next one.
