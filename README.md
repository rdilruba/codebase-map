# Codebase Map

**A skill that turns an unfamiliar repository into something you can actually contribute to.**

Point your coding agent at a repo and ask it to map the codebase. It reads the implementation, traces how real requests move through the system, and writes a set of Markdown documents and Mermaid diagrams into `docs/codebase-map/` — so the understanding stays in the repo instead of disappearing with the chat session.

Works with Claude Code, Codex, Gemini CLI, Cursor, Copilot, OpenCode, and any other agent that reads `SKILL.md`.

The goal is not documentation for its own sake. The goal is to get you from "I have no idea where anything is" to "I know which file to open and why" as fast as possible.

---

## The problem

You cloned the repo. You have a task. Now you spend your time opening files at random, trying to figure out where the logic you need actually lives.

Onboarding docs, when they exist, tell you how to install dependencies. They rarely tell you what you need: where the important logic lives, what depends on what, and what actually happens when a user clicks a button.

And increasingly, a growing share of the code was written with AI assistance — by someone who may no longer be on the team — which means patterns are inconsistent and some sections deserve a closer look than others.

## What you get

A persistent map inside your repository:

```
docs/codebase-map/
├── INDEX.md                          Entry point, links to everything
├── 01-tldr.md                        What this system is, in one read
├── 02-system-architecture.md/.mmd    How the pieces fit together
├── 03-dependency-graph.md/.mmd       What depends on what
├── 04-data-flows/                    Real requests, traced end to end
├── 05-the-spine.md                   The 5–15 files that matter most
├── 06-recommended-reading-order.md   What to read, in what order, and why
├── 07-potentially-ai-assisted-code.md  Sections worth a closer human look
├── 08-key-concepts.md                What you must understand before editing
└── 09-open-questions.md              What is still unknown, and what wasn't explored
```

The files are committed to your repository. Your teammates get them. Future coding agents get them too — the map becomes context for every session after the first.

## What makes it different

**Five modes, not one big generate button.** Build it, update it after a refactor, explain one domain, trace one flow, or check whether the map has drifted.

**Evidence labels instead of confident guessing.** Every architectural claim is marked CONFIRMED (supported directly by the implementation), INFERRED (strongly suggested but not established), or UNKNOWN. Inferences never get presented as facts, and missing architecture never gets invented. You can trust the map because it tells you how much to trust each part of it.

**Archetype-aware tracing.** A CLI tool is not traced like a React app. The skill first classifies the repository — frontend, backend service, CLI, library, data pipeline, background worker, infrastructure-as-code, monorepo — and picks a tracing strategy that fits. Request lifecycle for APIs, render path for frontends, one record through the stages for pipelines.

**The spine and the reading order.** Not a file inventory. An answer to "if I only have one hour, what do I read?", the handful of files that carry the system, in the order that makes each one make sense, with actual repository paths.

**Real flows, not boxes and arrows.** Three to five representative user actions traced through the actual implementation path, from the trigger all the way to the data store and back. This is the part that teaches you how the codebase really works.

**A pass for code that deserves a closer look.** Repetitive generated-looking implementations, inconsistent abstractions, over-commented obvious code, complex logic with weak tests. The map flags them as characteristics worth reviewing, never as accusations about authorship.

**Honest boundaries.** The map states what it did not explore — which directories were skipped, which paths could not be followed, which flows were left untraced. A map that quietly looks complete is worse than one that shows its edges.

**Your code is never touched.** This is an analysis workflow. It writes only inside `docs/codebase-map/`. No refactors, no fixes, no dependency changes.

## Install

`SKILL.md` is an open format. This skill works in any agent that supports it, only the directory changes.

**Any agent, one command** (detects what you have installed):

```bash
npx skills add rdilruba/codebase-map
```

**Manual install** — clone, then copy the folder to your agent's skills directory:

| Agent | Personal | Per-project |
| --- | --- | --- |
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| OpenAI Codex | `~/.agents/skills/` | `.agents/skills/` |
| Gemini CLI | `~/.gemini/skills/` | `.gemini/skills/` |
| OpenCode | `~/.config/opencode/skills/` | — |
| GitHub Copilot | `~/.copilot/skills/` | — |
| Cursor, Windsurf, Cline, Roo Code, and others | use the `npx skills add` command above | |

```bash
git clone https://github.com/rdilruba/codebase-map.git
mkdir -p ~/.claude/skills          # swap in your agent's path from the table
cp -r codebase-map ~/.claude/skills/
```

Per-project installs are worth considering: commit the skill under `.claude/skills/` or `.agents/skills/` and every contributor gets it, along with the map it generates.

**Claude.ai** — download `codebase-map.skill` from the releases page and upload it under Settings → Capabilities → Skills.

**Agents without skill support** — point them at `SKILL.md` directly, or paste it in as instructions. It is plain Markdown with no scripts or dependencies, so nothing breaks.

## Use it

| Command | What it does |
| --- | --- |
| `codebase-map` | Build the full map from scratch |
| `codebase-map update` | Refresh the map after the code changed — only what moved gets rewritten |
| `codebase-map explain payments` | Focused analysis of one feature, domain, or module |
| `codebase-map trace checkout` | Follow one user request end to end |
| `codebase-map check` | Verify the map against the repo and report drift — writes nothing |

Plain language works identically. "Update the codebase map", "explain how payments work", and "trace what happens when a user checks out" hit the same modes. In Claude Code the skill is also available as `/codebase-map`.

`update` is the one to remember. Run it after a significant refactor and it diffs against the commit the map was generated from, re-inspects only the affected areas, and leaves your own edits to the map alone.

`check` is cheap and read-only, which makes it useful in CI or before trusting a map somebody else generated.

For a full map, it runs the complete workflow. Ask about a single feature and it does a focused version instead of analyzing everything, useful when you only need to understand the part you are about to change.

## Start here after it runs

1. `01-tldr.md` — five minutes, and you know what the system is
2. `05-the-spine.md` — the files that carry the weight
3. `06-recommended-reading-order.md` — follow it instead of browsing at random
4. `04-data-flows/` — pick the flow closest to your task and read the trace

## Good to know

The map is a snapshot, not a live view. It does not update itself when the code changes — run `codebase-map update` after significant refactors, or `codebase-map check` if you want to know whether it has drifted before you trust it.

Quality tracks the repository. Clear entry points and conventional structure produce a sharp map. Heavy dynamic dispatch, runtime plugin loading, or configuration-driven behavior produce more UNKNOWN labels, which is the correct outcome rather than a failure.

Review before you commit. Treat the output as a well-researched draft from a new teammate, not as verified truth.

## Contributing

Issues and pull requests are welcome, particularly reports of repositories where the map came out weak, the archetype table and the tracing strategies get better with real examples.

## License

MIT
