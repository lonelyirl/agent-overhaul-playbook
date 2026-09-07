# The Agent Overhaul Playbook

**Viktor without the credit cost. Give your existing agent the capabilities of a Viktor-class "AI employee" without rewriting it, overwriting its memory, or making a mess.**

Version 1.1, 2026-09-05. Distilled from a real overhaul: a from-scratch study of Viktor (viktor.com) across its site, ads, YouTube, Reddit, X, LinkedIn, G2, Product Hunt and press, a 138-row capability inventory, and the build that closed the gaps on a live 24/7 personal agent while its owner kept using it. Works with any coding agent that can read files, run commands and commit (Claude Code, Codex, Cursor, OpenClaw, a custom harness). Stack-specific shortcuts are confined to Appendix D.

---

## Part 0. For the human: how to use this file

1. Open a coding-agent session **inside your agent's repository** (the folder that holds the agent's code). Any capable coding agent that can read files, run commands and commit works.
2. Upload or paste this whole file and say:

   > Run the Agent Overhaul Playbook on this agent. Start at Phase 0. Do not skip the contract in Part 1.

3. The executing agent will read your codebase, write a snapshot and an inventory, and then **stop once** to show you the ranked gap list and ask the intake questions in Appendix A. Answer them. Everything after that runs without needing you, except where the playbook says a change needs your press.
4. Expect the work to span several sessions. The agent keeps a ledger file so a new session resumes where the last one stopped.

**Safety of this document.** It contains no credentials, hostnames, chat ids or business data, and it must stay that way: when the executing agent writes its ledger and reports, it records key **names**, never values, and it never pastes message content or customer data into a document that might be shared.

What you get at the end: your agent, with the same code, memory, skills and personality it had before, plus a set of clearly separated additions, each behind a switch, each with tests, each proven live, and a report that names every file touched.

What you do not get: a copy of anyone else's agent. Every agent has its own stack, surfaces and habits. This playbook teaches the executing agent **what to build and how to build it without breaking what is there**. The code is written fresh, in your agent's language and style.

---

## Part 1. The contract (binding on the executing agent)

Read this before touching anything. These rules exist because each one was learned the expensive way.

### 1.1 Additive, never destructive

