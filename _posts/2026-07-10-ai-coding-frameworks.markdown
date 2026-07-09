---
title: "AI Coding Frameworks"
comments: true
date: 2026-07-10 10:00:00 +1200
categories:
  - general
tags:
  - ai
---

I’ve used AI in my coding workflow right from the beginning.

At first, that meant programming with ChatGPT in a web interface. Then it moved into IDEs, which was really the beginning of using a proper harness.

By “harness”, I mean a framework around an LLM that lets it interact with the outside world: tools, files, repositories, web access, command-line interfaces, issue trackers, and automation systems.

ChatGPT itself is a kind of harness. In the early days it was mostly locked inside its own box, limited to what its training data knew. Then tools were added. Then web access. Then file uploads. Then IDE integrations. Each step gave the model more ways to interact with real systems.

But throughout most of that evolution, I was still micromanaging it.

I would tell the AI every step. I would plan every change. I would guide it through each bug or feature. If it misunderstood something, I would correct it manually. I accepted the margin of error as “just the nature of coding with AI”.

Then about a month ago, I came across Matt Pocock’s AI engineering videos, and a few things clicked for me.

One of the big ideas is that context windows matter. Even when a model technically supports a very large context window, there still seems to be a practical “smart zone” where the model performs best. Once the context gets too full, quality starts to degrade.

That explained a lot of what I was seeing.

When you use one long-running agent for everything, the context fills up quickly. The agent starts carrying too much history, too many assumptions, and too much noise.

The answer is not necessarily “one smarter agent”.

The answer is often subagents.

Subagents can pick up focused jobs, complete them, and pass the relevant result along. Their context fills up, but the whole system does not have to carry every detail forever. Each agent can stay focused on its part of the workflow.

The other issue is hallucination.

What I took from Matt Pocock’s approach is that hallucination often happens when the AI does not have a clear enough understanding of what we want. Planning helps, but humans are fallible too. We miss details. We leave gaps. And when there are gaps, the AI tries to fill them.

That is where the engineering skills became really useful.

Instead of jumping straight into implementation, the workflow flips the role of the AI during planning. The AI does not just say “yes” and start coding. It grills the idea.

Using skills like `grill-me` or `grill-with-docs`, the AI starts with the rough feature or bug and then challenges every path through it. It asks questions. It explores edge cases. It forces clarity.

With `grill-with-docs`, it also starts producing documents: architecture notes, decisions, assumptions, and supporting context. Those documents become useful later because future agents can refer back to them instead of relying on vague memory or one giant chat history.

Once the grilling is done, the next step is to turn the discussion into a Product Requirements Document.

A PRD outlines what is being built, why it matters, the expected behaviour, the edge cases, and the constraints. From there, the PRD can be turned into implementation issues.

For me, that is where `/to-issues` comes in.

It takes the PRD and turns it into bite-sized tasks that can be picked up by the AFK workflow.

This is where my own system comes in.

I’ve built a workflow that can pick up issues from local files, GitLab, or GitHub and process them. There are a few ways to trigger it.

One way is through repo-native signals.

Instead of manually telling the AI what to work on, I label an issue as `AFK`.

That label becomes the trigger for development work.

Another way is through a more direct command-style trigger, such as running something like `develop-loop`, where I explicitly ask the system to pick up a piece of work and move it through the agent workflow.

That was where I started.

The `develop-loop` approach worked well, but it mostly ran from my own machine.

The problem was that I did not want the AFK workflow to be tied to whether my laptop or desktop was on, configured correctly, or in the right state. If this was going to become a real AFK system, it needed to live outside my personal machine.

So the goal became moving from:

my machine runs the loop

to:

the repo event triggers the backend, and the backend runs the workflow

Both approaches lead to the same idea: I am no longer manually walking the AI through every individual step. I am giving the system a clear unit of work, and the workflow takes it from there.

The development flow looks like this:

issue → `AFK` label or command trigger → automation → orchestrator pipeline → branch → merge request or pull request

That worked well, but I noticed another manual step.

I still wanted AI to review my merge requests and pull requests. So I would copy commits or diffs into ChatGPT and ask it to review them.

That meant I was still in the loop.

So I spent another couple of weeks extending the workflow.

Now, for merge requests and pull requests, my manual review process has mostly been replaced by a repo-native comment.

I comment `/afk_review` on a merge request or pull request.

That comment becomes the trigger for the review workflow.

The review flow looks like this:

merge request or pull request → `/afk_review` comment → automation → review pipeline → result posted back to the repo

The important part is that these triggers do not just start “an agent”.

They start a pipeline.

My AFK system has an orchestrator pipeline with subagents.

The Orchestrator Agent owns the overall flow. It keeps track of the stage of the work, makes sure the process keeps moving, and decides what needs to happen next.

The Task Router Agent works out the path through the system. It looks at the task, estimates complexity and risk, and decides which developer tier should handle it.

From there, the work can be assigned to different coding agents.

A Junior Developer Agent handles low-complexity, low-risk work. For that tier, I use DeepSeek Flash. These are tasks where the scope is clear and the work should be fairly mechanical.

A Mid-tier Coding Agent handles medium-complexity work where there are more moving parts. For that tier, I use DeepSeek Flash with thinking enabled, because the task usually needs more reasoning but does not always justify the highest-cost model.

