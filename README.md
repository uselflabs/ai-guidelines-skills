# 🧠 AI Guidelines Skill (Karpathy Guidelines)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skill](<https://img.shields.io/badge/Format-Agent%20Skill%20(SKILL.md)-purple.svg>)](https://agentskills.io/specification)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#-contributing)

> Behavioral guidelines to reduce common LLM coding mistakes — derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) and extended to cover the full spectrum of AI agent pitfalls.

---

## 📖 Overview

When AI coding assistants (e.g., Antigravity, Cursor, Claude Code, Copilot, Windsurf) write code, bad output rarely looks bad — it looks finished.

This repository provides a drop-in **Agent Skill** (`ai-guidelines`) designed to constrain AI coding agents:

- 🚫 **No hallucinating**: Prevents inventing APIs, parameters, paths, or versions.
- 🎯 **No overcomplicating**: Enforces minimum code and zero unrequested abstractions.
- 🔬 **Surgical edits**: Touches only what is necessary, preserving surrounding style and comments.
- 🧪 **Verifiable verification**: Prevents faking passing checks, disabling assertions, or silently swallowing errors.
- 🛡️ **Safe execution**: Prevents destructive or irreversible actions (git resets, secret leaks, unasked deletions).

---

## 📁 Repository Structure

```text
ai-guidelines-skills/
├── .agents/
│   └── skills/
│       └── ai-guidelines/
│           └── SKILL.md      # The core skill specification & prompt instructions
├── .gitignore
├── LICENSE
└── README.md
```

The whole skill is a single self-contained file: [`.agents/skills/ai-guidelines/SKILL.md`](.agents/skills/ai-guidelines/SKILL.md). No scripts, no dependencies, no build step.

**When it applies** (from the skill's own `description`): use it when writing, reviewing, debugging, or refactoring code — to surface assumptions, make surgical changes, fix root causes, and define verifiable success criteria. Skip it for one-liners and pure explanation.

---

## ⚡ The 8 Core Rules

| Rule  | Name                                | Core Mandate                                                                                                                                 |
| :---- | :---------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------- |
| **0** | **Establish the Source of Truth**   | Know what defines "correct" before starting. Never treat AI's past responses as authoritative.                                               |
| **1** | **Think Before Coding**             | Surface tradeoffs, don't assume, don't invent. Check the environment instead of memory; ask only when it can't answer.                       |
| **2** | **Simplicity First**                | Minimal code that solves the problem. No speculative flexibility or single-use abstractions.                                                 |
| **3** | **Surgical Changes**                | Touch only what you must. Never rewrite entire files for small changes; match existing style.                                                |
| **4** | **Goal-Driven Execution**           | Define verifiable success criteria. Loop until verified. Never edit tests just to make them pass. Flag every stub or skipped part.           |
| **5** | **Fix the Cause, Not the Symptom**  | Making an error disappear is not fixing the bug. Trace the bad value to its origin and fix it there; no silent guards or empty catch blocks. |
| **6** | **Never Take Irreversible Actions** | Never delete data, wipe git history, install dependencies, or expose secrets without explicit consent.                                       |
| **7** | **Preference vs. Fact**             | User decides preferences and tradeoffs; verifiable evidence decides facts.                                                                   |

### 📋 Reporting Contract

The skill also fixes how the agent reports back: **open** with the source of truth and its assumptions (one line each), and **close** with what it ran and what it returned — or a plain statement that the work is unverified. Rule 4 defines four acceptable rungs of evidence: an automated check, a build plus concrete manual repro, observable evidence (log/trace/screenshot), or an explicit "unverified". Saying nothing is not one of them.

> **Tradeoff:** these guidelines bias toward caution over speed. For trivial tasks, use judgment.

---

## 🚨 Failure Catalog

The skill actively instructs the agent to catch itself if it attempts any of these common failure patterns:

| Rule       | Prevented Behavior                                                                                                                                 |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Rule 0** | Treating previous assistant outputs as requirements; silently picking between conflicting specs.                                                   |
| **Rule 1** | Inventing an interface, flag, version, or file path; citing uninspected files; asking what the codebase could answer.                              |
| **Rule 2** | Duplicating existing utility logic; adding unasked dependencies; premature abstractions.                                                           |
| **Rule 3** | Reformatting untouched lines; deleting adjacent comments; using `... rest unchanged` placeholders; editing from a stale read.                      |
| **Rule 4** | Reporting done without running verification; weakening/skipping failing tests; leaving stubs or skipped parts unflagged; guessing past 2 failures. |
| **Rule 5** | Guarding at failure sites without examining root cause; swallowing errors into empty handlers.                                                     |
| **Rule 6** | Git force-pushing, wiping unstaged files, altering release configs, or logging credentials.                                                        |
| **Rule 7** | Reversing a verified correct answer solely due to user pushback.                                                                                   |

---

## 🚀 How to Install & Use

First, clone this repository somewhere outside your project:

```bash
git clone https://github.com/uselflabs/ai-guidelines-skills.git
```

### 1. Antigravity / Cursor / Windsurf (Agent Skills-compatible tools)

All three load skills from `.agents/skills/`, the layout this repo already uses.

**Per-project:** copy the skill directory into your project's `.agents/skills/`:

```bash
mkdir -p .agents/skills
cp -r /path/to/ai-guidelines-skills/.agents/skills/ai-guidelines .agents/skills/
```

**Global (all projects):** copy it into the tool's user-level skills directory instead:

| Tool        | Global skills directory                              | Docs                                                                                                           |
| :---------- | :--------------------------------------------------- | :------------------------------------------------------------------------------------------------------------- |
| Cursor      | `~/.agents/skills/` or `~/.cursor/skills/`           | [Agent Skills](https://cursor.com/docs/skills)                                                                 |
| Windsurf    | `~/.agents/skills/` or `~/.codeium/windsurf/skills/` | [Cascade Skills](https://docs.windsurf.com/windsurf/cascade/skills)                                            |
| Antigravity | `~/.gemini/config/skills/`                           | [Authoring Antigravity Skills](https://codelabs.developers.google.com/getting-started-with-antigravity-skills) |

These paths change between releases — if the skill doesn't show up, check the linked docs.

### 2. Claude Code

Personal skill (available in every project):

```bash
mkdir -p ~/.claude/skills
cp -r /path/to/ai-guidelines-skills/.agents/skills/ai-guidelines ~/.claude/skills/
```

Project skill (shared with your team via the repo):

```bash
mkdir -p .claude/skills
cp -r /path/to/ai-guidelines-skills/.agents/skills/ai-guidelines .claude/skills/
```

Claude Code loads the skill on demand based on its `description`, so nothing else is required.

### 3. Tools Without Agent Skills Support

Paste or reference the body of `SKILL.md` (everything below the YAML frontmatter) in:

- `AGENTS.md` / `CLAUDE.md`
- Your tool's rules file
- Or your agent's system prompt directly.

Pasted this way the guidelines are always in context instead of loading on demand.

---

## 🤝 Contributing

Contributions, improvements, and translations are welcome! If you find edge cases where AI agents tend to fail, feel free to open an issue or submit a pull request with refined rules. Include the failure you saw: the prompt, what the agent did, and what it should have done.

---

## 📜 License

This project is licensed under the [MIT License](LICENSE).
