---
description: Review the current branch diff and report findings by severity
agent: reviewer
subagent: true
---

Review the changes on this branch against `origin/main`, or against the base
branch the repository declares.

$ARGUMENTS

If the argument names a brief file, read it and check the diff against its
acceptance criteria and its out-of-scope list. Otherwise review against the
repository `AGENTS.md`. Run the diff yourself and report findings only.
