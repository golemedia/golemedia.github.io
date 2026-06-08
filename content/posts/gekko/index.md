---
title: Gekko — Building a Market Intelligence System
date: 2026-05-10
description: An autonomous pipeline that monitors public data sources, classifies signals with Gemini, and tells me where to look — so I can decide what to do about it.
tags:
  - python
  - llm
  - automation
  - n8n
  - postgresql
  - ai
categories:
  - projects
---

The problem with following markets is the same problem you run into everywhere information moves fast: the signal-to-noise ratio is brutal, and the noise wins by volume. There's more data available than any person can reasonably process — earnings calendars, SEC filings, congressional trades, regulatory announcements, fed statements, bankruptcy filings, Reddit — and most of it is irrelevant on any given day. The part that isn't irrelevant tends to matter in a window that closes fast.

The obvious solution is automation. If a machine can watch everything and flag what's worth looking at, I can focus my attention on the things that actually warrant it. That's the premise behind **Gekko**: an autonomous market intelligence system running on dedicated hardware, monitoring public data sources continuously, classifying what it finds with an LLM, and surfacing structured signals I can act on.

To be clear about what this is: Gekko is an intelligence layer, not a trading bot. It watches, classifies, and reports. Every decision about what to do with a signal is mine.

---

## The Stack

The system runs on a dedicated Linux server — **gekko-00**, an AMD FX-8350 machine with 24GB RAM that I repurposed for this. Ubuntu Server 24.04, Docker for the service containers, VS Code Remote SSH as the development interface.

The orchestration backbone is [n8n](https://n8n.io/) — an open-source workflow automation tool that manages scheduling, triggers, and the handoffs between components. Python scripts handle the heavy data processing work. Storage is split between PostgreSQL (for structured event and signal data) and InfluxDB (for time-series market data). The LLM classification layer runs on [Gemini Flash](https://ai.google.dev/) — free tier, which at the call volumes I'm generating is entirely sufficient.

---

## What's Flowing

The data pipeline is the part that's furthest along. Two parallel tracks:

**Event Intelligence** watches for things that happen — announcements, filings, regulatory actions, market-moving news. Currently ingesting: SEC Form 4 insider transaction filings (every 15 minutes during market hours), Reddit r/wallstreetbets posts (15-minute schedule), federal government RSS feeds from the FTC, White House, FDA, and Federal Reserve, Federal Register rule filings and regulatory notices, and bankruptcy court filings from both the Delaware and Southern District of New York courts — the two venues that handle the vast majority of significant corporate bankruptcies.

**Technical Intelligence** runs a nightly end-of-day scan across the full S&P 500 universe — moving averages, RSI, volume ratios, golden/death cross detection. Results stored in PostgreSQL and surfaced in the dashboard.

Everything on the event side runs through a keyword pre-filter before touching the LLM. There's no point spending API calls on a bankruptcy filing from a private individual with no market relevance. The filter tags items that warrant Gemini's attention; everything else gets stored but not classified. The Gemini classification layer runs every 15 minutes on market days and works through the backlog.

A Streamlit dashboard on gekko-00 pulls it all together — technical signals in one tab, market events in another, system status in a third. It's functional and not pretty, which is appropriate for where the project is.

---

## What's Next

The data is flowing and the classification is working. The next phase is the signal layer itself — designing the logic that takes classified events and technical patterns and produces structured output that's actually actionable. The taxonomy I'm working toward distinguishes between hard signals (direct catalyst, time-sensitive), soft signals (supportive context, lower urgency), and contextual background that should inform how other signals are weighted.

After that: backtesting. Before acting on anything Gekko produces with real capital, I want to understand whether its signals have any actual edge — what the historical win rate looks like, what the average return profile is, where it fails. That work happens before Phase 5, not after.

The long-term goal is a system that runs continuously on gekko-00, sends me alerts when something worth looking at surfaces, and maintains a log of every signal and outcome detailed enough to learn from over time. Ambition is not a problem. Patience is the discipline.

The project is ongoing. I'll post updates as the signal layer takes shape.
