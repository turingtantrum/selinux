# CLAUDE.md — SELinux Learning Project Instructions

This file contains persistent instructions for Claude Code sessions working on this repository.
Read this file at the start of every session.

---

## Project Purpose

This repository is being used as a **structured SELinux learning resource**.
The learner is a complete novice to Linux and programming.
All teaching is done inside this repo, using its source files as reference material.

---

## How to Resume a Learning Session

When the user says they want to continue learning, do the following:

1. Read `SELINUX_LEARNING_LOG.md` in the repo root
2. Find the first module with an unchecked box (`- [ ]`)
3. Announce which module you are resuming from
4. Deliver that module using the teaching format described below

> **User resume prompt:** "I am following the SELinux learning log. My progress is tracked in
> `SELINUX_LEARNING_LOG.md` on branch `claude/learn-selinux-QSNyz`. Please read it and resume
> from the next unchecked module."

---

## HTML Output Format — MANDATORY

Every module MUST be delivered as a **self-contained HTML file**.

### File naming convention

```
learning/<phase>-<module>-<short-name>.html
```

Examples:
```
learning/phase-0-1-how-linux-works.html
learning/phase-0-2-linux-filesystem.html
learning/phase-0-3-users-groups-dac.html
learning/phase-1-1-what-is-mac.html
```

### File requirements

Each HTML file MUST be:
- **Self-contained** — all CSS and JavaScript inline, no external dependencies
- **Readable offline** — open directly in any browser with no internet needed
- **Rich** — include all of: SVG diagrams, tables, syntax-highlighted code blocks,
  interactive tabs/accordions, visual workflows, attack scenario steps
- **Dark-themed** — use the dark design system defined in existing files

### After writing each HTML file

1. `git add learning/<filename>.html`
2. Tick the checkbox in `SELINUX_LEARNING_LOG.md` for that module (`- [ ]` → `- [x]`)
3. Update the Progress Overview table counts in `SELINUX_LEARNING_LOG.md`
4. `git add SELINUX_LEARNING_LOG.md`
5. `git commit` with a clear message
6. `git push -u origin claude/learn-selinux-QSNyz`

---

## Teaching Format — Every Module

Every module HTML file MUST cover these sections in order:

1. **Why do we need this?** — motivation, the problem it solves
2. **Core Concepts** — theory with SVG diagrams and analogies
3. **Analogies** — simple real-world comparisons to make concepts stick
4. **When to use it** — practical guidance
5. **How to configure it** — code examples with syntax highlighting
6. **What it protects from** — security value explained
7. **Boot & Runtime behaviour** — what happens at boot and during operation
8. **Attack Scenario** — a realistic ECU attack, step-by-step visual walkthrough,
   showing exactly how this concept stops (or fails to stop) the attack
9. **Intrusion Detection** — how this concept enables detection of attacks (if applicable)
10. **Forensics** — how to use this concept after an incident to investigate
11. **Key Terms table** — all new terms introduced, defined
12. **Summary** — card-based visual recap

---

## Teaching Style

- The learner is a **complete novice**. Explain every term, including basic Linux and
  programming concepts. Do not assume prior knowledge.
- Use **analogies** for every major concept to help retention.
- **ECU context**: All examples must be tied to automotive embedded scenarios:
  CAN bus, OTA updates, V2X, UDS/OBD-II diagnostics, ASIL safety levels,
  Xen hypervisor partitioning on ARMv8.
- **Platform**: Xen + ARMv8. Where relevant, explain down to the ARMv8 hardware level
  (Exception Levels EL0–EL3, TrustZone, MMU, SMMU).
- **Delivery**: One module per session. Wait for "next" or "continue" before advancing.

---

## Branch

Always work on: `claude/learn-selinux-QSNyz`
Never push to `main`.

---

## Progress Tracking

- Module progress is tracked in: `SELINUX_LEARNING_LOG.md` (repo root)
- HTML modules live in: `learning/` (repo root)
- After each module: tick the checkbox, update progress counts, commit, push

---

## Curriculum Summary

73 modules across 11 phases. See `SELINUX_LEARNING_LOG.md` for the full list.

```
Phase 0  — Linux Foundations          (5 modules)
Phase 1  — SELinux Concepts           (7 modules)
Phase 2  — CIL Language               (6 modules)
Phase 3  — User, Role, Type, Level    (6 modules)
Phase 4  — Classes & Permissions      (4 modules)
Phase 5  — Access Vector Rules        (9 modules)
Phase 6  — Labeling Everything        (9 modules)
Phase 7  — Conditionals & Booleans    (3 modules)
Phase 8  — Building & Deploying       (6 modules)
Phase 9  — Automotive / ARMv8 / Xen  (10 modules)
Phase 10 — Advanced Topics            (5 modules)
Phase 11 — Debug, Analysis, Forensics (7 modules)
```
