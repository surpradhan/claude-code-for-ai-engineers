---
description: Analyze a captured multi-agent trace, identify the failure mode, and propose a minimum fix. Refuses to operate without a trace.
---

# /agent-debug

Debug a failing multi-agent workflow from a captured trace. Refuse to proceed without one.

## Procedure

### Stage 0: Confirm we have a trace

Before anything else, ask:
> "I need a captured trace of the failing run. Do you have one available — a LangSmith / Langfuse / Phoenix link, a JSON export, or a log file?"

If the user doesn't have a trace:
- Stop. Provide framework-specific instructions for capturing one (LangGraph stream, CrewAI verbose log, AutoGen message logging, OpenAI Agents SDK tracing, or structured logging for custom setups).
- Ask the user to capture a failing run and a successful run (if the failure is non-deterministic, several of each).
- Don't proceed until traces exist.

**Debugging without a trace is guessing.** Do not propose fixes based on the user's natural-language description of the failure alone.

### Stage 1: Reproduce the failure

With trace in hand:
- Identify the exact input that produced the failure
- Re-run with that input and confirm the failure repeats
- If non-deterministic, run 5–10 times and capture the failure rate

If you can't reproduce, the failure is one of: temperature-driven, external-service-flapping, or a race condition. Each needs different handling. Capture more data before proposing a fix.

### Stage 2: Locate the failure point

Walk the trace step-by-step. For each step:
- Was the input to this step correct?
- Was the output from this step correct given its input?
- Did this step do what its role says it should?

The failure point is the *first* step where the answer to (b) or (c) is "no." Everything downstream is contaminated by upstream errors. Don't investigate downstream symptoms until the upstream is fixed.

### Stage 3: Classify the failure mode

Identify which of these patterns matches:

1. **Tool selection error** — wrong tool picked
2. **Tool argument error** — right tool, wrong arguments
3. **Context overflow** — token budget exhausted by irrelevant content
4. **Loop / non-termination** — repetition or oscillation
5. **State / memory error** — forgot or misremembered earlier information
6. **Multi-agent coordination error** — wrong handoff or duplicated work
7. **Underlying model error** — reasoning/factual mistake the framework can't fix

Report the classification with evidence from the trace. "This is a context overflow because the trace shows 47 intermediate tool results in the context at the point of failure, totaling 8,500 tokens of irrelevant retrieval results crowding out the user's original query."

### Stage 4: Propose the minimum fix

Propose **one** fix scoped to the classified failure mode. Match the fix to the mode:

- **Tool selection / argument**: improve tool descriptions; add few-shot examples to the agent prompt; not a stronger model
- **Context overflow**: add summarization or pruning; not a bigger context window
- **Loop / non-termination**: add explicit step counter with fail-loud termination; not a more permissive condition
- **State / memory**: fix the memory layer; not the agent prompt
- **Coordination**: make the inter-agent contract explicit; not more verbose
- **Model error**: verify it's the model by testing on a stronger model; then either upgrade or restructure

Resist proposing multiple fixes. Multiple fixes confuse cause/effect.

### Stage 5: Verify with a regression check

After implementing the fix:
- Re-run the originally failing input. Confirm it now succeeds.
- Re-run 3–5 inputs that previously succeeded. Confirm they still succeed.
- If non-deterministic, run the failing input 10 times. Require >95% success rate.

A fix that resolves one failure while breaking another is not a fix.

## Output format

After analysis, report:

```
Trace: <link or filename>
Reproducible: <yes/no/intermittent (X/N failures)>

Failure point:
  Step <N>: <step name/role>
  Input was: <correct/incorrect>
  Output was: <correct/incorrect>
  
Failure mode: <classified mode>
Evidence: <trace details that support the classification>

Proposed fix:
  <one specific change>
  
Why this fix:
  <how it addresses the failure mode>

Regression check plan:
  - Originally failing input: <expected behavior>
  - Previously-passing inputs: <list>
  - Non-determinism: <yes/no, retry count if yes>
```

## Don'ts

- Don't propose fixes without a trace. Guessing isn't debugging.
- Don't propose multiple fixes simultaneously. One change, one verification.
- Don't increase step limits to "fix" loops. Fix the loop logic.
- Don't increase context windows to "fix" context overflow. Fix the pruning.
- Don't switch frameworks because debugging feels hard. Most multi-agent debugging is hard in every framework.
- Don't mark the issue resolved without the regression check.
