# fix-toolbar-comments

A [Claude Code](https://claude.com/claude-code) skill that clears your Vercel Toolbar feedback
queue end to end: fetch every open comment thread, implement the requested fix, build, commit,
deploy to production, and resolve (or explain) each thread.

## Why it exists

Vercel's [Toolbar](https://vercel.com/docs/vercel-toolbar) lets anyone drop a comment directly on
a live preview or production page. That's a great way to collect feedback and a tedious one to
process: open each thread, figure out what it's asking for, find the file, make the change,
re-deploy, then go back and resolve the thread. This skill does the whole loop in one pass instead
of one thread at a time.

## Install

```bash
git clone https://github.com/flashesofbrilliance/fix-toolbar-comments-skill.git ~/.claude/skills/fix-toolbar-comments
```

Or project-scoped:

```bash
git clone https://github.com/flashesofbrilliance/fix-toolbar-comments-skill.git .claude/skills/fix-toolbar-comments
```

Requires the [Vercel MCP server](https://vercel.com/docs/mcp) connected in your Claude Code
environment, and a project with `.vercel/project.json` present (i.e. linked via `vercel link`).

## How to use

```
fix the toolbar comments
```

or invoke it by name if your setup supports slash-style skill invocation:

```
/fix-toolbar-comments
```

## What it does

1. Loads the Vercel toolbar MCP tools.
2. Reads `.vercel/project.json` for the project ID.
3. Fetches every unresolved comment thread.
4. Reads each thread's text, screenshot context, and URL path to build a fix list.
5. Implements each fix (reading the affected file first).
6. Runs your build/typecheck command.
7. Commits.
8. Deploys to production and waits for `READY`.
9. Resolves each addressed thread; replies with a reason on any it leaves open.
10. Reports a summary: threads resolved, what changed, deploy URL, anything left open.

## Use cases

- **Design review sweep.** A designer drops ten comments across a preview deploy pointing out
  spacing, copy, and alignment issues. Run this once instead of processing them one at a time.
- **Stakeholder feedback day.** Non-technical reviewers leave comments directly on the live site.
  This turns that feedback into shipped fixes without anyone translating it into a ticket first.
- **Pre-launch punch list.** Before a public launch, comments accumulate on a staging URL. Clear
  the whole queue in one deploy instead of many small ones.

## What it does not do

It does not invent conventions for your project – it defers to whatever styling and dependency
rules your own `CLAUDE.md`/`AGENTS.md` (or equivalent) already state. It also does not silently
resolve a thread it couldn't confidently address; ambiguous or skipped items get a reply
explaining why, so nothing disappears without a trace.

## License

MIT. See [LICENSE](./LICENSE).
---

## Part of the ARCS family

An open, MIT-licensed tool in the [flashesofbrilliance](https://github.com/flashesofbrilliance) / ARCS family — small, composable, provenance-carrying. The tools are open; the ARCS intelligence that orchestrates them is private.
