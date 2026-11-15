---
name: GitHub Issues
description: Read and file work as plain GitHub issues over MCP — list what is open on a repo, open fully specified issues (assignee, type, labels, title, body), and take an issue from branch to merged PR. Use whenever the owner mentions a task, an issue key like `<Repo>#<n>`, a repo backlog, or asks what is left to do.
---

# GitHub Issues

The owner tracks every code project in plain GitHub issues, through the
`github` MCP server plus the `gh` CLI, without opening the GitHub UI. There is
no board, no project, no status field: an issue is the unit of work, and
closing it is the only progress signal. Follow the mapping below instead of
inventing a structure.

## Model

One **repository per code project**. Work lives in that repository's issues.

| Concept | GitHub entity |
| --- | --- |
| project (`<Repo>`, …) | a **repository** |
| task | an **issue** in that repository |
| issue key | `<Repo>#<n>` |
| kind of work | the issue's native **type**: `Bug`, `Feature`, `Task` |
| attributes | **labels** on the issue |
| status | **open** or **closed** — nothing else |

Every issue we open is fully specified: assignee, type, labels, title, body.
Never a title alone.

The key is unique per repository, so the repo name is the prefix: `<Repo>#12`
cannot collide with `<OtherRepo>#12`. Issues and pull requests share one counter
per repo, so numbers have gaps — always read the number back, never predict it.

Resolve the concrete facts at the start of a run, from GitHub itself: the account's
repository list, the account's own login as the assignee, the repository's issue
types, and the labels it already has. Never carry a stale list of repositories or
labels.

Connection is per machine: a client, an authenticated account, and a scope that can
write issues. If you cannot reach GitHub, ask the owner once how to connect, name
exactly what you need, and do not guess.

## Trigger intents

The owner writes in Italian. Any paraphrase of the intents below counts, in
either language; never require exact wording.

| Intent | Example |
| --- | --- |
| read open issues of one repository | what is left on `<Repo>`? |
| read every open issue | what is left? / what did I open? |
| read recently closed issues | what did I finish? / what is closed? |
| create an issue | add a task: … / open an issue: … |
| close an issue | `<Repo>#<n>` is done / close `<Repo>#<n>` |
| start an issue | let's work on `<Repo>#<n>` / do `<Repo>#<n>` |
| bulk close | mark all the … done, confirm the exact list first |

## Reading what is open

1. Resolve the repo from the account's repository list.
2. One repo: `list_issues` with `state: OPEN`, `orderBy: UPDATED_AT`,
   `direction: DESC`, and the field subset `number,title,labels,assignees`.
3. No repo named: `gh search issues --assignee @me --state open --limit 50`.
4. Answer as a compact list, one line per issue:
   `<Repo>#<n> Bug — fix three layout defects`. Include key, type, title.
   Say explicitly when nothing is left.

Add nothing that is not in GitHub. Never infer progress from a title, and never
report an issue as started because a branch exists.

## Filing an issue

Every create sets all five fields. A create missing any of them is a defect of
the run, not a shortcut.

1. **Assignee** — always the account's own login. Pass it explicitly; never
   leave it empty.
2. **Type** — pick from the repo's native types (`list_issue_types`):

   | Request | Type |
   | --- | --- |
   | something broken: crash, regression, wrong output, broken layout | `Bug` |
   | new behaviour: screen, endpoint, command, capability | `Feature` |
   | refactor, tests, docs, deps, CI, build, config, tooling, chore | `Task` |

   A request that mixes kinds becomes several issues, one per kind — never a
   `Task` with a bug buried inside. If the repo has issue types disabled, omit
   the type and say so in the reply.
3. **Labels** — at most two, only labels the repo already has
   (`gh label list -R <owner>/<repo>`):
   - the repo's type-label mirror, where it exists (`fix`, `feature`,
     `refactor`) — it must agree with the type;
   - one area label when one genuinely fits (`documentation`, `enhancement`,
     `bug`, `good first issue`, `help wanted`).

   Never invent a label and never create one unasked. If nothing fits, ship the
   type-only issue and mention that in the reply.
4. **Title** — short and intuitive. Imperative mood, at most ~60 characters, no
   type prefix (the type and labels carry it), no trailing period, no ticket
   noise. Optional `[BE]` / `[FE]` scope prefix only when the repo has both sides
   and the scope is not obvious from the title.
