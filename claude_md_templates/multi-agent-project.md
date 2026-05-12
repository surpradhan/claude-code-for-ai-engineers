# CLAUDE.md — Multi-Agent System Project

> Drop this file at the root of a multi-agent project. Edit the bracketed placeholders. The instructions below are what Claude Code reads when working in this project.

## Project shape

This is a multi-agent system that [INSERT ONE-SENTENCE PURPOSE — e.g., "coordinates document research agents to produce structured reports" or "automates customer support triage across product, billing, and account agents"].

**Stack:**
- Framework: [INSERT — e.g., LangGraph, CrewAI, AutoGen, OpenAI Agents SDK, custom]
- Model(s): [INSERT — e.g., Claude 3.5 Sonnet for orchestration, GPT-4o-mini for tool agents]
- Memory layer: [INSERT — e.g., shared-memory-protocol, framework-native, Redis, custom]
- Observability: [INSERT — e.g., agent-event-protocol, LangSmith, Langfuse, Helicone]
- Tools: [INSERT — e.g., MCP servers for filesystem and web, custom Python tools]

**Project layout:**
```
src/
  agents/          # agent definitions — one module per agent
  tools/           # tools agents can call
  memory/          # memory layer adapters
  observability/   # event capture, tracing
  graph/           # agent workflow definitions
  runners/         # entry points (CLI, server)
config/            # agent configs, model settings
traces/            # captured traces for debugging
tests/
  agents/          # per-agent unit tests
  graph/           # workflow integration tests
  fixtures/        # canonical inputs and expected behaviors
```

## Operating rules

### Capture before you debug

- **Every workflow run is fully traced.** Inputs, intermediate states, tool calls, tool responses, and final outputs are captured.
- **When a run fails, the trace is the first artifact to inspect, not the source code.** Debugging without a trace is guessing.
- **Tests use captured traces as fixtures.** A failing workflow becomes a permanent regression test.

If the user reports a multi-agent failure, **ask for the trace first**. Refuse to propose fixes without one. Use the `agent-trace-debug` skill if installed.

### Agent boundaries are explicit

- Each agent is a self-contained module in `src/agents/` with:
  - A clear single responsibility (one sentence)
  - Defined inputs and outputs (typed, schema-validated)
  - A list of tools it has access to
  - A list of memory keys it reads and writes
- **Agents do not share mutable state directly.** All cross-agent state flows through the memory layer with explicit reads and writes.
- **Agents do not call each other directly.** Coordination happens at the graph layer.

### Tool design

- Each tool has a strict input schema and a clear one-line description.
- **Tool descriptions are part of the agent's contract.** Agent behavior depends entirely on which tool descriptions look like the right fit. Write them assuming the agent has never seen the codebase.
- Tools log their inputs, outputs, and errors to the observability layer.
- A tool that fails silently is a bug. Tools either succeed or raise.

### Memory layer

- **Read what you need, no more.** Loading all of memory into every agent context inflates token costs and creates context overflow.
- **Write structured.** Never write raw freeform text to memory if a structured representation is possible.
- **Memory writes are observable events.** If memory changes, it's captured in the trace.

### Graph / workflow design

- **The workflow graph is explicit and committed to source.** No dynamically constructed graphs unless absolutely necessary.
- **Every edge has a condition or a guard.** Edges that fire under "any" conditions hide the actual control flow.
- **Termination is explicit.** Every workflow has a defined terminal state. Loops have step counters with fail-loud termination.

### Coding conventions

- Type hints on every function signature.
- Pydantic models for all agent inputs/outputs and tool I/O.
- One agent per module; no multi-agent files.
- `pytest` for tests; `pytest-asyncio` if agents are async.

## What to do when an agent makes wrong tool choices

The default fix is *not* to add more agent prompting. Sequence:

1. Look at the tool descriptions. Are they distinct? Is the routing instruction clear in the agent's system prompt?
2. Check the tool selection trace. Did the agent see all the relevant tools? Did one tool's description overlap with another's?
3. Add a few-shot example to the agent's prompt showing the correct tool choice for a similar case.
4. Only after the above, consider model upgrades.

## What to do when the workflow loops or doesn't terminate

1. Check the step counter in the trace. How many steps before it became clear the loop wouldn't break?
2. Check the termination condition. Is it too strict? Does the agent have access to the information needed to satisfy it?
3. Check state mutations between iterations. Is the agent's state actually changing, or is it cycling?
4. Fix the termination logic. Do not "fix" loops by increasing the step limit.

## What to do when adding a new agent

1. Define the agent's single responsibility in one sentence.
2. Define its inputs and outputs as Pydantic models.
3. List the tools it needs access to. If a tool doesn't exist, decide: build it, or restructure.
4. Identify the memory keys it reads and writes. Update the memory schema documentation.
5. Add the agent to the graph with explicit edges (no "anyone can talk to anyone").
6. Add at least one integration test using a captured trace.

## Available skills

If the user invokes multi-agent work, prefer these skills in the [Claude Code Skill Pack for AI Engineers](https://[your-gumroad-link]):

- `agent-trace-debug` — debug a failing workflow from a captured trace
- `mcp-server-bootstrap` — build a new MCP server to expose tools to agents
- `eval-report-writer` — write up multi-agent benchmark results

## Common slash commands

- `/agent-debug` — analyze a captured trace and propose fixes
- `/experiment-log` — log the current state of an experiment

## Common pitfalls in this project

[INSERT PROJECT-SPECIFIC PITFALLS — e.g., "The router agent currently has access to all six tool-agents' descriptions in its context. If a new tool-agent is added, audit the router's prompt for ambiguity."]

## Contact / context

- Owner: [INSERT]
- Latest design doc: [INSERT LINK]
- Production trace dashboard: [INSERT LINK]
- Known issues / open questions: [INSERT or LINK]