- **Never modify an existing memory entry, skill file, persona file or system prompt.** Read them. Reference them. Do not edit them. If a capability needs the agent to know something new, add a new file or a new row through the agent's own memory API, and record what you added in the ledger.
- **Never overwrite an existing `SKILL.md`** or equivalent. A new skill gets a new directory. If a skill with that name already exists, stop and choose another name.
- **New code lives in a namespace.** At Phase 0 you choose one location for new modules (a new folder such as `capabilities/`, or the agent's existing module folder with a consistent prefix). Every new file goes there. Wiring into existing code is done at the **fewest possible call sites**, each marked with a comment naming this playbook and the date.
- **New database tables only.** Prefix them. Create them idempotently (`CREATE TABLE IF NOT EXISTS`) from the module that owns them. Never rename or drop an existing table or column. An additive column on an existing table is allowed only behind an idempotent guard, and only when a new table genuinely cannot do the job.
- **New configuration is namespaced and defaults OFF.** Every new behaviour that speaks to the owner or acts on the world sits behind a switch (a key-value flag, an env var, or both). The agent must behave exactly as before until the owner flips a switch.
- **Commit by explicit path.** Never `git add -A`, `git add .`, or `git add -u`. Stage the files you changed, by name, and nothing else. One capability per commit where possible.

### 1.2 The running agent is production

- **Discover how the agent is supervised before you change anything** (PM2, systemd, launchd, Docker, a shell loop, nothing). Write it down.
- **Never restart the agent while its owner is using it.** If the agent has no safe-restart path (one that waits for in-flight work to finish), build one first (see 4.1) and use only that.
- **Never send anything to the owner from a test, a script you are iterating on, or a dry run.** Every send path you build is dry-run by default with an explicit `--send`. Test transports must refuse under the test runner.
- **The owner is the only recipient.** Nothing you build messages a third party, a client channel or a teammate. If a capability could reach outside, it is gated to the owner until they say otherwise.
- **Never print a secret value.** Names of keys only. Never copy a credential into a log, a commit, a doc or a message.
- **Financial and destructive actions stay read-only or behind a press.** No refunds, payments, deletes, force-pushes or bulk changes are ever automated by this playbook.

### 1.3 How to know you are done

- **A capability is not done until it has produced a row in production.** "The code is there" is not a claim you may make. Show the log line, the database row, or the message that proves it fired.
- **Numbers are composed in code, never by a model.** Anything with a figure in it (counts, money, durations, dates) is computed and formatted by code. A model may write prose around it.
- **Measure, change one thing, measure again.** Any tuning of a threshold or a gate is preceded and followed by the same measurement, and both are written down.
- **Tests run green before every commit**, and you gate the commit on the exit code, not on eyeballing output.

### 1.4 The ledger

Create `<namespace>/OVERHAUL-LEDGER.md` at the start. First line names this playbook and the date. Every phase writes to it: what you read, what you decided, what you built, the commit hashes, what is switched on, what is waiting on the owner. A later session trusts the ledger and `git log` over memory.

---

## Part 2. Phase 0: Discovery (read only)

Produce `<namespace>/00-snapshot.md`. Do not write code in this phase. Answer every line from the codebase, not from assumption; if a line cannot be answered from what you can read, say so.

**Identity and stack**
- Language, runtime, package manager, test runner, lint.
- Entry point. How a message becomes work (the inbound path, end to end).
- Process supervision and restart path. Whether a safe (drain-first) restart exists.
- Persistence: which database, which tables, which key-value or config store.
- Where the agent's identity lives (system prompt, persona file, soul file) and how it is loaded.

**Surfaces**
- Every channel the agent listens on (Telegram, Slack, Discord, email, voice, web, CLI, MCP) and every one it can send to.
- How a reply is chunked, formatted and delivered per surface.
- Whether there is any progress signal on long work, per surface.
- Whether the owner can interrupt work in flight, and how.

**Memory**
- What memory systems exist (files, vector store, tables, the model's own context window), what writes to them, what reads them, and whether anything can search them on demand.

**Skills and tools**
- Where skills live (every location, not the first one you find). How many resolve. Which have ever fired (usage telemetry, logs).
- Connectors and MCP servers, and how a dispatched child reaches them.
- Whether there is any mechanism that turns repeated work into a reusable skill.

**Scheduling and proactivity**
- Every scheduled job, its cadence, its switch, and whether it has ever produced output.
- Anything that watches a channel or a feed and proposes work unprompted. Whether it has ever proposed anything.
- Whether the owner can create, edit, pause or list standing jobs in conversation.

**Outputs**
- What the agent can hand back other than chat text: files, PDFs, images, video, links, pages. Whether files are sent as attachments or as paths.

**Safety**
- Approval flows. Kill switch. What counts as an error versus a cancellation. How financial or destructive actions are prevented. Whether tests can reach production endpoints.

**Model layer**
- Which models, chosen where, by whom. Whether the owner can ask for a model. Whether there is any failover when the primary lane is unavailable.

End the snapshot with a **"what fires and what only exists"** table: every module or job, with the date it last produced evidence, or "never".

---

## Part 3. Phase 1: Inventory against the catalogue

Produce `<namespace>/01-inventory.md`: one row per capability below, graded **HAS / PARTIAL / MISSING / N/A**, with a one-line evidence pointer (file and line, or a row count). N/A is for capabilities that do not apply to this agent's surfaces or purpose; say why.

The catalogue is what an "AI employee" feels like to its owner, grouped the way the owner experiences it. Each entry names the capability, the felt experience, and the acceptance proof.

### A. Responsiveness (the owner never wonders whether the agent died)

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| A1 | Progress signal on every surface | One status line at ~45s, edited in place every ~60s, removed on completion. Never a new bubble each time. | A real long run shows one edited line, then it disappears. |
| A2 | Interrupt in flight | "stop" kills the running work in under a second, including subprocesses. Quoting a status line and saying "stop" kills only that run. | A live run aborted mid-flight resolves as cancelled, not as an error, with no orphan process. |
| A3 | "What's running" | A deterministic read of in-flight work: source, model, elapsed, task, and how many decisions wait on the owner. No model call. | The composer is one function used by every surface. |
| A4 | Follow-ups fold in | A second message while the first is still running is merged into it (young run) or queued behind it (older run) with context, never raced. | Two messages 30s apart produce one run and one reply. |
| A5 | Albums and attachments as one ask | Several photos sent together are one message with one caption, not N runs. Files arrive with the ask, and the agent is told to read them first. | A three-photo album produces one run. |
| A6 | Crash recovery | A request in flight during a restart is re-run once on boot, with a visible note, and abandoned after a fixed number of attempts with a request to re-send. | The in-flight table empties after a clean boot. |

### B. Proactivity (the agent notices and proposes)

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| B1 | Ambient watch that speaks | The agent reads the channels it is allowed to and proposes concrete work as a card with buttons (Do it, Not now, Change it, Always). Proposals are rare, relevant, and never about the owner's own messages. | At least one proposal row exists and one became work. |
| B2 | Rejection ledger | Every rejected candidate records WHICH gate rejected it and why, in a table separate from proposals, so the gates can be tuned on evidence. | The ledger has rows; a replay script can re-run history through the gates without sending anything. |
| B3 | Untrusted content fence | Channel text reaches any classifier inside explicit markers, cannot forge the markers, cannot add fields, and reaches a card only as a quote of a real message. | Test with a forged closing marker. |
| B4 | Standing jobs in conversation | "Every weekday at 7am, pull yesterday's numbers" creates a job after a readback; the owner can list, edit, pause, resume and delete jobs and control the built-in ones by name. Ambiguous cadences are refused, not guessed. | A job created in chat runs on schedule and delivers to the owner. |
| B5 | Speed-to-lead or equivalent event alert | Whatever the agent's business is, the time-critical event (a lead, an outage, a payment failure) is announced within seconds, deduplicated, with an alarm when the pipeline goes dark. | First run seeds silently; a broken credential produces one alarm, not a stream. |

### C. Memory (the agent remembers, and can be asked)

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| C1 | Memory in the daily artefact | The morning brief, if there is one, carries the decisions and preferences relevant to today. | A memory block appears in a real brief. |
| C2 | Recall on demand | "What did we say about X" is answered from a deterministic search over messages and memories, with dates, before any model is involved. | The matcher is anchored so ordinary sentences containing "remember" fall through. |
| C3 | Client or project scope | "Brief me on <client>" narrows every read to that scope. | Test with a name that is also a common word. |
| C4 | Initiatives and briefs | The owner can register where they are heading ("X is on the horizon") and feed context in their own words ("context for X: …"). Long messages that name an initiative are filed automatically. A deliverable for an initiative with no brief is **refused with intake questions**, never guessed. | A registered initiative with notes; a refused artefact request. |

### D. Compounding (the agent gets better)

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| D1 | Skill capture | After repeatable project work, one proposal per day to save it as a skill, behind a press. Every "not repeatable" verdict is recorded with its reason. Exactly one write path to a skill file. | A proposal row; a rejected row with a reason. |
| D2 | "Turn that into a skill" | The explicit request runs the same path on demand, without the daily ceiling. | Works on the last exchange. |
| D3 | Skill leverage scan | Weekly: which installed skills have never fired, and three concrete plays that end in a shippable artefact. | A card with plays, no play whose deliverable is "set up a system". |
| D4 | Growth scan | Weekly: compares the owner's registered initiatives against the live installed skill set, scouts candidates, offers Install buttons. Nothing installs without a press. A claimed "covered by" that does not resolve is demoted to uncovered. | A card with candidates; sticky coverage so a covered gap is not re-reported. |
| D5 | Integrations document themselves | The first successful use of a new connector proposes an integration skill capturing endpoints, ids and gotchas. | Detection runs before any model call. |
| D6 | Self-improvement behind a press | Weekly: evidence gathered in code (errors grouped by normalised prefix, failed jobs, unverifiable events, repeated rejections, deferred review items), ranked arithmetically, at most three proposals with a Build button. Load-bearing files (dispatch, interfaces, money guard, restart script, entry point, env) are never proposed. A build is verified in code (commit exists, only allowed files, suite green) and reverted on red. | A card; a build that was verified or reverted. |

### E. Outputs (the agent delivers, not describes)

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| E1 | Files, not paths | A produced document arrives as an attachment with the summary as caption. A path string in a message is a failure. | Delivered file in the chat. |
| E2 | Everything at a link | Artefacts are also viewable at a URL (token-gated), with version history, images and video rendered inline. | Open the link. |
| E3 | Media kinds | Images and video as first-class artefact kinds, gated in code by a real media file (size and magic bytes; duration measured for video). | A short video request produces a file of the requested length band. |
| E4 | PDF as a format | Any text artefact can be rendered to PDF on request or via a follow-on button; the render runs in a separate process. | Button press yields a PDF on the same surface the card is on. |
| E5 | One follow-on offer per artefact | After delivery, one button ("Make the PDF"). Never after a failed delivery, never for media where the only follow-on spends money. | Button present on text artefacts only. |
| E6 | Nothing truncates silently | No hard-coded character slice anywhere in a send path. Long messages chunk on line or paragraph boundaries. | grep for `.slice(0, 3900)`-style caps returns nothing in send paths. |

### F. Model layer (you keep the context, the model can change)

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| F1 | Lanes chosen in code | Tier from route class (chat and action to the fast tier, project and analysis to the strong tier); explicit ask wins; anything needing tools stays on the tool-capable lane. | A pure function with tests. |
| F2 | Ask for a model | "Use the strong model for this", "think harder", "on gemini". A leading model clause is stripped before other matchers so it cannot break them. | Requests route correctly with the clause present. |
| F3 | Footer composed in code | Every project-class delivery ends with a line naming the lane actually used, written by code. | The footer never names a model the run was not on. |
| F4 | Overflow and failover | When the primary lane is rate-limited or down, a router falls through a chain of providers. Empty-wallet errors fail over; auth errors surface. | A probe through the router returns 200 and names the provider. |
| F5 | Second opinions are text-only | Non-primary providers receive text in and text out, never a connector credential. | Env passed to those calls is inspected in a test. |
| F6 | Usage recorded, never priced | Token counts per run, accumulated across retries, NULL distinguished from zero. No dollar figure anywhere. | Ledger rows with token counts. |

### G. Skills and tools

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| G1 | Both skill homes surveyed | The roster reads every location skills can live (user skills, plugin skills), never a remembered list. Installed plugins come from the installed manifest, not a glob over a cache. | Roster count matches reality. |
| G2 | Run a skill by name | "Run <skill> on <subject>" validates the name against the live roster before spawning; unknown names refuse rather than silently doing nothing. | Test with a plausible non-existent name. |
| G3 | Skills visible | A page or command lists what is installed. | Open it. |
| G4 | Browser work as a job shape | Jobs that need a browser carry skill hints that actually resolve, and run outbound-stripped. No browser inside the main process. | grep confirms no browser library is required by the daemon. |
| G5 | Connector auth failures come with the next step | An expired session or dropped connection surfaces as a card naming what to do, rate-limited per family. | A simulated auth failure produces one card. |

### H. Safety and approvals

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| H1 | Approvals with memory | On the third approval of an action class, the card offers "Always". Trusted classes run with a notice and an undo line. **Money is exempt by construction**: it cannot be trusted even by editing the store directly. | Test the hand-forced case. |
| H2 | Decisions waiting on the owner are visible | A posted card writes a `requires_action` row resolved by any press, surviving restarts, counted in "what's running". | Row exists while a card is open. |
| H3 | Read-only money | Every financial tool call passes an allowlist; unknown slugs throw. | Test an unrecognised slug. |
| H4 | Tests cannot reach production | A suite-wide guard fails the run if any test reaches a messaging API, a billed API or spawns the agent CLI. Every transport refuses under the test runner with a named escape hatch. | The guard test is in the suite. |
| H5 | Audit trail | Every outbound records surface, target and size (never content), scrubbed of anything token-shaped. An audit gap is never an outage. | Rows appear per send. |
| H6 | Verification as a state | Claims the agent cannot verify are marked `unverifiable`, never rendered as fact; stale open loops are reconciled on a schedule with cited reasons. | A reconciler run report. |

### I. Reach (ways in and out)

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| I1 | Inbound email, fail closed | Mail arrives via IMAP; announce-only by default; routing into the agent requires both a switch and an exact-address trusted-sender list; routed email is scope-limited to answer and draft, inside an untrusted fence. First run seeds silently. | Announce rows; refusal of a non-trusted sender. |
| I2 | Voice through the same door | Spoken requests pass through the same inbound handler as text, so "stop" and every matcher apply. | A spoken "stop" aborts. |
| I3 | Callable from the developer's tools | An MCP server exposing ask, status and memory search, as a separate process with outbound credentials stripped, registered only on machines where a dispatched child cannot see it (recursion risk). | Registered on the developer's machine only. |
| I4 | Public ingress that survives restarts | Tunnels for webhooks and the artefact page, with URL changes announced to the owner and a quiet window so a flapping tunnel cannot storm the chat. | URL in the store; a change produces one message. |

### J. Operations

| # | Capability | Felt experience | Acceptance proof |
|---|---|---|---|
| J1 | Safe restart | A restart request is queued and fires only when nothing is in flight; an immediate restart refuses if work is running; force exists for emergencies. Lifecycle commands that bypass it are denied by tooling, not by prose. | A hook or permission rule denies the raw restart. |
| J2 | One supervisor | Exactly one process supervisor; a second poller on the same chat token is a permanent conflict. Dispatched children cannot start a poller on the owner's bot token. | Kill the process; nothing respawns except via the supervisor. |
| J3 | Switch-on discipline | Every job reads its switch at tick time; defaults are documented against the live store, not the code; a job that cannot read its source records a FAILURE, never a quiet day. | The doc points at the store, not a constant. |
| J4 | Review of conflicts and redundancies | After the build, a read-only review of every new commit against the old code: duplicate composers, stale claims, matchers that shadow each other, dead files, unread config keys. Fixed in waves, each reported. | The review document and the wave reports. |

---

## Part 4. Phase 2: Rank the gaps and stop once

Produce `<namespace>/02-gap-ranking.md`. Rank every MISSING and PARTIAL row by **felt gap × how visibly the owner will miss it × inverse build cost** for this agent's architecture. Group into tiers. Name the **closing set**: the eight to twelve items that, once built, put the agent past the reference product for its one owner.

Then **stop and show the owner** the snapshot, the inventory and the ranking, together with the intake questions in Appendix A. This is the one planned pause. Do not build before the owner has answered.

Record the answers in the ledger as rulings. From here on, decide small things yourself and record them as `Ruling: <what> because <why>`.

---

## Part 5. Phase 3: Build, in the proven order

Build in this order unless the owner's ranking changes it. Each item is one or more commits, each with tests, each behind a switch where it speaks or acts. Each pattern card below gives the design that worked, the non-overlap rule, the acceptance proof and the pitfall that cost time. Write code in the agent's language and conventions.

### 4.1 Safe restart and lifecycle guard (J1, J2) — build first

**Design.** A script sets a `restart_pending` flag in the agent's store. After every completed request (and on a periodic idle sweep) the agent checks the flag and exits cleanly only when its in-flight table is empty; the supervisor brings it back on the new code. `--now` restarts immediately but refuses if anything is in flight; `--force` overrides; `--check` reports. Deny the raw lifecycle commands (`restart`, `stop`, `delete`, `reload` of the agent process) with tooling: a pre-command hook or permission rule that returns the safe instructions in the denial.

**Non-overlap.** Do not change how the supervisor starts the agent. Add the drain check at one place in the request path.

**Pitfall.** Prose rules do not hold. The rule "never restart" was written down and broken three times the same day because a permissions file allowed the command silently. Enforce in config.

**Pitfall.** If the owner's chat platform allows one poller per token, a dispatched child that loads a chat plugin with the same token steals the connection and the agent goes deaf. Strip the token from children that do not need it, disable such plugins for child sessions, and grep for stray pollers when you see a conflict error.

### 4.2 Interrupt, kill switch, "what's running" (A2, A3)

**Design.** A registry holds every spawned child with a `ref` keyed on the inbound message (surface, chat, message id). "stop" is matched **deterministically, before every other route, and never dispatched to a model**, because routing an interrupt through a model queues it behind the job it exists to interrupt. Kill the **process group**, not the pid (spawn children detached; signal the negative pid; SIGTERM then SIGKILL after a few seconds). Aborted runs carry `aborted: true` so no layer retries them and the ledger records `aborted`, not `error`. A reply quoting a status message and saying "stop" kills only that ref. "What's running" is one composer, reused by every surface, ending with how many decisions wait on the owner.

**Non-overlap.** New module. One matcher inserted at the top of the inbound handler. Vocabulary anchored both ends so "stop the ads" falls through as a work request.

**Acceptance.** A live run aborted mid-flight settles as cancelled in seconds with no orphaned subprocess.

### 4.3 Progress on every surface (A1)

**Design.** One shared progress module (thresholds, elapsed formatter). Per surface: post one status message at ~45s, edit it in place every ~60s, delete or finalise it on settle. Wrap the handler so the reporter is stopped in a `finally`; a thrown handler must not orphan a live status line. Remove any older single ack that now duplicates it.

**Pitfall.** Post-then-post produces five bubbles on a phone and is worse than silence. Post-then-edit.

### 4.4 Follow-ups and albums (A4, A5)

**Design.** A gate keyed per conversation (chat id; channel plus thread on Slack) wraps the **final** dispatch, below every deterministic quick path. If a run under ~90s is in flight, kill it by ref, merge texts and files, run once, and tell the owner in one line. If the run is older, let it finish and deliver, then run the follow-up with `followupTo` set so the agent amends rather than restarts. Bursts fold together; only the last arrival replies; superseded runs resolve to an empty reply the surface sends as nothing. Log only the new part of a merged message to history. For albums: buffer items sharing a media group id for ~1.5s, then run once with every file and the shared caption; tell the agent to read every file first.

**Pitfall.** The platform puts an album's caption on only one item; the others look captionless and each becomes a "photo with no caption" run.

### 4.5 Files, links, media, PDF (E1 to E6)

**Design.** Replace every path-in-a-message with an attachment send that also logs to history so a reply to the attachment resolves. Serve the artefact directory at a token-gated URL with a directory listing filtered in code (never build a path from the request), inline image and video, and strict CSP with no script. Media kinds gate on a real file (size, magic bytes; `ffprobe` duration for video, rejecting anything under half the requested length; a missing `ffprobe` falls back to the bytes gate rather than failing every video). PDF is a format selected by metadata and rendered in a separate process. One follow-on button after text artefacts only. Remove every fixed-length slice in send paths and chunk on boundaries.

**Pitfall.** A request for a 20-second video produced a perfectly valid 2-second file. Bytes prove a file is a video; they say nothing about whether it is the video asked for. Clamp the requested length in one function and measure the output.

### 4.6 Model lanes (F1 to F6)

**Design.** A registry of lanes and tiers. `choose(task, opts)` is a pure function: explicit ask wins; tool-needing work stays on the tool-capable lane; else tier by route class. Normalise a leading model clause before any other matcher. Compose the "Ran on <lane>" footer in code. An overflow router (a small worker with a provider chain) takes over when the primary lane is limited; empty-wallet errors fail over, auth errors surface. Record tokens per run, never a price. Non-primary providers get text only.

**Pitfall.** Uploading a secret through a piped `npx` stored an empty string. Upload from a variable with `printf '%s'`. Verify with a probe that names the provider.

### 4.7 Ambient watch (B1 to B3)

**Design.** Instrument before tuning: every negative path returns `{gate, reason}` and writes a rejection row per channel per tick to a **separate** table (rejection rows in the proposals table would exhaust any daily ceiling and pollute any digest). A replay script re-runs real history through the gates and prints a tally; it has no send flag and advances no cursor. Wrap channel text in untrusted markers; neutralise forged markers; fixed result keys; a card quotes only a real message. Cards carry buttons: Do it, Not now, Change it, Always.

**Rule.** Measure, change one gate, measure again. Record before and after tallies in the ledger. A false positive costs the owner one tap; a false negative costs a client issue nobody surfaced.

### 4.8 Standing jobs (B4)

**Design.** Cadence parser that refuses ambiguity; readback with confirmation; list, edit, pause, resume, delete in conversation; built-in jobs controllable by name through a registry that is **a description, not a second source of truth** (each job still reads its own switch at tick time). Live registration without restart; a cap. Optional `model` and `skills` per job, validated against live registries so a job never runs against a model or skill that does not exist. A leading cadence ("every friday brief me on X") offers to schedule rather than running once.

### 4.9 Memory in the brief, recall, scope, initiatives (C1 to C4)

**Design.** Recall is a deterministic search over messages and memories with dates, anchored so ordinary sentences fall through. Scope narrows reads to a named client or project. Initiatives: a table of where the owner is heading, notes in the owner's own words (also written to memory through the agent's own API as a new type), an intake prompt composed in code, auto-filing of long messages that name an initiative, and **refusal** of any deliverable for a briefless initiative.

**Non-overlap.** The agent's existing memory is read, never rewritten. New notes go through the agent's own write path with a new type or tag so they are distinguishable and removable.

### 4.10 Compounding (D1 to D6)

**Design.** Skill capture with a rejection ledger and a one-per-day ceiling that explicit requests bypass; one write path to a skill file, requiring a press, never overwriting. Leverage scan over the live roster and usage telemetry. Growth scan against initiatives with Install buttons, sticky coverage, and demotion of unresolvable "covered by" claims. Integration capture on first connector use, detected before any model call. Self-improvement: evidence in code, arithmetic ranking, at most three proposals, Build behind a press, load-bearing files never proposed, verification in code, revert on red, a successful build **queues** a safe restart.

**Pitfall.** A gap list that drifts run to run (a covered capability re-reported as a gap) erodes trust fast. Make coverage sticky and match installed skills by their own names.

### 4.11 Approvals with memory, money exempt (H1 to H3)

**Design.** Action class derived in code; a per-class trust flag written only by the "Always" button; trusted classes run with a notice and undo. `money` is refused at every layer: classification, granting, and reading, even when the store is edited by hand. Financial tool calls pass an allowlist.

### 4.12 Reach (I1 to I4)

**Design.** Inbound email: IMAP poll, two fail-closed switches (poll; act), exact-address trusted list, scope gate at the top of the inbound handler, untrusted fence, silent first seed, overflow past the per-poll cap re-fetched not skipped. Voice through the inbound handler. An MCP server as a separate process with outbound credentials stripped, registered only where no dispatched child can see it. Tunnels with announced URL changes and a quiet window.

### 4.13 Audit, verification, tests that cannot reach production (H4 to H6)

**Design.** Every surface-rendered outbound writes an audit row (surface, target, size, never content, scrubbed). Claims the agent cannot verify are a state, not a fact. A suite-wide guard preloads a network and spawn interceptor, re-runs the whole suite, and fails on any production endpoint or CLI spawn. Every transport refuses under the test runner with a named escape.

**Pitfall.** A single test that reached the real dispatcher with empty dependencies sent the owner a financial summary on every test run for most of a day. The guarantee belongs in the transport, where it cannot be forgotten, not at each call site.

---

## Part 6. Phase 4: Review and fix waves (J4)

When the closing set is built, run a **read-only review** of every commit since the overhaul began against the pre-existing code. Look for: two composers of the same thing; a matcher that shadows another (order in the inbound chain matters; pin the order with tests); stale claims in docs; switches documented as ON that read OFF; fixed-length slices; dead files; unread config keys; test-runner refusals missing on a new transport; a name that does not resolve (skill, model, job) failing silently.

Write the findings with ids. Fix in waves, smallest and safest first. Each wave gets a report. Nothing is deleted that the owner did not create or explicitly release; archive instead.

---

## Part 7. Phase 5: Prove it live

For every capability marked built, record in the ledger the production evidence: a log line, a row, a delivered message. A capability without evidence is PARTIAL, whatever the code says. Then write the final report for the owner:

- What was built, by capability, with commit hashes.
- What is switched ON and what waits for their press, with the exact command or button.
- What needs them (a login, a key, a domain, a top-up) and why.
- What was deliberately not built and why.
- Every file the overhaul touched outside its namespace, and the one-line reason for each.

---

## Appendix A. Intake questions for the owner

Ask these at the Phase 2 pause. Skip any the snapshot already answers.

1. Which surface is your primary one, and which others must keep working exactly as they do?
2. When is it safe to restart the agent, and how do you want to be told before it happens?
3. Who may the agent message? (Default: only you. Anything else is a decision.)
4. Which systems are read-only for the agent (money, CRM, email send)?
5. Which channels or feeds may the agent watch for ambient proposals, and which are off limits?
6. Where do you want new code, tables and docs to live so they are recognisably separate?
7. Do you want new skills installed automatically, or only behind a press? (Default: press.)
8. Which model providers do you have keys for, and is any account unfunded?
9. What is on your horizon that the agent should grow toward (a launch, a brand, a role)?
10. Is there anything in the current agent you consider finished and untouchable?

## Appendix B. Commit and report conventions

- One capability per commit. Stage by explicit path.
- Commit body: problem, design, proof. Name the switch and its default.
- Every send path: dry-run default, `--send` explicit.
- No em dashes in any copy the owner will read on a phone; short lines; numbers on their own line.
- The ledger is updated before the session ends, every session.

## Appendix C. Order of the closing set that worked

1. Safe restart and lifecycle guard.
2. Kill switch, targeted cancel, "what's running".
3. Progress on the primary surface.
4. Files not paths; artefacts at a link.
5. Model lanes and the overflow router.
6. Ambient instrumentation, then tuning on evidence.
7. Standing jobs made discoverable and controllable.
8. Recall, scope, memory in the brief.
9. Follow-up gate and album collector.
10. Compounding loops (capture, leverage, growth, self-improve).
11. Approvals with memory, money exempt.
12. Reach (email, voice, MCP), then the review and fix waves.

Everything in this file was done once, on a live agent, while its owner kept using it. The order above is the order that caused the least disruption. Keep the contract, keep the ledger, and prove every claim with a row.

## Appendix D. Stack-specific shortcuts (optional)

The playbook is stack-neutral. These notes save time on the stacks seen most often. Use them only if they match your agent.

**Claude Code as the executing agent or as the agent's own dispatcher.**
- Skills live in more than one place (a user skills folder and a plugins cache). Survey both before declaring a skill missing; installed plugins come from the installed-plugins manifest, not a glob over the cache.
- Deny raw lifecycle commands with a `PreToolUse` hook on the shell tool and a `permissions.deny` entry; return the safe-restart instructions in the denial text.
- A dispatched child inherits the parent's environment. Strip chat tokens and outbound credentials from children that do not need them, and pass a settings override that disables any chat plugin so a child can never poll the owner's bot token.
- Run dispatched children with a JSON output format so token usage rides on the envelope; store tokens, never a price.
- Test-runner detection: the built-in runner sets an environment variable in every test process; refuse to spawn or send when it is present, with a named escape variable for the few tests that exercise a stubbed spawn.

**Node with PM2.** One PM2 app is the only supervisor. `pm2 resurrect` at login; never a second LaunchAgent or systemd unit for the same process. Spawn children detached so a kill reaches the process group.

**Python with systemd.** `Restart=on-failure` plus a drain flag checked at the top of the main loop gives the same safe restart. Use process groups (`start_new_session=True`) for the same reason.

**Docker.** The drain flag must live in a mounted volume or the store, not the container filesystem, or a restart loses it.

**Telegram.** One `getUpdates` consumer per bot token. Albums arrive as one update per item sharing `media_group_id`, caption on one item only. Callback data is capped at 64 bytes.

**Slack.** Socket Mode for inbound; per-thread keys for follow-ups; edits and deletes can re-run or cancel a run by message ts. Post file uploads with the bot token, not through a connector that authenticates as the owner.
