# agentic-workflow

This repository is the source of truth for how work is planned, tracked, delegated and
shipped. The rules, the roles, the commands and the skills live here; the agent
configuration on a machine points at these files instead of holding copies of them.

## What is inside

| Path | What it is |
| --- | --- |
| `AGENTS.md` | The rules |
| `agents/` | The roles |
| `commands/` | The commands |
| `skills/` | The skills |
| `ADOPT.md` | How to bind a machine to this clone |

## How to adopt it

- Clone this repository.
- Read `ADOPT.md` and follow it with your agent; it binds that machine's configuration to
  this clone and records what it did.
- From then on, edit here and pull: every adopted machine reads the edit.

## Why pointers, not copies

A copy is right the day it is made and silently wrong after the next commit, because the
agent keeps following the stale one. A broken pointer is loud and repairable, naming the
file that must be fixed instead of hiding a divergence. And there is one place where a
rule can be edited.

## Nothing to keep in sync

There is no local fact file. The repository list, the assignee, the issue types and the
labels are read from GitHub at the start of a run, so a new repository or a renamed label
needs no edit here.
