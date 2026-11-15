---
description: Build a repo profile for the agent workflow, optionally persisting it
agent: build
---

Build the repo profile for the current repository.

Read, in this order: `AGENTS.md` (look for `## Agent commands`, then
`## Commands`), `README.md` or `RUNBOOK.md`, then the project's build manifest
and dependency file, whatever the stack calls them.

Report exactly this shape and nothing else:

```
scopes:   <path → stack → build system>, one per line
verify:   build / test / lint commands
run:      how the UI is observed (URL, simulator, n/a)
contract: shared schema path and its regenerate command, or none
tests:    present | absent
danger:   commands that must never run here
source:   agent-commands | readme | manifest-inference | asked-user
```

Mark any field you cannot establish as `unknown`. Never guess a command, a stack,
or a path.

If the argument is `write`, also add a `## Agent commands` section to this
repository's `AGENTS.md` containing that same block, and change nothing else in
that file.
