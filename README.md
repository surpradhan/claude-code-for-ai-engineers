# Claude Code for AI Engineers — Open-Source Preview

This is the open-source preview of *Claude Code for AI Engineers*, a methodology-first skill pack for engineers building RAG systems, multi-agent workflows, and MCP servers.

The preview includes two of six skills, one of three project templates, and one of five slash commands. **The full pack is available on Gumroad: [surpradhan.gumroad.com/l/claude-code-for-ai-engineers](https://surpradhan.gumroad.com/l/claude-code-for-ai-engineers).**

---

## What's in this preview (open-source, MIT)

**Two skills** (`skills/`)

- `rag-eval-harness` — set up a controlled RAG evaluation harness with the variables locked. Enforces fixing the embedding model, chunker, vector store, and generator so comparisons mean something.
- `agent-trace-debug` — systematic debugging for multi-agent workflows from a captured trace. Refuses to propose fixes without one. Classifies the seven canonical failure modes.

**One project template** (`claude_md_templates/`)

- `multi-agent-project.md` — multi-agent systems (LangGraph / CrewAI / AutoGen / OpenAI Agents SDK)

**One slash command** (`slash_commands/`)

- `/agent-debug` — analyze a captured agent trace; identify failure mode; propose fix

## What's in the full pack (Gumroad)

Everything in this preview, plus:

**Four more skills**

- `benchmark-scaffold` — scaffold a reproducible benchmark project (configs, runners, results)
- `mcp-server-bootstrap` — scaffold a new MCP server in Python (FastMCP) or TypeScript
- `eval-report-writer` — generate a publication-quality evaluation report with mandatory methodology and "where this is honest" sections
- `paper-reproduce` — set up a paper reproduction scaffold with validation gates

**Two more project templates**

- `rag-project.md` — RAG projects (retrieval, eval, deployment)
- `mcp-server-project.md` — MCP server projects (tool schemas, transport, testing)

**Four more slash commands**

- `/eval-run` — run the configured evaluation with current changes
- `/benchmark-new` — scaffold a new architecture inside an existing benchmark project
- `/mcp-scaffold` — add a new tool to an existing MCP server
- `/experiment-log` — capture the current project state into the experiment log

[Get the full pack →](https://surpradhan.gumroad.com/l/claude-code-for-ai-engineers)

---

## How to use this preview

```bash
cp -r skills/* ~/.claude/skills/
cp -r slash_commands/* ~/.claude/commands/
```

Then open Claude Code in any project and try one of:

- "Compare two RAG retrieval architectures on HotpotQA" — `rag-eval-harness` activates and refuses if your comparison varies more than one thing at a time.
- "My LangGraph agent keeps calling the wrong tool, fix the prompt" — `agent-trace-debug` refuses to propose a fix without a captured trace and walks you through capturing one.

If those gates feel right, the full pack applies the same discipline to benchmarking, MCP server work, paper reproduction, and evaluation reporting.

---

## Design principles

1. **Fix what you can; vary one thing at a time.**
2. **Capture before you debug.**
3. **Reproducibility before novelty.**
4. **Honesty in reporting.**

Each skill enforces at least one of these as a hard gate.

## Support

If a skill misfires or you have questions, open an issue or DM me on LinkedIn.

---

*Surabhi Pradhan publishes reproducible AI benchmarks — [rag-benchmark](https://github.com/surpradhan/rag-benchmark), [agent-workflow-comparison](https://github.com/surpradhan/agent-workflow-comparison), [rec-bench](https://github.com/surpradhan/rec-bench), [forecasting-showdown](https://github.com/surpradhan/forecasting-showdown) — and maintains the open-source agent observability protocol [agent-event-protocol](https://github.com/surpradhan/agent-event-protocol). She writes on [Medium](https://medium.com/@surabhi7pradhan) about LLM internals and developer infrastructure.*
