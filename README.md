# Agent Overhaul Playbook

**Viktor without the credit cost.** Upgrade the AI agent you already have to "AI employee" class without rewriting it, overwriting its memory, or making a mess.

Viktor (viktor.com) is the reference product this playbook was measured against: a Slack-native AI employee that watches, proposes, delivers files and pages, picks the model, and bills per credit. Everything it does for a team, your own agent can do for you on a flat rate, and this is the build list. Not affiliated with Viktor or Zeta Labs.

## Two ways to use it

**Any coding agent (Claude Code, Codex, Cursor, OpenClaw, custom).**
Upload `AGENT-OVERHAUL-PLAYBOOK.md` into a session opened inside your agent's repository and say:

> Run the Agent Overhaul Playbook on this agent. Start at Phase 0. Do not skip the contract in Part 1.

**Claude Code users, as a skill.**
Copy `skill/agent-overhaul/` into your skills folder (usually `~/.claude/skills/agent-overhaul/`) and, inside your agent's repository, say:

> /agent-overhaul

## What happens

1. The executing agent reads your agent's code and writes a snapshot and a capability inventory.
2. It pauses once to show you the ranked gap list and ask ten intake questions.
3. It builds the closing set you chose, additively, behind switches, with tests, and proves each capability live.
4. You get a report naming every file touched and every switch to flip.

## What it never does

Edits your agent's memory or skills. Restarts your agent while you are using it. Messages anyone but you. Prints a secret. Automates money or destructive actions.

## Contributing

Pull requests that add a pattern card, a pitfall you hit, or a stack note to Appendix D are welcome. Never commit a ledger, a snapshot of a real agent, credentials, hostnames or chat ids. The `.gitignore` blocks the obvious files; the rule covers everything else.

## License

MIT. See `LICENSE`.

## Contents

- `AGENT-OVERHAUL-PLAYBOOK.md`: the playbook.
- `skill/agent-overhaul/SKILL.md` and `PLAYBOOK.md`: the same playbook packaged as a Claude Code skill.

This document contains no credentials, hostnames, chat ids or business data. Keep it that way when you share your own ledger or report.
