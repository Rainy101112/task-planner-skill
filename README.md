# task-planner v2

A Pi-compatible Agent Skill for proportionate planning and persistent Markdown task tracking.

## What's new in v2

- Three planning levels: direct execution, lightweight planning, and persistent TODO.
- Persistent TODO files are reserved for substantial, risky, dependent, or long-running work.
- Separate task directories under `.agent/tasks/<slug>/` prevent unrelated active plans from colliding.
- Explicit guidance for concurrent agents: use one coordinator for shared TODO updates or separate worker notes.
- More efficient checkpoint-based updates instead of editing the file after every tiny action.
- Recovery reconciles TODO state with the actual working tree and fresh validation where needed.
- The plan is dynamic: user goals and repository reality outrank the original checklist.

## Install for Pi

Copy this directory to either:

- Global: `~/.pi/agent/skills/task-planner/`
- Project: `.pi/skills/task-planner/`

For a global install:

```bash
mkdir -p ~/.pi/agent/skills/task-planner
cp -R task-planner/* ~/.pi/agent/skills/task-planner/
```

Reload Pi (for example, with `/reload` in an active session), then invoke explicitly if desired:

```text
/skill:task-planner
```

The skill description also allows automatic selection for substantial multi-step work.

## Default file policy

- One active substantial task: `TODO.md` in the workspace root.
- Multiple unrelated active tasks: `.agent/tasks/<short-task-slug>/TODO.md` per task.
- Existing TODO files are inspected and preserved; they are never blindly overwritten.

The Skill does not require a dedicated TODO plugin or special Harness API. It uses ordinary workspace file operations available to the agent.
