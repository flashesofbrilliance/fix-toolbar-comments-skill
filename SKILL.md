---
name: fix-toolbar-comments
description: Fetch all open Vercel Toolbar comment threads for the current project, implement the requested fixes in code, commit, deploy to production, and resolve each thread. Use when someone says "handle the toolbar comments," "resolve the feedback threads," or similar.
---

# fix-toolbar-comments

Fetch all open Vercel Toolbar comment threads for the current project, implement the requested
fixes in code, commit, deploy to production, and resolve each thread.

## Step 1 – Load the Vercel toolbar tools

The Vercel MCP toolbar tools are typically deferred until requested. Before doing anything else,
load them (adjust the tool-search call to however your environment surfaces deferred tools):

```
select:mcp__vercel__list_toolbar_threads,mcp__vercel__get_toolbar_thread,mcp__vercel__change_toolbar_thread_resolve_status,mcp__vercel__reply_to_toolbar_thread
```

## Step 2 – Identify the project

Read `.vercel/project.json` to get `projectId`. This is required for all toolbar MCP calls.

## Step 3 – Fetch all open threads

Call `mcp__vercel__list_toolbar_threads` with the projectId. Filter to threads where
`resolved !== true`.

If there are no open threads, tell the user and stop.

## Step 4 – Read each thread

For each open thread, call `mcp__vercel__get_toolbar_thread` to get the full comment text,
screenshot context, and URL path where the comment was dropped.

Build a list of all requested changes. Group by file if you can infer which file is affected
from the URL path or comment content.

## Step 5 – Implement the fixes

Work through the fix list. Before editing any file, read it first.

Follow your project's own conventions (styling approach, dependency policy, etc.) – this skill
does not prescribe any; check your `CLAUDE.md`/`AGENTS.md` or equivalent for house rules before
making changes.

If a comment is ambiguous, make a reasonable call and note it. Don't stop to ask for every one;
batch any genuinely open questions for after the commit if needed.

## Step 6 – Build check

Run your project's typecheck/build command and confirm it passes. Fix any type errors before
proceeding.

## Step 7 – Commit

```
git add <changed files>
git commit -m "fix(toolbar): <summary of changes>"
```

## Step 8 – Deploy to production

```
vercel --prod
```

Wait for `READY` state.

## Step 9 – Resolve threads

For each thread that was addressed, call `mcp__vercel__change_toolbar_thread_resolve_status` to
mark it resolved.

If a fix was skipped or punted, reply to that thread with `mcp__vercel__reply_to_toolbar_thread`
explaining why before leaving it open.

## Step 10 – Summary

Report back:
- How many threads were resolved
- What was changed (file + description per fix)
- Deploy URL
- Any threads left open and why
