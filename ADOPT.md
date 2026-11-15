# Adopt this workflow

This repository is the source of truth. The rules are in `AGENTS.md`, the roles in
`agents/`, the commands in `commands/`, the issue skill in `skills/`.

## What to do

1. Read this repository. It is small on purpose, and nothing here is a suggestion.
2. Clone it somewhere stable on this machine.
3. Bind your agent to the clone: what your agent loads as instructions must be these
   files, so that a change committed here is the change your agent follows. How that is
   done depends on your harness — use its own mechanism for pointing at external
   instructions.
4. Leave a note of where the clone lives, so the next session does not have to find it
   again.

## What must not happen

Do not copy these files into your agent's configuration: a copy is correct the day it is
made and silently wrong after the next commit. Do not rewrite them to fit your harness.
Do not move them inside the clone to match a layout your harness expects. The mapping
runs from this clone to the harness, never back.

If your harness cannot follow a pointer and the only way forward looks like a copy, stop
and say so. That is a decision for the owner, not a workaround.

## When you are done

The rules your agent follows are the files in this clone, and there is no second place
where they could be edited.
