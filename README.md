# Practical TDD Skill for OpenCode

A production-grade TDD workflow skill for [OpenCode](https://github.com/anomalyco/opencode), inspired by [Superpowers](https://github.com/opencode-ai/superpowers).

## What it does

Full-cycle TDD workflow from brainstorming to commit:

- **Brainstorm** — clarify vague requirements before writing code
- **Test-first** — write tests, get user approval, verify RED
- **Minimal implementation** — smallest code to make tests pass
- **Code review with `ocr`** — integrates [Alibaba Open Code Review](https://github.com/alibaba/open-code-review) CLI for AI-powered code review
- **Acceptance checklist** — structured verification with "Why/How" pattern
- **Auto commit** — checkpoint after every GREEN phase

## Installation

```bash
# Copy to OpenCode skills directory
mkdir -p ~/.config/opencode/skills/practical-tdd
cp SKILL.md ~/.config/opencode/skills/practical-tdd/SKILL.md
```

## Usage

Load the skill in OpenCode:

```
/skill practical-tdd
```

Or configure OpenCode to auto-load it in your `opencode.jsonc`.

## Why this over the built-in `test-driven-development` skill?

- **Acceptance checklist with "Why/How"** — each item explains what we're testing and why this method proves it
- **Bug fix loop** — user reports bug → fix → new acceptance round
- **Server restart rule** — auto-kill old process before asking user to test
- **New feature / optimization loops** — handle scope creep cleanly
- **Git init + .gitignore** — auto-init on new projects

## License

MIT
