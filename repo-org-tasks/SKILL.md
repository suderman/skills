---
name: repo-org-tasks
description: >
  Maintains a per-repository Org project file at ~/org/projects/<slug>.org.
  Use during substantive repository work, including implementation, debugging,
  refactoring, actionable reviews, and explicit project ideas. Creates or
  updates individual top-level task threads, scopes the active work, tracks its
  checklist and follow-ups, records verification, and keeps TODO state current.
  Skip quick informational questions, read-only lookups, isolated commands, and
  trivial edits unless the user asks to track them.
license: MIT
---

# Repo Org Tasks

Keep the user's Org project file in step with substantive repository work. The
file is a compact working record, not a transcript and not an agent scratchpad.

## Activation

Use this skill automatically for substantive work in a repository unless the
user opts out. Keep it active through follow-up requests in the same work
thread, then synchronize the project file before the final report.

Skip quick explanations, read-only lookups, isolated commands, and trivial
edits unless the user explicitly asks to track them.

## Non-negotiable model

- One Org file represents one repository.
- The file preamble holds repository context.
- Every first-level heading is one task, idea, bug, feature, or continuing work
  thread.
- Never create a broad first-level heading such as "Maintain this repository"
  and put unrelated work beneath it.
- Preserve user-written headings and prose. Extend the matching thread instead
  of replacing it or creating a duplicate.
- Track all meaningful tasks discovered within the active work thread. Split an
  independent follow-up into its own first-level heading.

## Find the project file

1. Honor an explicit repository or project file named by the user.
2. Otherwise use the nearest enclosing Git root for the work being performed.
3. Use the repository directory's basename as the default slug.
4. Prefer an existing project file whose local repository link resolves to that
   root, even if its filename differs from the default slug.
5. If there is no repository, do not invent a project file unless the user
   explicitly identifies the work as a project.

The default path is:

```text
~/org/projects/<slug>.org
```

Read the existing file before every edit. If it changed during the session,
preserve and merge the new content. Stop and ask only when concurrent edits
conflict with the same thread.

## Create the preamble

Create the file only when substantive work begins or the user asks to capture
an idea. Use this shape:

```org
#+TITLE: Human-readable repository name
#+CATEGORY: short-name

Repository: [[file:../../path/to/repository/][local]] | [[https://github.com/owner/repository][GitHub]]
```

- Make the local link relative to the project file so it remains portable with
  the user's home directory.
- Read the `origin` remote when available. Normalize GitHub SSH and HTTPS URLs
  to `https://github.com/owner/repository` and remove a trailing `.git`.
- Omit the GitHub link when the repository has no GitHub remote. Never fabricate
  one.
- Always add `#+CATEGORY`. Use the slug unchanged when it is clear and at most
  10 characters. Otherwise choose a recognizable category of at most 10
  characters. Ask only when no clear short label exists.
- Preserve an existing title, category, links, and other file metadata unless
  they are missing or demonstrably stale.

## Task states

Use the user's Org workflow consistently:

- `TODO`: captured but not started.
- `PROG`: active investigation or implementation.
- `EVAL`: implementation is ready but awaits review, acceptance, or external
  validation.
- `HOLD`: blocked by a concrete dependency or decision.
- `DONE`: requested work is complete and verified.

Verification, not a commit or push, is the threshold for `DONE`. Do not leave a
thread in `PROG` merely because the source changes are uncommitted. Never mark a
required unchecked item complete without evidence. If unfinished work remains,
keep the thread open or split the remaining work into a new top-level task.

## Start or resume work

At the beginning of substantive work:

1. Find a semantically matching first-level thread before creating one.
2. If the task is new, add a concise verb-led heading and mark it `PROG`.
3. If a matching `TODO`, `EVAL`, or `HOLD` thread is now active, move it to
   `PROG`.
4. Add a short `Scope` section that states the intended outcome and important
   constraints.
5. Add or update a `Checklist` early enough that the user can see the real work
   unfold. Include investigation, implementation, verification, and known
   follow-ups only when they are meaningful to this task.

Example:

```org
* PROG Fix duplicate webhook delivery

** Scope
Prevent retries from applying the same delivery twice without changing the
provider-facing response contract.

** Checklist
- [X] Reproduce the duplicate write and trace its call path.
- [ ] Add idempotency at the shared persistence boundary.
- [ ] Verify first delivery, retry, and concurrent delivery behavior.
```

Do not pad the checklist with routine mechanics such as reading files, invoking
tools, or announcing plans. The list should explain the work, not the agent.

## Maintain the active thread

- Update the checklist at meaningful transitions rather than after every small
  action.
- Record a decision when its rationale will matter in a later session.
- Add `Context`, `Decisions`, `Verification`, or `Outcome` sections only when
  they contain useful information.
- Add a dated `Session YYYY-MM-DD` section only for multi-session work,
  handoffs, or chronology that materially helps continuation.
- Capture a newly discovered task as a separate top-level `TODO` when it is not
  required to finish the current thread.
- Keep failed hypotheses only when they prevent likely repetition. Do not turn
  the file into a debugging log.
- Keep paths, command names, issue links, pull requests, and commit hashes when
  they make the thread easier to resume.
- Never put credentials, tokens, private keys, personal data, or secret command
  output in the project file.

Manually added ideas are first-class input. When the user starts one, retain
their wording where practical, set the appropriate state, and grow it with
scope and checklist sections. Do not alter unrelated manual ideas.

## Finish and synchronize

Before reporting completion:

1. Re-read the active thread and source state.
2. Check only items supported by completed work or verification.
3. Record the focused tests, build, manual check, or other evidence that proves
   the outcome.
4. Set the heading to `DONE`, `EVAL`, or `HOLD` according to its actual state.
5. Add a separate top-level `TODO` for independent residual work.
6. Remove stale statements such as "pending commit" after that event occurs.
7. If the session creates a commit or pull request, record its identifier after
   success. Do not make commits a prerequisite for `DONE`.

Do not claim the project record is synchronized until this final pass is done.

## Git boundary

The Org tree is tracking state, not part of the repository being changed.

- Never run Git commands in `~/org`.
- Never stage, commit, push, reset, clean, or otherwise manage the `~/org`
  repository.
- Git operations in the source repository remain governed by the user's normal
  instructions.
- Editing `~/org/projects/<slug>.org` does not grant permission to modify other
  Org files.

## Restraint

If untracked work reveals a real follow-up, capture it only when the user asks
or when it clearly belongs to an existing active thread. Prefer a small
accurate update over a ceremonial project-management document.
