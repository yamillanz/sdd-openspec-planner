# SDD OpenSpec Planner

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A **multi-tool planning agent** for spec-driven development (SDD) using the [OpenSpec](https://github.com/Fission-AI/OpenSpec) framework. Works with Claude Code, Codex (OpenAI), Pi, OpenCode, Cursor, GitHub Copilot, and 20+ other agentic coding tools.

## What It Does

The SDD OpenSpec Planner researches your codebase, clarifies requirements, and produces a complete planning package on disk:

```
openspec/changes/<your-change>/
├── proposal.md    # Why, what, and how
├── design.md      # Architecture, patterns, TDD strategy
├── tasks.md       # Phased implementation checklist
└── specs/         # Delta specs with Given/When/Then scenarios
```

It never writes code — it produces a plan for a developer or AI agent to implement.

## File Structure

```
sdd-openspec-planner/
├── sdd-planner.md                         # Canonical instructions · OpenCode agent (installs as `sdd-planner`)
├── sdd-openspec-planner.md                # OpenCode agent definition (same instructions)
├── sdd-openspec-planner-agent.md          # General agent (drop into any tool)
├── .opencode/skills/sdd-openspec-planner/ # OpenCode skill
│   └── SKILL.md
├── .claude/agents/                        # Claude Code sub-agent
│   └── sdd-openspec-planner.md
├── .codex/skills/sdd-openspec-planner/    # Codex (OpenAI) skill
│   └── SKILL.md
└── .pi/skills/sdd-openspec-planner/       # Pi skill
    └── SKILL.md
```

Every adapter ships the **same tool-neutral instructions** — only the
frontmatter differs per tool, so the planning logic can never drift between
installs.

## Quick Install

Clone this repo into your project or copy the relevant file into your tool's directory:

```bash
git clone https://github.com/yamillanz/sdd-openspec-planner.git /tmp/sdd-planner
```

Then choose your tool:

### Claude Code

```bash
mkdir -p .claude/agents
cp /tmp/sdd-planner/.claude/agents/sdd-openspec-planner.md .claude/agents/sdd-openspec-planner.md
```

Invoke: `/sdd-openspec-planner plan a dark mode feature`

### Codex (OpenAI)

```bash
mkdir -p .codex/skills/sdd-openspec-planner
cp /tmp/sdd-planner/.codex/skills/sdd-openspec-planner/SKILL.md .codex/skills/sdd-openspec-planner/SKILL.md
```

Invoke: `$sdd-openspec-planner plan a dark mode feature`

### Pi

```bash
mkdir -p .pi/skills/sdd-openspec-planner
cp /tmp/sdd-planner/.pi/skills/sdd-openspec-planner/SKILL.md .pi/skills/sdd-openspec-planner/SKILL.md
```

Invoke: `/sdd-openspec-planner` via skill picker

### OpenCode

```bash
cp /tmp/sdd-planner/sdd-planner.md ~/.config/opencode/agents/sdd-planner.md
```

Invoke: `@sdd-planner` in a message or the Task tool with
`subagent_type: sdd-planner`. The repo also ships a project skill at
`.opencode/skills/sdd-openspec-planner/` (invoke `/sdd-openspec-planner`).

### Cursor

```bash
cp /tmp/sdd-planner/sdd-openspec-planner-agent.md .cursor/commands/sdd-openspec-planner.md
```

Invoke: `/sdd-openspec-planner`

### GitHub Copilot

```bash
cp /tmp/sdd-planner/sdd-openspec-planner-agent.md .github/prompts/sdd-openspec-planner.prompt.md
```

Invoke: `/sdd-openspec-planner`

### Any Other Tool

Copy `sdd-openspec-planner-agent.md` into your tool's agents, commands, or prompts directory. See the [supported tools table](https://github.com/Fission-AI/OpenSpec/blob/main/docs/supported-tools.md) for exact paths.

## How to Use

1. **Start the agent** using your tool's invocation syntax
2. **Describe what you want to plan** — a feature, refactor, bug fix, or any change
3. **The agent researches your codebase** to understand current behavior
4. **It asks clarifying questions** when requirements are ambiguous
5. **It writes all artifacts to `openspec/changes/<change-name>/`**
6. **Review the plan** on disk, request revisions, or approve
7. **Hand off the plan** to a developer or implementation agent

## How It Works (The OpenSpec Workflow)

```
┌──────────┐     ┌───────────┐     ┌──────────────┐     ┌───────────┐
│ 1.       │────▶│ 2.        │────▶│ 3. OpenSpec   │────▶│ 4.        │
│ Discovery│     │ Alignment │     │ Artifact      │     │ Refinement│
│          │◀────│           │◀────│ Creation      │◀────│           │
└──────────┘     └───────────┘     └──────────────┘     └───────────┘
```

1. **Discovery** — Classify the request, research the codebase, inventory affected features
2. **Alignment** — Clarify ambiguities, surface constraints, validate assumptions
3. **Artifact Creation** — Write `proposal.md`, `design.md`, `tasks.md`, and delta specs to disk
4. **Refinement** — Iterate on user feedback until approval

Every phase ends with a mandatory human approval gate. No phase begins without explicit approval of the previous one.

## Key Design Decisions

- **Never implements code** — sole responsibility is planning
- **Guardrails travel with the plan** — the EXECUTION GUARDRAILS preamble is written verbatim at the top of `tasks.md`, the file the implementer reads first
- **One gate per phase** — gate count must equal phase count, and every gate is the final task of its phase; "just do it all" does not lift the gates
- **Spec-first** — requirements (WHAT) before design (HOW)
- **TDD assessment** — evaluates whether test-driven development is suitable
- **Design pattern assessment** — only introduces patterns with concrete benefit
- **Tool-agnostic** — same planning logic, adapts to each tool's capabilities

## Related

- [OpenSpec Framework](https://github.com/Fission-AI/OpenSpec)
- [OpenSpec Supported Tools](https://github.com/Fission-AI/OpenSpec/blob/main/docs/supported-tools.md)

## License

MIT — see [LICENSE](LICENSE).