5. **Body** — explicit but short: 3 to 8 lines. One paragraph saying what has to
   change and why in plain language, then a `Done when` bullet list of two to
   four observable criteria when there is more than one. No template headings, no
   process checklists, no restating the title, no code dumps.

Example of a complete issue:

```
title:  Surface the server's validation message instead of a fixed toast
type:   Bug
labels: fix
assignee: <assignee>

Saving an invalid amount shows "Something went wrong", so the user cannot tell
what to correct. The API already returns a readable message in `detail`.

Done when
- the toast shows the server's message when the response carries one
- the generic message stays only for network failures
```

Search before creating: `search_issues` or `gh search issues` on the same words.
Report the existing key instead of duplicating. Reply with the created key and a
one-line summary; no confirmation for a plain create.

## Working an issue

The PR closes the issue, and only a merge closes work delivered through a PR.

1. Branch from an up-to-date `main`: `<type>/<n>-<slug>`, where `<type>` is
   `feat`, `fix`, `refactor`, `chore` or `docs` and `<n>` is the issue number.
2. PR title mirrors the issue title. In the PR **body** put `Closes #<n>` —
   the keyword must be in the body, not only the title, or the issue stays open.
3. Merge once checks are green; the merge closes the issue. Never close by hand
   an issue whose work shipped through a PR.
4. If an issue ended up closed without a closing keyword, say that the automation
   did not fire instead of silently moving on.
5. Nothing tracks "in progress" any more: an open issue plus an open PR is the
   state. Do not post "started working" comments.
6. A task that needs several PRs becomes a parent issue with `sub_issue_write`
   children, one per deliverable, not one oversized issue.

## Closing an issue

`issue_write` with `method: update`, `state: closed` and a `state_reason`:
`completed` for delivered work, `not_planned` for dropped work, `duplicate` with
`duplicate_of` for a duplicate. Closing is an explicit action, never a side
effect of something else.

## Asking when the request is incomplete

The owner often gives partial instructions. Do not guess and do not silently narrow
the result: ask for what is missing, in **one** round, with concrete options.
`question` takes several questions per call, so batch them.

| Missing | Ask |
| --- | --- |
| repo, when more than one is plausible | the repo, listing the candidates |
| repo that does not exist | offer to create it (a decision, not an inference) |
| type, when it changes the type and the labels | Bug / Feature / Task |
| which issue, when the reference is vague ("the label fix") | the matching open issues, as options |
| scope of a bulk request | the exact list of keys that will close, before closing |

Use a safe default and state it instead of asking when being wrong costs nothing:
a read without a repo and with one obvious candidate → that repo; "what is left"
→ open issues only; "what is closed" → recently closed only.

Never ask twice for the same thing, never ask for information already in GitHub,
never ask about a detail that does not change the outcome.

## Hard rules

- No projects, no boards, no status fields. Work lives in issues; do not create
  or update a Project as a side effect of filing or working an issue.
- Never create an issue with only a title: assignee, type, labels, title and body
  always land together.
- Never invent a label, an issue type or a body template; verify against
  `gh label list` and `list_issue_types` first.
- Do not rewrite titles, bodies, types or labels of existing issues as a side
  effect of another request. Issues filed before this skill keep their
  labels-only shape and are not retrofitted.
- Never delete issues or branches you did not create.
- Never merge with failing checks, never force-push `main`.
- Report failures verbatim (missing repo, ambiguous name, missing token scope,
  MCP not authenticated) instead of retrying with guesses.

## Tools

Reads and issue writes go through the `github` MCP server: `list_issues`,
`search_issues`, `issue_read`, `issue_write` (create and update, including
`type`, `labels`, `assignees`), `list_issue_types`, `list_issue_fields`,
`sub_issue_write`, `add_issue_comment`. The `projects_*` tools still exist but are
out of scope now — do not reach for them. Confirm tool names against the tool
catalog before the first call in a session.

CLI equivalents: `gh issue list -R <owner>/<repo> --state open`,
`gh issue create -R <owner>/<repo> --title … --body-file … --type Bug
--label fix --assignee @me`, `gh issue close <n> --reason completed`,
`gh search issues --assignee @me --state open`, `gh label list -R <repo>`.

If the MCP server is unavailable (daemon down, expired token), do not report the
backlog as broken: fall back to the CLI for the same operation and mention that
the MCP path was down.
