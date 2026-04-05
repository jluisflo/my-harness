# my-harness

Principles-based framework for orchestrating AI agents across multi-stack projects. Built on open standards ([agents.md](https://agents.md/) + [SKILL.md](https://agentskills.io/)).

## What is this?

A manifesto and toolkit that defines **how AI agents should work together** to deliver cross-project features. Instead of letting agents freestyle, this harness provides structure: who plans, who builds, who reviews — and what artifacts they must produce.

## Structure

```
├── MANIFESTO.md              ← Research, principles, and design decisions
├── agents/
│   ├── planner/AGENTS.md     ← Transforms PRDs into technical specs
│   ├── builder/AGENTS.md     ← Implements tasks against contracts
│   └── reviewer/AGENTS.md    ← Skeptical QA that catches what builders miss
├── skills/
│   ├── git-commit/SKILL.md   ← Atomic commits with conventional messages
│   ├── test-generator/SKILL.md ← Tests aligned to acceptance criteria
│   └── build-check/SKILL.md  ← Verify compilation and tests before commit
└── templates/
    ├── PRODUCT.md             ← Technical bridge over business PRD
    ├── TECH.md                ← Technical decisions + interface contract
    ├── TASK.md                ← Tasks with verifiable acceptance criteria
    ├── changelog.md           ← Builder's work log per round
    └── review.md              ← Reviewer's evaluation per round
```

## The workflow

```
Business PRD → Planner → PRODUCT.md + TECH.md + TASK.md
                              ↓
                    Human validates spec
                              ↓
                Builder implements per task
                              ↓
                Reviewer evaluates against criteria
                              ↓
              Human reviews → Approve / Iterate / Pivot
```

## Key principles

1. **Spec before code** — No code without a contract that defines "done"
2. **Who builds does not evaluate** — Generation and evaluation are separate roles
3. **Coordination through artifacts** — Structured files, not free prose
4. **Context on-demand** — Skills load when needed, not preemptively
5. **Questionable modularity** — Every component must justify its existence

## AI-tool agnostic

Works with Claude, Codex, Gemini, Copilot, Cursor, Zed, and any tool that supports agents.md + SKILL.md standards.

## Read more

See [MANIFESTO.md](MANIFESTO.md) for the full research, rationale, and design decisions behind this framework.
