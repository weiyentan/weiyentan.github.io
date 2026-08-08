# 0001: Use deterministic software for workflow control; AI agents for interpretation and implementation

We decided to separate the AFK workflow into deterministic orchestration and bounded AI agent work. Kafka, FastAPI, Event-Driven Ansible, and AWX handle event reception, validation, transport, state transitions, and job scheduling. AI agents handle interpretation, implementation, content generation, and review. The goal is to make agents replaceable workers inside a conventional automation system rather than the automation system itself.

This boundary exists because traditional software is predictable and testable for control flow, while AI is useful where work is inherently less deterministic — interpreting requirements, planning, writing code, and reviewing changes. The consequence is that the orchestration layer can be reasoned about, tested, and debugged with standard software engineering practices, while the agents operate within explicitly bounded scope and constraints.

Status: accepted