A Senior Developer Agent handles higher-risk or more complex work where deeper reasoning is needed. For that tier, I use DeepSeek Pro.

There is also a Reviewer Agent that sits separately from the coding agents. Its job is not to blindly continue implementation. Its job is to review the work, check whether the changes match the intent, and enforce standards.

That separation matters.

The coding agent should not be its own reviewer. The reviewer should not be the one rushing to implement. The orchestrator should not be writing every line of code.

Each agent has a job, and each tier has a cost profile.

That is where the system starts to feel less like “AI autocomplete” and more like an automated engineering workflow.

The other important piece is prompting.

Each primary agent and subagent has its own prompt that steers how it behaves.

The Orchestrator Agent has a prompt that tells it to manage the flow, not blindly implement.

The Task Router Agent has a prompt that tells it to assess the work, classify complexity and risk, and choose the right developer tier.

The Junior, Mid-tier, and Senior Developer Agents each have prompts that define the kind of work they should take on, how much autonomy they have, and what kind of output is expected.

The Reviewer Agent has a different prompt again. Its job is to be critical, check the work against the original intent, and avoid simply agreeing with the implementation.

That matters because the prompt is the steering mechanism.

The model provides the reasoning capability, but the prompt defines the role, the boundaries, and the behaviour.

Without that, the system would just be a pile of agents.

With it, the agents start to behave more like a coordinated team.

The other thing I do is create tools and plugins for OpenCode.

That matters because I do not want the AI to guess how to do infrastructure work.

If the agent needs to interact with AWX, GitHub, GitLab, a local issue file, or some other system, I would rather give it a proper tool with a defined interface than ask it to invent a command from memory.

The plugin becomes the safe path.

Instead of the agent guessing:

“maybe I should call this CLI command with these flags”

it can use a tool that already knows how to do the thing properly.

That keeps the AI inside a better operating boundary.

The model can reason about what needs to happen, but the plugin handles how it happens.

That distinction has been really useful.

Prompts steer the agent’s behaviour.
Documents give the agent context.
Tools and plugins give the agent reliable ways to act.

That combination is what reduces the guessing.

Underneath the hood, OpenCode provides the harness layer for the agents.

OpenCode has integrations for platforms like GitHub, GitLab, and local file-based workflows. It can use the relevant platform tooling and APIs to read issues, branches, pull requests, merge requests, comments, and repo metadata.

For many people, those integrations would probably be enough.

In my case, I deliberately did not make the built-in integrations the whole system. I wanted to build my own event-driven infrastructure around it because part of the point of this project was learning how the backend pieces fit together.

I wanted to understand the moving parts: GitHub and GitLab events, webhooks, event routing, messaging, EDA, AWX, agent runners, workflow selection, and repo feedback loops.

So I built a backend path around it.

In my setup, GitHub and GitLab do not talk directly to the whole automation stack.

I have a small webhook container in front of it.

That container receives the raw GitHub/GitLab webhook, filters out events that do not matter, and normalizes the useful ones into a cleaner shape.

For example, it checks whether an issue was labelled `AFK`, or whether someone commented `/afk_review` on a merge request or pull request.

If the event is irrelevant, it gets dropped.

If the event is relevant, the webhook container publishes a cleaner event into Kafka.

Kafka acts as the messaging layer between the webhook gateway and the automation backend.

That matters because it decouples the systems. The webhook container does not need to know exactly how the automation will run. It just publishes a valid event. The backend can then consume that event and decide what to do with it.

From there, I use tools like EDA and AWX.

EDA stands for Event-Driven Ansible. In plain terms, it is the event listener and rule engine. It listens for events, such as the clean event from Kafka, checks it against rules, and decides what kind of automation should be triggered.

AWX is the automation runner. It is the open source upstream project behind Ansible Automation Platform. In my setup, AWX runs the actual job templates and playbooks: checking out the repo, preparing the context, launching the right OpenCode agent workflow, and posting the result back to GitHub or GitLab.

So in plain terms:

The webhook container filters the event.
Kafka carries the clean message.
EDA decides what should happen.
AWX runs the automation.
OpenCode agents do the development or review work.
The result gets posted back to the repo.

The backend flow looks like this:

GitHub/GitLab event → webhook container → Kafka → EDA rule → AWX job → OpenCode orchestrator pipeline → subagents develop or review → result posted back to GitHub/GitLab

You could build the same idea with other tools. The important concept is not Kafka, EDA, or AWX specifically.

The important concept is that repo-native events become the interface for triggering agent work, and the backend turns those events into a controlled automation pipeline.

It is genuinely surreal watching it happen.

The AFK workflow picks up the request, sends it through the orchestrator, routes it based on complexity and risk, assigns it to the right coding agent, reviews the result, and posts the outcome back to the repo.

The biggest benefit has been alignment.

There are still second passes. There are still corrections. But it feels different now.

It does not feel like the AI is mindlessly doing tasks.

It feels like the agents have better context, clearer boundaries, and a much stronger understanding of what I actually want.

That is the real shift for me.

The goal is not just “AI writes code”.

The goal is building a system where AI can take well-defined work, operate inside a useful harness, preserve context through documents, hand work between tiered subagents, use prompts to steer behaviour, use tools to avoid guessing, react to repo-native events, and reduce the amount of micromanagement needed from me.

That is what my AFK system is starting to become.
