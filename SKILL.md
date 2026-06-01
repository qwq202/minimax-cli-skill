---
name: "MiniMax CLI"
description: Teach users and agents how to use MiniMax CLI (`mmx`): installation, authentication, Token Plan quota checks, command discovery, flags, examples, and troubleshooting for text, image, video, speech, music, search, vision, config, and file commands. Use this skill when the user asks how to use MiniMax CLI, wants command examples, needs help choosing the right `mmx` command, or wants to understand MiniMax CLI behavior.
---

# MiniMax CLI

Teach practical MiniMax CLI usage. The goal is to help the user understand which `mmx` command to run, why it works, what each important flag does, and how to verify the result.

Official docs: https://platform.minimaxi.com/docs/token-plan/minimax-cli

## Teaching Workflow

When the user asks how to do something with MiniMax CLI:

1. Identify the task category: setup, auth, quota, text, image, video, speech, music, search, vision, config, or files.
2. Show the shortest working command first.
3. Explain only the flags that matter for the task.
4. Add one practical variant when useful, such as JSON output, saving to a file, async video, or region selection.
5. If the command may spend quota or create a long-running task, say that before running it.
6. Prefer `mmx <resource> <command> --help` when exact flags are uncertain.

## Setup Lesson

Check whether `mmx` is installed:

```bash
mmx --version
```

If missing, install it:

```bash
npm install -g mmx-cli
```

Authenticate with an API key only when the user explicitly provides one. Do not echo or print the key in summaries.

```bash
mmx auth login --api-key <MINIMAX_API_KEY>
```

Verify auth and Token Plan quota:

```bash
mmx auth status
mmx quota
```

`mmx auth login` detects region automatically. If region is wrong or the user requests a specific endpoint, set it explicitly:

```bash
mmx config set --key region --value cn
mmx config set --key region --value global
```

## Good CLI Habits

For teaching, show human-readable commands first. For automation, prefer non-interactive, machine-readable commands:

```bash
mmx <resource> <command> --non-interactive --output json --quiet
```

Explain these common flags when they appear:

```text
--output json        return structured JSON for scripts and agents
--quiet              reduce progress text and make stdout easier to parse
--non-interactive    fail instead of prompting, useful in CI or agents
--dry-run            preview the request without executing
--region cn|global   select the MiniMax API region
--api-key <key>      override saved auth for one command
```

Use `--api-key` only for one-off calls when the user asks for that; otherwise rely on persisted auth.

Configuration precedence is:

```text
CLI flags > environment variables > ~/.mmx/config.json > defaults
```

## Command Lessons

### Text Chat

```bash
mmx text chat --message "user:Write a short launch note." --output json --quiet
mmx text chat --system "You are concise." --message "user:Summarize this." --output json --quiet
```

Teach this as: `text chat` sends messages to a MiniMax text model. Use `--message` for simple prompts, `--system` for behavior, `--messages-file <path>` for multi-turn JSON conversations, and `--model <model>` for explicit model selection.

### Image Generation

```bash
mmx image generate --prompt "A clean product photo of a ceramic mug" --out-dir ./outputs --output json --quiet
```

Teach this as: `image generate` creates images from a prompt. Use `--out-dir` to save files, `--n` for multiple images, `--seed` for repeatability, and either `--aspect-ratio` or explicit `--width` plus `--height` for sizing.

### Video Generation

```bash
mmx video generate --prompt "Ocean waves at sunrise" --async --output json --quiet
mmx video task get --task-id <TASK_ID> --output json --quiet
mmx video download --file-id <FILE_ID> --out ./outputs/video.mp4 --quiet
```

Teach this as: video generation can be long-running. Use `--async` to get a task ID immediately, then query the task and download the result. For a blocking command that waits and downloads, use:

```bash
mmx video generate --prompt "Ocean waves at sunrise" --download ./outputs/video.mp4 --quiet
```

### Speech Synthesis

```bash
mmx speech synthesize --text "Hello from MiniMax." --out ./outputs/speech.mp3 --quiet
mmx speech synthesize --text-file script.txt --voice English_expressive_narrator --out ./outputs/speech.mp3 --quiet
```

Teach this as: `speech synthesize` turns text into audio. Use `--out` for an audio file, `--voice` for voice selection, `--text-file` for longer scripts, and `--subtitles` when subtitle output is needed.

### Music Generation

```bash
mmx music generate --prompt "Warm cinematic piano, hopeful mood" --instrumental --out ./outputs/music.mp3 --quiet
mmx music generate --prompt "Upbeat pop about summer" --lyrics-optimizer --out ./outputs/song.mp3 --quiet
```

Teach this as: `music generate` creates new music; `music cover` transforms reference audio into a new style. For instrumentals, use `--instrumental`. For generated lyrics, use `--lyrics-optimizer`.

### Web Search

```bash
mmx search query --q "MiniMax CLI quota command" --output json --quiet
```

### Vision Understanding

```bash
mmx vision describe --image ./photo.jpg --prompt "Describe the visual style." --output json --quiet
```

### File Storage

Teach this as: file commands manage server-side files for workflows that require file IDs. Use `mmx file upload`, `mmx file list`, and `mmx file delete`. Check command help before use because allowed file types and response fields may vary:

```bash
mmx file upload --help
```

## Quota Lesson

Teach this as: `mmx quota` shows Token Plan usage and remaining limits. Run it before expensive media generation if the user asks about balance, availability, or repeated generation:

```bash
mmx quota --output json
```

## Troubleshooting Lesson

Typical exit codes:

```text
0 success
2 bad flags or missing arguments
3 authentication error
4 quota exceeded
5 timeout
10 content filter triggered
```

If a command fails:

1. Run `<same command> --help` to check required flags.
2. Run `mmx auth status` for auth errors.
3. Run `mmx quota` for quota errors.
4. Check `mmx config show` for region or default model issues.
5. Rerun with `--verbose` only when request details are needed, and avoid exposing API keys or secrets in the response.

## Output Handling For Examples

When teaching with examples that create files, save user-facing generated files into the current project's `outputs/` directory when available. For intermediate prompts, JSON payloads, and scratch files, use `work/`.

When returning results to the user, include the command, what it does, the relevant saved file path if any, quota impact if known, and any task ID needed to resume async work.
