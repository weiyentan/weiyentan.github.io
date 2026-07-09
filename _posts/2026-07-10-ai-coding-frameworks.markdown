---
title: "AI Coding Frameworks"
comments: true
date: 2026-07-10 10:00:00 +1200
categories:
  - general
tags:
  - ai
image: /images/cover.jpg
---

I've used AI in my coding workflow right from the start. At first that meant ChatGPT in a browser, then integrations moved into IDEs. That shift was the real beginning of using a proper "harness" — a framework around an LLM that lets it interact with tools, files, repos, CLIs, issue trackers, and automation systems.

Throughout most of that evolution I was still micromanaging every step: planning every change, correcting every misunderstanding, accepting the error rate as just how coding with AI worked. Then about a month ago I found Matt Pocock's AI engineering videos and a few things clicked.

**Context windows matter.** Even when a model technically supports a large context, there's a practical "smart zone" where it performs best. A single long-running agent fills its context with history, assumptions, and noise. The answer isn't a smarter agent — it's subagents. Focused agents pick up jobs, complete them, and pass results along without dragging everything forward forever.

**Hallucination happens when the AI doesn't understand what we want.** Planning helps, but humans miss details too. That's where grilling skills come in: instead of jumping straight into implementation, the AI challenges the idea, asks questions, explores edge cases, and forces clarity. With `grill-with-docs`, it also writes architecture notes and decisions that future agents can reference instead of relying on one giant chat history.

From there, grilling turns into a PRD — what's being built, why, the expected behaviour, edge cases, and constraints. The PRD gets split into implementation issues via `/to-issues`. Those issues can be picked up by the AFK workflow.

The development flow is: issue → `AFK` label or command trigger → automation → orchestrator pipeline → branch → PR/MR. I extended this to reviews too: commenting `/afk_review` on a PR triggers the review pipeline instead of me manually copying diffs into ChatGPT.

**The orchestrator pipeline uses tiered subagents.** The Orchestrator owns the flow and progress. The Task Router assesses complexity and risk, then assigns work to the right tier: Junior (DeepSeek Flash, low-risk mechanical tasks), Mid (DeepSeek Flash with thinking, medium complexity), or Senior (DeepSeek Pro, complex refactors). A separate Reviewer Agent checks work against intent — coders don't review themselves. Each agent has its own prompt that steers its role, boundaries, and behaviour. The model provides reasoning; the prompt provides the job description.

**Tools and plugins reduce guessing.** Instead of the AI inventing a CLI command from memory, a well-defined tool handles the interaction with AWX, GitHub, GitLab, or other systems. The model reasons about what needs to happen; the plugin handles how. Prompts steer behaviour, documents give context, and tools give reliable ways to act — that combination is what reduces hallucination and drift.

**The backend is event-driven.** GitHub and GitLab events go through a webhook container that filters and normalises them, then publishes clean events to Kafka. EDA (Event-Driven Ansible) listens for those events, matches them against rules, and decides what automation to trigger. AWX runs the actual jobs: checking out repos, preparing context, launching the OpenCode agent workflow, and posting results back. The full flow is: repo event → webhook container → Kafka → EDA → AWX job → OpenCode orchestrator → subagents develop or review → result posted back to the repo.

The important concept isn't the specific tools — it's that repo-native events become the interface for triggering agent work, and the backend turns those events into a controlled pipeline. The AFK workflow picks up the request, routes it by complexity, assigns the right agent, reviews the result, and posts back. I'm no longer walking the AI through every step.

It still needs corrections, but the feeling has shifted. It doesn't feel like mindless autocomplete. The agents have clearer context, better boundaries, and a stronger understanding of what I actually want. The goal isn't just "AI writes code" — it's a system where AI takes well-defined work, operates inside a harness, preserves context through documents, hands work between tiered agents, uses prompts to steer behaviour, uses tools to avoid guessing, and reacts to repo-native events. That's what this AFK system is becoming.
