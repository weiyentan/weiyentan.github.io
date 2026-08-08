---
title: "How My AFK Engineering Workflow Actually Works"
comments: true
date: 2026-08-08 10:00:00 +1200
categories:
  - general
tags:
  - ai
  - automation
image: /images/cover.jpg
---

In my previous post on AI coding frameworks, I described the AFK
workflow at a high level. This is the follow-on: less about the idea,
and more about what actually happens once work enters the system.

Before getting into it, I want to acknowledge
[Matt Pocock's skills](https://github.com/mattpocock/skills),
particularly `grill-with-docs`. That workflow was an important influence
on how I think about context.

For me, the useful part is not simply the grilling itself. The grilling
produces durable artefacts: Architectural Decision Records (ADRs) and a
`context.md` that captures the decisions, assumptions and boundaries that
would otherwise live only in a conversation.

An **Architectural Decision Record (ADR)** is a short document that records an
important technical or architectural decision: the problem being solved, the
options considered, the decision that was made, and the reasoning behind it.
The value is not just knowing *what* the architecture looks like, but preserving
*why* it looks that way.

A **Product Requirements Document (PRD)** describes what the product or feature
is supposed to achieve. It captures the problem, goals, scope, requirements,
constraints and acceptance criteria before that work is broken down into
implementation tasks.

A **directed acyclic graph (DAG)** is a collection of work items connected by
dependencies that point in one direction and never form a loop. In this
workflow, each issue is a work item: an issue can become ready when the issues
it depends on have been completed, while independent issues can proceed in
parallel.

From there, the PRD becomes implementation issues, and those issues form the
dependency graph the AFK workflow executes.

I have taken that idea and built it into a broader workflow around
persistent repository context, issue DAGs, task routing, event-driven
orchestration, bounded developer agents and adversarial review.

The interesting part, at least for me, is not getting an agent to write
code. That part is increasingly easy.

The harder problem is giving an agent enough context to do useful work
without giving it the entire world, deciding which agent should do the
work, constraining what it is allowed to touch, and then making sure
another agent is prepared to tell it that its implementation is wrong.

That is what the AFK workflow is really doing.

## Every Agent Wakes Up With Amnesia

One of the mental models I use for AI agents is a slightly nerdy one:
**Johnny Mnemonic**.

Each agent starts a job effectively brand new.

It does not have the lived history of the project. It was not in the
grilling session. It does not remember why a particular architectural
decision was made three weeks ago. It does not know which alternatives
were considered and rejected unless I give it that information again.

That changes how I think about "memory".

I do not want the workflow to depend on an agent remembering a previous
conversation. I want the **repository itself to carry the memory**.

That is why the output from planning matters so much:

```text
Grilling
   |
   +--> context.md
   |
   +--> ADRs
   |
   +--> PRD
          |
          v
       Issues
          |
          v
   Dependency DAG
```

`context.md` gives an agent the project-level orientation it needs.

ADRs preserve the reasoning behind important architectural decisions,
including the alternatives and constraints behind them.

The PRD turns the idea and its requirements into defined product work.

The issues turn that product work into executable units, and their
dependencies define the order in which those units are safe to execute.

In that sense, Git is doing more than versioning code. It is acting as
the durable memory substrate for a set of otherwise stateless agents.

A fresh agent can arrive, read the relevant project memory, receive a
bounded task, and operate without needing the entire history of every
conversation that came before it.

That is the strategy: **do not rely on the agent to remember the project;
make the project remember itself.**

## Spend Tokens Up Front

One of the design choices I have made is that I am quite happy to spend
tokens up front.

I do not try to make the planning phase artificially cheap.

I use grilling sessions to get an idea out of my head and make the
assumptions explicit. I write Architectural Decision Records (ADRs). I
spend time defining the problem, the boundaries and the decisions that
have already been made.

That context is an investment.

The payoff comes later, when the implementation agents do **not** need
to rediscover the architecture every time they are given a task.

Instead of handing a developer agent an issue and effectively saying,
"Here is the repository; work it out," I can give it a much smaller
problem.

That matters because context windows are not free. More context does not
automatically produce a better result. A large context window can
contain a lot of information that is irrelevant to the task at hand.

My approach is therefore almost the opposite:

> Spend context deliberately during planning so that execution can
> operate on less context.

This is also why I do not use one giant agent for the whole workflow.

## The Council: Challenging the Idea Before There Is Code

There is an additional step when I am starting a **brand new project**.

I call it the **Council**.

The Council is not part of normal feature development and it is not
something every issue passes through. If I am adding a feature to an
existing project, the Council normally has nothing to do with it.

It exists for greenfield work, where the architecture itself is still
being decided.

The important distinction is that the Council is not there to challenge
another agent.

It is there to challenge **me**.

I treat it a bit like an adversarial architecture board. I bring the
idea, and the Council pushes on it: assumptions, boundaries,
alternatives, failure modes, unnecessary complexity and decisions I may
be making without realising I have made them.

Each Council member plays a distinct role. The **Product Owner**
validates whether the problem is real, whether the proposed value is
genuine, and whether the scope is right-sized. The **Platform
Architect** pushes on architectural boundaries, operational concerns
and blast radius --- what does this change touch, and does the
architecture actually support it? The **Senior Engineer** validates
complexity, edge cases and maintainability --- is this the simplest
version that works, and can someone else pick it up later? The
**Delivery Planner** validates the decomposition into issues and cost
efficiency --- does this break down into work that is genuinely
independent, and are the issue sizes sensible?

The Council operates in up to two rounds. In each round, every role
writes an independent opinion. There is a convergence gate between
rounds: if average confidence is at least 0.85, there is no dissent,
and no role has made round-two requests, the second round is skipped.
That keeps the process lightweight when an idea is already solid,
while still allowing a second pass when the first round surfaces
genuine disagreement.

For low-risk ideas, the full four-role Council is overkill. I use a
lightweight tier instead: just the **Product Owner** plus one
technical role. That keeps the overhead proportional to the risk.

The output of the Council process is stored under
`.ai/council/{session-id}/` --- the individual briefs, per-role
opinions, the decision YAML, PRD outlines and issue breakdowns. Those
artifacts become part of the durable project context that downstream
agents can read.

And a scope reminder: the Council exists for greenfield projects
only. If I am adding a feature to an existing codebase, the
architecture is already decided. The Council is not relevant there.

The purpose is not to generate code. The purpose is to make the idea
harder to misunderstand.

The output of that process becomes durable project context: ADRs,
architectural boundaries, decisions, risks and eventually well-defined
pieces of work.

Once that exists, the Council gets out of the way.

The normal AFK workflow can begin.

## The Plan Becomes a DAG

The grilling process does not just produce a flat backlog of issues.

As the work is broken down, it also captures the dependencies between those issues. The result is a **directed acyclic graph (DAG)** of implementation work.

That distinction matters.

An issue is not ready to run simply because it exists or has been added to the backlog. It becomes eligible for execution when the issues it depends on have reached the required state.

A simplified plan might look like this:

```text
                 +--> Issue B --+
Issue A ---------+              +--> Issue D
                 +--> Issue C --+
```

`Issue A` has to be completed before `B` and `C` become eligible. Once both branches have satisfied their dependencies, `Issue D` can proceed.

This gives the AFK workflow something more useful than a queue. It gives it an execution plan.

Independent branches of the DAG can progress without waiting for unrelated work, while dependent work stays blocked until the context it relies on actually exists. That means concurrency comes from the shape of the plan rather than from simply throwing more agents at the backlog.

The DAG is also another place where the upfront planning work pays off. During grilling I am not only asking *what needs to be built?* I am working out *in what dependency order can it safely be built?*

By the time an issue reaches an implementation agent, the agent should not need to reconstruct that project plan for itself.

## The Issue Is the Handoff

By the time work reaches the AFK pipeline, I want the architectural
thinking to have already happened.

The issue is therefore more than a to-do item. It is the handoff between
planning and execution.

It carries the intent of the work, acceptance criteria, relevant context
and references back to the decisions that led to it.

But I still do not hand that entire body of context directly to a
developer agent.

There is another step first.

## The Task Router Is Where the Work Gets Bounded

The **Task Router** is probably the most important part of the execution
pipeline.

Its job is not simply to look at a task and choose an agent.

It reduces the problem.

Given an issue and the project context, the Task Router determines what
kind of work this is and produces a bounded execution plan. That can
include things such as:

-   which developer role should take the task;
-   which files or directories are relevant;
-   which parts of the repository are out of scope;
-   the constraints the developer needs to respect; and
-   what the expected result should look like.

I currently use different developer roles --- junior, mid-level and
senior --- rather than treating every implementation task as if it
requires the same agent with the same context.

The point is not to role-play an engineering organisation.

The roles give me a way to route different classes of work to different
prompts, capabilities and levels of reasoning.

More importantly, the router can give the selected developer only the
context it actually needs.

That is where the upfront investment in grilling sessions and ADRs
starts paying for itself.

The planning context can be large. The execution context does not have
to be.

## Use Traditional Code Wherever You Can

One of the most important principles in this workflow is that **AI should not
be used simply because AI is available**.

If a part of the workflow can be implemented reliably with traditional code,
I prefer to do that.

Traditional software is predictable. Given the same inputs and the same state,
I can expect the same behaviour. I can test it, version it, reason about it and
run it repeatedly without wondering whether the implementation will interpret
the task differently on the next execution.

That makes traditional code a much better fit for things such as:

- receiving and validating webhooks;
- normalising payloads;
- transporting events;
- evaluating known state transitions;
- checking whether dependencies are satisfied;
- triggering jobs;
- moving work between well-defined workflow states; and
- enforcing hard constraints.

AI is most useful where the work is inherently less deterministic: interpreting
a requirement, creating or transforming content, planning an implementation,
writing code, or reviewing a change.

I do not want an LLM deciding how to move an event from one system to another
if a few lines of deterministic code can make that decision perfectly well.

A useful rule of thumb for this architecture is:

> **Use code for control flow. Use AI for content and judgement.**

That separation is part of what makes the AFK workflow repeatable. The
automation surrounding the agents is deliberately conventional. The agents
operate inside that automation rather than replacing it.

## Where This Workflow Fits — and Where It Does Not

This workflow is not intended to handle every kind of engineering work.

The main use cases I designed it for are:

- **new feature implementation**;
- **bug fixes**; and
- other bounded pieces of repository work that can be described clearly, routed to the right agent, implemented, and reviewed.

Those are good candidates because they naturally fit the model used throughout
the AFK workflow: defined context, bounded scope, explicit dependencies,
implementation, and review.

There are plenty of cases where that machinery would be unnecessary or even
counterproductive.

A tiny change may be faster for a person to make directly than to send through
the full agent pipeline. Some problems are ambiguous enough that the correct
next step is a conversation rather than another automated job. And sometimes
an agent reaches a point where it should stop rather than guess.

For small, bounded changes and some bug fixes, I still interact directly with
the agent rather than forcing every change through the complete AFK workflow
from the beginning.

That is especially useful when a problem is found in the middle of an existing
review cycle. If the reviewer identifies a small bug or a narrowly scoped issue,
I can tell the developer agent to fix that specific problem directly.

The important part is that this does not break the surrounding workflow. Once
the developer has made the change, the same event-driven architecture can pick
the work back up and continue using the existing framework: the repository
event is emitted, the automation reacts, and the change flows back through the
appropriate review and execution stages.

So the workflow is not all-or-nothing. I can interact with an agent directly
for a small correction while still using the deterministic orchestration around
it to resume the larger process.

Sometimes the overhead of starting the complete pipeline from scratch is simply
not justified. If the change is small enough, it is faster and clearer for me
to stay in the loop, give the agent the context it needs, review what it does,
and then let the existing automation continue from there.

There are also cases where an AI agent escalates a problem because it cannot
safely resolve it within the context and constraints it has been given.

An escalation is not a failure of the system. In many cases it is the correct
behaviour.

I would much rather have an agent say, *"I do not have enough information to
make this decision safely,"* than invent an architectural decision simply to
keep the workflow moving.

The goal is therefore not full autonomy at any cost.

It is **bounded autonomy with explicit escape hatches back to a human**.

## From an Event to an AWX Job

The orchestration around the agents is event-driven.

At a simplified level, the path looks like this:

```text
Issue / repository event
        |
        v
     Webhook
        |
        v
 FastAPI gateway
        |
        v
      Kafka
        |
        v
Event-Driven Ansible
        |
        v
       AWX
        |
        v
   Agent workflow
```

![AFK Engineering Workflow Lifecycle](/assets/afk-lifecycle-diagram.png)

A webhook hits my FastAPI application, which validates and normalises
the event before publishing it into Kafka.

From there, Event-Driven Ansible picks up the relevant event and
payload. The matching rule then hands the work to AWX, which launches
the appropriate job.

**Event-Driven Ansible (EDA)** is the event-processing layer. Instead
of waiting for a person or a scheduled job to start automation, it
listens for events and evaluates them against rules. When an event
matches a rule, EDA decides what automation should happen next. In this
workflow, that means taking an event from Kafka, inspecting the payload,
and triggering the appropriate downstream job.

That is useful in cases where polling or scheduled automation is the
wrong model. A scheduled job asks, *"Is there anything for me to do
yet?"* every few minutes. EDA can instead react when the relevant event
actually occurs.

For this workflow that matters because work does not become ready on a
clock. An issue becomes executable when its state changes, when a
dependency in the DAG is satisfied, or when a review produces a new
verdict. Those are events, not times of day.

That does not mean event-driven automation is always better than a
schedule. Scheduled jobs are still a good fit for periodic work such as
nightly maintenance, reports or regular reconciliation. But where the
trigger is a change in system state, EDA lets the automation respond to
that change directly rather than repeatedly polling for it.

**AWX** is the automation execution platform I use to run those jobs.
It is the upstream open-source project behind the automation controller
in Red Hat Ansible Automation Platform. AWX gives me centrally managed
Job Templates, inventories, credentials, execution environments and job
history, so the agent workflow runs as controlled automation rather than
as an ad-hoc process on my laptop.

Put another way: **EDA decides when and what to run; AWX runs it.**

I could wire the repository event directly to an agent runner, but I
deliberately do not.

The separation gives me boundaries between the system that receives an
event, the system that transports it, the rules that decide what the
event means, and the system that actually performs the work.

That makes the agent another worker in an automation system rather than
the automation system itself.

## The Developer Does a Bounded Job

Once AWX starts the development job, the selected developer agent
receives the bounded task produced by the router.

At this point I want the developer to be boring.

It should not be redesigning the project. It should not be reconsidering
architectural decisions that were already made. It should not be
wandering through unrelated parts of the repository because they happen
to fit in the context window.

Its job is implementation.

That separation is deliberate:

```text
Human / Council     -> Why are we building this?
Planning / ADRs     -> What decisions and constraints already exist?
Task Router         -> What exactly is this task allowed to touch?
Developer           -> Implement the bounded change.
Reviewer            -> Try to prove the implementation is wrong.
```

Each stage gets a different problem to solve.

## The First Review Loop: Diff Review

After the developer produces a change, it does not immediately become a
merge request.

There is an internal diff review first.

This is the one part of the execution pipeline where I allow an
immediate back-and-forth loop. The diff reviewer examines what the
developer changed and can send it directly back to the developer to fix
problems.

The developer fixes the issue. The diff is reviewed again.

That loop can happen as many times as necessary.

The purpose is to catch implementation errors while the work is still
inside the execution pipeline, before promoting it into the external
merge-request review process.

## The Reviewer Is Supposed to Be Adversarial

I intentionally make the reviewer adversarial.

I do not want a reviewer whose default behaviour is to agree with the
developer.

The developer's job is to make the implementation work.

The reviewer's job is to find reasons it might not.

That means challenging assumptions, checking whether the implementation
actually satisfies the task, looking for unintended changes and refusing
to approve work just because the code looks plausible.

This is an important part of the multi-agent design.

Using multiple agents is not particularly useful if they all share the
same goal and spend their time agreeing with one another.

I want tension between the roles.

## The External Merge Request Review Loop

Once the internal execution pipeline is satisfied with the change, a
merge request is created.

That begins a separate review loop.

The external reviewer examines the merge request and produces a verdict.

If the verdict is **Request Changes**, the work goes back to the
developer — together with the reviewer's comments.

The developer does not simply rerun the original task. It reads the
reviewer's feedback, responds to the specific points raised, updates the
implementation where required, and pushes the revised change back to the
merge request.

The reviewer then evaluates both the developer's response and the new
diff. If it is still not satisfied, it can request further changes and
the developer responds again.

So this is a genuine developer-reviewer conversation expressed as an
automated feedback loop:

```text
Developer
    |
    | implementation
    v
Merge Request
    |
    v
Adversarial Reviewer
    |
    | Request Changes + review comments
    v
Developer
    |
    | responds to comments + updates code
    v
Merge Request
    |
    +---------------------> Adversarial Reviewer
                              |
                              +-- Request Changes --> Developer
                              |
                              +-- Approve --> Ready for next stage
```

There is no assumption that review happens once, or even twice.

It continues until the reviewer is prepared to return **Approve**.

That is a subtle but important distinction in how I think about agentic
workflows. I am not asking a sequence of agents to each take one turn. I
am creating bounded feedback loops with explicit exit conditions.

## Walking One Task Through the Pipeline

Putting those pieces together, a normal piece of work on an existing
project looks roughly like this:

1.  I create or refine the issue using the architectural context that
    already exists.
2.  A repository event triggers the AFK workflow.
3.  The webhook reaches the FastAPI gateway.
4.  The gateway validates and normalises the payload and publishes the
    event to Kafka.
5.  Event-Driven Ansible consumes the relevant event.
6.  EDA triggers the appropriate AWX job.
7.  The Task Router interprets the issue and creates a bounded execution
    plan.
8.  The router selects the appropriate developer role and limits the
    working context and file scope.
9.  The developer implements the change.
10. The diff reviewer inspects it and can send it back to the developer
    repeatedly until the immediate implementation problems are resolved.
11. A merge request is created.
12. The adversarial external reviewer reviews the merge request and
    leaves specific feedback.
13. A **Request Changes** verdict sends that feedback back to the
    developer.
14. The developer responds to the review comments, updates the
    implementation, and pushes the revised change.
15. The reviewer evaluates the developer's response and the new diff.
16. Developer and reviewer continue that conversation until the verdict
    is **Approve**.

For a greenfield project, there is one extra phase before all of this: I
go through the Council and the planning/grilling process first so that
the architecture and context exist before I start generating
implementation tasks.

## Why Split the Workflow This Way?

There is a temptation with coding agents to keep adding context and
capability to one increasingly powerful agent.

I have gone in the other direction.

I would rather have several agents with narrower jobs, explicit handoffs
and smaller context windows.

The Council challenges the human.

The Task Router constrains the work.

The developer implements it.

The diff reviewer catches immediate implementation problems.

The external reviewer challenges the merge request until it is prepared
to approve it.

And the event-driven infrastructure moves work between those stages
without requiring me to sit in front of the terminal and shepherd every
transition.

That is what **AFK** means for me.

It does not mean removing the human from engineering. I still invest
heavily in the decisions, context and boundaries at the beginning.

It means that once I have defined the work properly, I do not need to
babysit every execution step.

The autonomy comes from the constraints.

And the continuity comes from the repository.

The agents can be disposable. The context cannot be.

And the agents should not own the parts of the system that ordinary software
can do more reliably.

That is probably the most important point in the whole design: this is not an
attempt to replace an engineering system with AI. It is an engineering system
that uses AI selectively, inside boundaries enforced by deterministic software.

My Kafka, EDA, AWX and FastAPI setup is simply one implementation of that idea.
The framework matters more than the products.

## Autonomy Is Not the Same as Removing Humans

AFK does not mean I am trying to remove myself from software engineering.

It means I want the routine parts of bounded implementation work to continue
without me having to manually shepherd every transition.

Humans still matter at the points where judgement, missing context or risk make
automation the wrong choice.

Sometimes that means I make a small fix directly.

Sometimes an agent escalates a decision back to me.

Sometimes the right outcome of a run is not another commit, but a request for
more context.

That is intentional.

A useful AFK system should know not only how to continue, but also **when to
stop and ask for help**.

## My Stack Is an Implementation, Not a Requirement

My particular implementation uses **Kafka, Event-Driven Ansible, AWX and a
custom FastAPI application** because I already had that infrastructure
available and I know how to operate it.

That does not mean someone implementing this pattern needs the same stack.

You could build the same basic workflow with a much smaller set of tools: a
CI system, a queue, a webhook receiver, a workflow engine, GitHub Actions,
GitLab CI, or even a carefully designed application that handles the state
machine directly.

The technologies are not the important part.

The important parts of the framework are the responsibilities they represent:

```text
Durable project context
        |
        v
Work decomposed into a dependency DAG
        |
        v
Deterministic orchestration
        |
        v
Task routing and context bounding
        |
        v
Specialised implementation agents
        |
        v
Adversarial review loops
        |
        v
Explicit state transitions and exit conditions
```

The thing I want readers to take away is not, *"I need Kafka and AWX to build
an AFK coding workflow."*

It is:

> **Understand which parts of the system need deterministic automation, which
> parts benefit from AI, and put explicit boundaries between them.**

Once those boundaries are clear, the underlying tools can be swapped for
whatever makes sense in your environment.
