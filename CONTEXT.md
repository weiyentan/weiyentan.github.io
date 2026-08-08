# AFK Workflow Context

The AFK (Autonomous From Keyboard) workflow is an event-driven automation system that uses bounded AI agents for implementation work while keeping deterministic orchestration in conventional software.

## Language

**AFK**:
Autonomous From Keyboard — the workflow where bounded AI agents implement, review, and update repository work triggered by events rather than manual agent interaction.
_Avoid_: full autonomy, lights-out coding

**Grilling**:
An adversarial interview process that challenges assumptions, makes boundaries explicit, and produces durable project context before implementation begins.
_Avoid_: planning, brainstorming

**ADR (Architectural Decision Record)**:
A short document recording an important technical decision: the problem, alternatives considered, the decision, and the reasoning.
_Avoid_: design doc, specification

**PRD (Product Requirements Document)**:
A document describing what a product or feature should achieve, capturing problem, goals, scope, requirements, constraints, and acceptance criteria.
_Avoid_: spec, brief

**DAG (Directed Acyclic Graph)**:
The dependency structure of implementation work. Issues are nodes; dependencies are edges. Independent branches progress in parallel; blocked work stays blocked until dependencies are satisfied.
_Avoid_: backlog, queue

**Task Router**:
The component that receives an issue and produces a bounded execution plan: which agent role, which files are relevant, which are out of scope, and what constraints apply.
_Avoid_: dispatcher, selector

**Council**:
An adversarial architecture board used only for greenfield projects. Challenges the human's idea before code exists. Not part of normal feature development.
_Avoid_: review board, design review

**Developer Agent**:
A bounded implementation agent that receives a scoped task from the Task Router and implements the change within explicitly defined boundaries.
_Avoid_: coder, assistant

**Reviewer Agent**:
An adversarial review agent that tries to prove the implementation is wrong. Challenges assumptions, checks task satisfaction, looks for unintended changes.
_Avoid_: reviewer, QA

**Escalation**:
When an agent stops and reports it cannot safely resolve a problem within its context. Expected behavior, not failure.
_Avoid_: error, failure

## Relationships

- A **Grilling** session produces **ADRs**, **context.md**, and a **PRD**
- The **PRD** is decomposed into issues forming a **DAG**
- The **Task Router** bounds each issue for a **Developer Agent**
- The **Developer Agent** produces a change reviewed by a **Reviewer Agent**
- **Escalation** returns unresolved decisions to the human
