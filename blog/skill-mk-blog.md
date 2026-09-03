---
title: Your AI Agent Is Loading Too Much — SKILL.mk Fixes That
published: true
tags: ai, devtools, llm, productivity
cover_image: <!-- Add a cover image URL here -->
---

## The Problem Nobody Talks About

If you're building with AI coding agents — think Claude Code, Copilot, or custom agent frameworks — you've probably written a SKILL file. These are instruction documents that teach your agent *how* to do things: search the web, triage GitHub issues, scaffold a project.

Most of these are written in Markdown (`.md`). And they work fine... until you realize your agent is loading **thousands of tokens** of instructions it doesn't even need for the current task.

That's like reading an entire cookbook when you just want to boil an egg.

## Enter SKILL.mk

A developer named Zhou Chang recently open-sourced a clever idea: [SKILL.mk](https://github.com/Teaonly/SKILL.mk) — writing agent skill documents in **Makefile format** instead of Markdown.

Yes, *that* Makefile. The same format developers have used for decades to build software.

But why?

## The Core Idea in 30 Seconds

A regular SKILL.md file is a flat wall of text. The agent loads *all of it* every time, even if it only needs one section.

A SKILL.mk file breaks the skill into **targets** (think: named sections) with **dependencies** between them — just like a Makefile:

```makefile
when-to-use:
    - Searching for documentation
    - Looking up facts or current information

setup: when-to-use
    1. Create an API account
    2. Get your API key

query: when-to-use setup
    @search.js "your query" -n 10
```

Each target knows what it depends on. The agent only loads what it needs.

## Why Should You Care? Three Real Benefits

### 1. Dramatically Fewer Tokens

In the Brave Search skill example from the repo, on-demand loading cuts token usage by **85%**. Across a full collection of 20+ skills, the average reduction was **14%** — with some skills shrinking by over **50%**.

Fewer tokens = faster responses, lower costs, and less chance of the agent getting confused.

### 2. Built-in Logic (It's a DAG!)

Makefile targets naturally form a **directed acyclic graph** — a fancy way of saying "steps in order, with no loops." Your agent doesn't have to *figure out* the right sequence. It's already baked into the file structure.

`query` depends on `setup`, which depends on `when-to-use`. The agent follows the chain automatically. Less guessing means fewer errors.

### 3. You Can Track and Improve Individual Steps

Since each recipe is a separate, named target, you can:

- **Git-track** changes to individual steps
- **Measure** which recipes succeed or fail
- **Optimize** one recipe without touching the rest

This is what the project calls "Evolution Engineering" — letting your skills improve piece by piece over time.

## A Quick Before & After

| | SKILL.md | SKILL.mk |
|---|---|---|
| **Full load** | 2,165 characters | 2,014 characters (-7%) |
| **On-demand load** | Not possible | 313 characters (**-85%**) |
| **Auditability** | Whole file only | Per-recipe tracking |

## How to Try It

Creating your first `.mk` skill file takes under 5 minutes:

1. **Create a file** named `your-skill.mk` in your agent's skills directory
2. **Define your targets** — start simple with `when-to-use`, `setup`, and a main action:

```makefile
when-to-use:
    - Use this skill when you need to [describe use case]

setup: when-to-use
    1. [First setup step]
    2. [Second setup step]

run: when-to-use setup
    @your-command-here
```

3. **Register it** with your agent framework exactly as you would a `.md` file

Even without a compatible loader, the structure alone makes your skills easier to read, maintain, and version.

## Is It Ready for Production?

Not quite. SKILL.mk is currently a **proof-of-concept**. It requires a compatible loader tool in your agent framework to unlock on-demand loading. Without that tooling, it still works as a drop-in replacement for `.md` files — just with better structure.

The spec is intentionally minimal (only 4 rules), which makes it easy to implement in any agent harness.

## The Bottom Line

SKILL.mk takes an old, battle-tested format and applies it to a very modern problem: making AI agents more efficient. It's a small, elegant idea — structure your agent's knowledge like a build system, and only load what you need.

I'm experimenting with this in my own agent setups and the structure benefits alone are already worth it, even before any loader tooling is in place.

If you're building agents and tired of bloated context windows, it's worth a look.

**Check it out:** [github.com/Teaonly/SKILL.mk](https://github.com/Teaonly/SKILL.mk)

---

*Have you run into the token bloat problem with your own skill files? Are you using a different approach? Drop a comment — I'd love to hear how others are handling this.*
