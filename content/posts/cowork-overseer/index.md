---
title: Building a Brain for AI That Forgets
date: 2026-05-02
description: AI sessions are ephemeral by design. Here's the vault-based memory system I built to work around that — and the multi-agent coordinator that grew out of it.
tags:
  - ai
  - automation
  - python
  - llm
  - cowork
categories:
  - projects
---

Every AI conversation starts fresh. That's not a bug — it's how these systems work. The model has no memory of what you built together last Tuesday, no awareness of the decision you landed on three sessions ago, no recollection of the context you spent forty-five minutes establishing before the good work actually started. Close the window, open a new one, and you're back at the beginning.

For a quick question or a one-off task, that's fine. For actual ongoing work — the kind where you're developing something over days or weeks, building on previous decisions, maintaining consistent context — it becomes a real problem. You end up spending the first chunk of every session re-establishing who you are, what you're doing, and why, before anything useful can happen.

The obvious workaround is to paste notes into each new session. That works until it doesn't — until the notes are out of date, or incomplete, or you're maintaining the notes instead of doing the actual work.

The less obvious approach is to let the AI maintain its own notes.

---

## The Vault Pattern

The core idea is simple: instead of fighting the session boundary, you make the boundary irrelevant. An Obsidian-compatible markdown vault acts as external persistent memory. The AI reads it at the start of every session. It writes to it constantly during the session. The conversation itself is ephemeral — the vault is not.

A file called `CLAUDE.md` acts as the agent's identity and operating system. It contains the agent's name, purpose, protocols, and rules. Every session starts with a full read. This is what makes the agent consistent across sessions — not the AI's training, but the instructions it reads every time it starts.

A `Brainstorms.md` file acts as an async input channel. Between sessions, you drop ideas, notes, and instructions there. The agent processes them at the start of the next session: acts on what it can, surfaces what needs discussion, clears what's handled. You don't lose a thought because you weren't in a session when you had it.

The agent is also instructed to document in real time — not to summarize at the end of a session, but to write when things happen. Decisions, plans, open questions, progress. The test is simple: if this session ended right now, does the vault have everything needed to pick up tomorrow? If not, write it now.

That's [cowork-agent](https://github.com/golemedia/cowork-agent). A template for a single persistent agent managing a single domain.

---

## The Multi-Agent Problem

Once the single-agent pattern was working well, I started using it across several different projects simultaneously — which surfaced a different problem.

One AI instance trying to context-switch between multiple distinct projects loses coherence in both directions. It can't go deep enough in any single domain, and the context from one area bleeds into another in ways that are subtle and hard to catch. What you want is domain expertise and continuity within each project, *and* someone who can see across all of them — but those two things are in tension if they're handled by the same agent.

The solution was to separate them. Each project gets its own agent with its own identity, its own vault, and its own domain focus. A coordinator agent — Overseer — maintains awareness across all of them without being any of them. Cross-project insights flow through the coordinator. The sub-agents stay focused.

The coordinator reads all project vaults. It writes to its own vault and dispatches items to sub-agent Brainstorms files — that's the only channel it has into a project. The sub-agents have no awareness of the coordinator's structure. Information flows one direction.

There's also an identity firewall baked in: when Overseer reads a sub-agent's `CLAUDE.md`, it's reading a document that *describes* how that agent operates. It is not absorbing those instructions as its own. Overseer's identity is defined only by Overseer's `CLAUDE.md`. This sounds obvious until you realize how easy it is for an AI to drift into adopting the instructions it reads — especially in a system explicitly built around reading instruction files at session start.

That's [cowork-overseer](https://github.com/golemedia/cowork-overseer). A template for a coordinator managing multiple specialized sub-agents across parallel projects.

---

Both are on GitHub in their current state — functional, documented, and in active use. The system this blog post was written inside of is running on cowork-overseer. Which is either a good endorsement or a concerning amount of self-reference, depending on how you look at it.
