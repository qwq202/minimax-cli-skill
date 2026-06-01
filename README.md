# MiniMax CLI Skill

This repository contains a Codex/Agent skill named **minimax-cli**, displayed as **MiniMax CLI**.

Its purpose is to teach users and agents how to use the MiniMax `mmx` command-line tool: installation, authentication, quota checks, command selection, common flags, examples, and troubleshooting.

The skill is intentionally instructional. It helps an agent explain the right command before running it, and it highlights quota usage, long-running tasks, and secret-handling practices.

## Files

- `skills/minimax-cli/SKILL.md` - the skill instructions
- `skills/minimax-cli/agents/openai.yaml` - UI metadata for skill lists and chips

## Install

```bash
npx skills add qwq202/minimax-cli-skill
```

## Source Documentation

- MiniMax CLI docs: https://platform.minimaxi.com/docs/token-plan/minimax-cli
