---
title: DownTime
date: 2026-03-07
description: I built a tool to make job searching less soul-crushing. It worked well enough that I went back to school instead.
tags:
  - python
  - llm
  - automation
  - job-search
categories:
  - projects
---

Job searching is tedious in a very specific way. You spend a lot of time reading variations of the same posting, trying to sort out which opportunities are real, which ones are already spoken for, and which ones are worth the effort of a real application. The signal-to-noise ratio leaves something to be desired.

I had some downtime between positions and, being who I am, decided the correct response was not to work through the process like a functional adult — but to build something that would do most of it for me.

The result was **DownTime**: a Python application that scraped job posting sites on a schedule, pulled new listings as they appeared, and fed each one to the [Google Gemini API](https://ai.google.dev/) for analysis. Gemini's job was to read the posting and score it against a profile of what I was actually looking for — not just keywords, but the texture of the role. Seniority expectations. Tech stack alignment. Whether the position was a genuine fit or just adjacent enough to be a waste of everyone's time.

The scored and ranked results landed on a locally hosted web dashboard. New listings showed up with their scores, a brief summary of why Gemini ranked them the way it did, and enough context to decide in about fifteen seconds whether a posting was worth pursuing. The ones that weren't, I didn't have to read.

It worked well. I found myself spending far less time in the job boards and more time on applications that were actually worth sending. The irony of building an AI-powered job search tool using exactly the kind of skills I was trying to get hired for was not lost on me — but that's usually how these things go.

And then I enrolled in school.

The decision to pursue a BS in IT Management wasn't sudden, but it was conclusive. At some point the math gets simple: certain doors open faster with the credential than without it, and I've spent enough time being good at things I can't prove on paper. DownTime got shelved mid-development — functional, but not quite finished.

It's up on [GitHub](https://github.com/golemedia/downtime) in its current state — rough edges and all. The scraper works, the Gemini integration works, and the dashboard does what it's supposed to do. It's not polished, but it's real, and it solves a real problem. I'll get back to it when the semester gives me room.

I'll keep you posted.
