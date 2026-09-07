---
name: agent-overhaul
description: Use when the owner wants their existing AI agent upgraded to "AI employee" class (progress, interrupts, follow-ups, proactivity, memory recall, artefacts as files and links, model lanes, compounding skills, approvals with memory) without overwriting its memory, skills or code. Runs the Agent Overhaul Playbook phase by phase, additively, behind switches, with a ledger.
---

# Agent Overhaul

Run the Agent Overhaul Playbook on the agent whose repository you are in.

1. Read `PLAYBOOK.md` in this skill's directory in full before doing anything else. Part 1 is a binding contract: additive only, no edits to existing memory or skills, new code in one namespace, switches default OFF, commit by explicit path, never restart while the owner is working, never send to the owner from a test, never print a secret.
2. Create the ledger at `<namespace>/OVERHAUL-LEDGER.md`. If one already exists whose first line names this playbook, resume from it; do not repeat completed phases.
3. Phase 0: write the snapshot from the codebase, not from assumption.
4. Phase 1: grade every catalogue row HAS / PARTIAL / MISSING / N/A with an evidence pointer.
5. Phase 2: rank the gaps, propose a closing set, then STOP ONCE and ask the owner the intake questions in Appendix A. Record their answers as rulings.
6. Phases 3 to 5: build in the proven order, review for conflicts and redundancies, and prove every capability with a production row before calling it done.

Announce at start: "Using agent-overhaul to run the playbook on this agent."

The playbook describes designs, pitfalls and acceptance proofs. Write the code in this agent's language and conventions. Nothing here is copied from another agent.
