# CodingSkills

Personal, reusable skills for AI coding assistants (Claude Code / Codex).
Each folder is a self-contained skill: a `SKILL.md` plus, where useful, a
`references/` deep-dive rule sheet and an `agents/openai.yaml` for Codex.

## Skills

| Skill | What it does |
|---|---|
| [`python-code-format`](python-code-format/) | Python 3.12 style decisions — formatting, naming, docstrings, logging, resource handling. Includes a detailed `references/` rule sheet. |
| [`cpp-code-format`](cpp-code-format/) | C++/C (C++20) style — layout, naming, headers/includes, comments, error handling, logging messages. Includes a detailed `references/` rule sheet. |
| [`prompt-engineering`](prompt-engineering/) | Writing and optimizing prompts, commands, hooks, and sub-agent instructions for LLM interactions. |
| [`create-hook`](create-hook/) | Author a hook (`.json`) to enforce policy or automate agent lifecycle events. |
| [`incremental-change-cycle`](incremental-change-cycle/) | Run an incremental change cycle — checklist continuity and repeated review checkpoints — for long-running, high-token work. |

## Use

Drop a skill folder into your assistant's skills directory and invoke it by name:

- Claude Code: `~/.claude/skills/<skill>/`
- Codex: `~/.codex/skills/<skill>/`
