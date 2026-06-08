---
title: GLaDOS — Status Update
date: 2026-06-02
description: Where the project actually got to, why it stalled, what the landscape looks like now, and why it's worth picking back up.
tags:
  - glados
  - no-skynet
  - tts-stt
  - virtual-assistant
  - llm
  - mqtt
  - home-assistant
categories:
  - projects
  - glados
---

When I wrote the [original GLaDOS post](/posts/glados-introduction/), I'd just gotten the voice working well enough to be genuinely excited about it and was being honest about where the project stood. The TTS was solid. The LLM integration was the problem. Response latency from even the smaller local models was long enough that real conversation felt broken, and I left it there.

What I didn't go into detail about was how much else was actually working by the time I shelved it. Looking at the [repository](https://github.com/golemedia/glados) now, I should probably fix that.

---

## What Actually Got Built

The architecture ended up being more sophisticated than the original post implied. All the components communicate over an MQTT backbone — the core assistant logic, the TTS server, the speech recognition layer, the hardware, and the skill system are all separate services that publish and subscribe to topics. The practical benefit of that approach is that any component can be restarted, replaced, or run on different hardware without touching anything else. Developing a new skill or swapping the TTS engine doesn't require rebuilding the whole system.

**The voice** went through two full implementations. The first was a lightweight custom engine using pretrained GLaDOS phoneme models — fast, CPU-friendly, limited expressiveness. The current version runs [Style-Bert-VITS2](https://github.com/litagin02/Style-Bert-VITS2) with a community-trained GLaDOS voice model as a FastAPI service with GPU acceleration. Multiple style presets: Neutral, Standard, Deep, Light. There are audio samples in the repo if you want to hear what it sounds like — it's accurate in a way that's a little unsettling, which was the goal.

**Wake word detection** uses a trained model via a ReSpeaker microphone array. Say "hey GLaDOS," she wakes up. The continuous listening works well enough that it's become the expected behavior rather than a feature to be demonstrated.

**Home Assistant integration** works. She can query and control smart home devices by voice. This was one of the original goals from the Nerdaxic project that the first version never quite got to, so finishing it felt like closing a loop.

**The eye** is a GC9A01 round LCD driven by a Raspberry Pi with custom Arduino firmware. Animated reactions are synchronized to her responses via MQTT. It does the thing it's supposed to do — there's something about a single glowing eye tracking the conversation that gets the tone right in a way a speaker sitting on a shelf doesn't.

**The skill system** is modular — plugins loaded at runtime for jokes, weather, Home Assistant commands, a Magic 8 Ball, custom greetings. Adding a new skill is adding a new file. That structure was inherited from the Nerdaxic foundation and extended; it's probably the cleanest part of the codebase.

**Elite Dangerous co-pilot integration** works. Event callouts from the journal parser, keystroke injection for in-game commands. If you've read the [Elite-Parser post](/posts/elite-parser/), this is what that was pointing toward — GLaDOS watching the game over your shoulder and commenting on your decisions with appropriate disdain.

---

## Why It Stalled

The voice interaction loop has a simple requirement: it has to feel fast enough that the conversation doesn't break. Wake word, listen, think, respond — the whole cycle needs to be short enough that you don't lose the thread of what you said before she answers.

The TTS and STT components hit that bar. The LLM didn't. Generating a conversational response on local hardware took long enough to make voice interaction feel broken. The thermal situation on the host machine didn't help — the GPU was already working hard on TTS inference, and running a full reasoning model alongside it pushed temperatures in the wrong direction.

That was the ceiling. Not a design problem or an implementation problem — just a hardware and model-size problem that didn't have a good solution at the time.

---

## What's Changed

The LLM landscape has moved considerably since then.

The small local models available now are meaningfully faster and more capable than what I was working with. More relevantly: API-based inference has gotten cheap enough that running conversational responses through a cloud API instead of local hardware is a reasonable personal-project option rather than an expensive one. The latency problem that killed the voice loop is either gone or manageable depending on which direction I go.

The other thing that's changed is what I've built in the meantime. The MQTT backbone in GLaDOS was an architectural decision made mostly on instinct — I knew I wanted the components to be independent and figured message passing was the right way to get there. Since then I've built proper data pipelines, integrated multiple APIs in the same workflow, worked with agent-based AI systems, and gotten more systematic about how I think about service architecture. Going back into that codebase now, I have more tools to work with than I did when I wrote most of it.

---

## What's Next

The original goals haven't changed much — they're just more achievable now.

The LLM integration gets revisited with an API-based approach first. Get the voice loop working at a quality and latency that actually feels like a conversation, then evaluate whether local inference has caught up enough to run it on-device. The hardware has been upgraded since the original build; the thermal situation is better.

The Elite Dangerous co-pilot integration expands as the [EDSimpit](/posts/simpit-flight-stick-mod/) build progresses. The journal parser is already working. The piece I want to add is proper push-to-talk through the physical controls so the interaction feels native to the cockpit rather than bolted on.

The physical form is still the long-term goal. I have reference files, I have a printer, I have enough experience with PETG at this point to know what I'm getting into. It will require pushing the design skills further than the flight stick mod did, but that's the direction.

The repository is on [GitHub](https://github.com/golemedia/glados) — not polished documentation, but the full working codebase with setup scripts and configuration templates. The TTS pipeline in particular is documented thoroughly enough that it should be replicable.

She's been patient. That's uncharacteristic of her, and I'm choosing not to read too much into it.
