---
name: unlayer-cli
description: Uses the Unlayer CLI (@unlayer/cli) to manage templates, projects, and workspaces from the terminal, CI, or an agent — install, authenticate with a Personal Access Token (UNLAYER_TOKEN, login --token), pull templates to local JSON, diff a local design against its remote, and parse --json output. Use when the task mentions the unlayer command, @unlayer/cli, pulling or diffing templates, or unlayer.config.json.
---

# Unlayer CLI

## Overview

`@unlayer/cli` is a command-line client for Unlayer. List and fetch templates, pull them to local JSON files, diff a local design against the remote copy, and switch workspaces and projects. It is built for scripts and agents: `--json` output, no prompts in CI, and a strict stdout/stderr split.

## Install

Requires Node.js 20 or later.

```bash
npm install -g @unlayer/cli
unlayer --version
```

## Authentication

The CLI authenticates with a Personal Access Token (PAT) that starts with `unlayer_pat_`.

| Method | Command | Use When |
|--------|---------|----------|
| Environment variable | `export UNLAYER_TOKEN=unlayer_pat_...` | CI and agents (recommended) |
| Token flag | `unlayer login --token unlayer_pat_...` | Non-interactive login that stores the token |
| Browser | `unlayer login` | A human at a terminal only |

`UNLAYER_TOKEN` is checked before any stored token. Commands that need auth and find no token exit with code 2.

`login --token` also accepts `--workspace-id <id>` and `--project-id <id>` to pick the context without prompting (needed when the account has several projects), and `--skip-context` to skip that step.

Other auth commands: `unlayer whoami`, `unlayer logout` (non-interactive needs `--force`), `unlayer status` (auth, API reachability, current context).

## Global Flags

| Flag | Effect |
|------|--------|
| `--json` | Machine-readable JSON on stdout |
| `--non-interactive` | Never prompt; fail with exit code 2 if input is needed |
| `-q, --quiet` | Suppress decorative output (header, status messages) |
| `-v, --verbose` | Verbose logging to stderr |

`CI`, `CONTINUOUS_INTEGRATION`, `UNLAYER_NON_INTERACTIVE`, or a non-TTY stdin turn on `--non-interactive` and `--quiet` automatically.

## Output Contract

- **stdout** — machine-readable data only (JSON, file contents). Safe to pipe and parse.
- **stderr** — spinners, status messages, warnings, and errors.
- With `--json`, errors are written to stderr as `{"error": "..."}`.
- Exit codes: `0` success, `1` runtime or API error, `2` usage error (bad flags, missing arguments, missing auth, input needed in non-interactive mode).

## Commands

| Command | Description |
|---------|-------------|
| `unlayer template list` | List templates. Flags: `--project-id <id>`, `--limit <n>`, `-s, --search <query>` |
| `unlayer template search <query>` | Case-insensitive name search. Flags: `--project-id`, `--limit` |
| `unlayer template get <id>` | Get one template. Flags: `--design-only`, `-o, --output <file>` (`-` for stdout), `--project-id` |
| `unlayer pull` | Write templates to local files. Flags: `--id <id>`, `--output-dir <dir>`, `--project-id <id>`, `--design-only` |
| `unlayer diff <file>` | Compare a local design against its remote template. Flags: `--remote <id>`, `--project-id <id>` |
| `unlayer workspace list` / `use [id]` | List workspaces / set the active one |
| `unlayer project list` / `use [id]` / `info [id]` | List projects (`-w, --workspace <id>`) / set the active one / show details |
| `unlayer init [name]` | Scaffold a new project |
| `unlayer config show` / `set <key> <value>` / `reset` / `path` | CLI settings; `set` keys are `api-url` and `token-env-var` |

In non-interactive mode `template get`, `workspace use`, and `project use` require the ID argument.

### Pull and diff

```bash
unlayer pull --json                      # all templates -> ./templates/{name}-{id}.json
unlayer pull --id 42 --design-only       # one template, design JSON only
unlayer diff ./templates/welcome-42.json --json
unlayer diff ./local.json --remote 42    # explicit remote when the file has no id
```

`pull` saves an envelope (`id`, `name`, `displayMode`, `design`, `createdAt`, `updatedAt`) unless `--design-only`. `diff` reads the remote ID from the envelope's `id`, or from a `{name}-{id}.json` filename, so a `--design-only` file needs `--remote`. With `--json`, `pull` prints an array of `{id, name, path}` and `diff` prints `{templateId, templateName, totalChanges, changes, summary}`.

## unlayer.config.json

Optional project file. The CLI looks for it in the current directory and then each parent, like `.gitignore`.

```json
{
  "projectId": 123,
  "defaults": {
    "outputDir": "./templates"
  }
}
```

Project resolution: `UNLAYER_PROJECT_ID`, then the project set with `unlayer project use`, then `projectId` from this file. The `token`, `apiKey`, and `$schema` keys are ignored, so never put credentials here. Other environment variables: `UNLAYER_API_URL`, `UNLAYER_WORKSPACE_ID`.

## Using the CLI as an Agent

1. **Always pass `--json`** and parse **stdout only**. Redirect stderr away (`2>/dev/null`) or read it separately for errors.
2. **Never run `unlayer login` without `--token`.** It opens a browser and waits. If there is no token, ask the user to set `UNLAYER_TOKEN` or give a PAT.
3. Pass explicit IDs (`--project-id`, `--id`, `--remote`) instead of relying on prompts or stored context.
4. Check the exit code. `2` means fix the command or the auth; `1` means a runtime or API failure.
5. Run `unlayer status --json` first when auth or context is uncertain.

```bash
export UNLAYER_TOKEN=unlayer_pat_...
unlayer template list --project-id 123 --json 2>/dev/null
unlayer pull --project-id 123 --output-dir ./templates --json 2>/dev/null
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Running `unlayer login` in CI or an agent | Set `UNLAYER_TOKEN` or use `login --token` |
| Parsing stderr as data | Data is on stdout; stderr is for humans and errors |
| `diff` says it cannot detect the remote ID | Add `--remote <id>`, or `pull` without `--design-only` |
| Prompt-style commands fail with exit code 2 | Pass the ID argument (`template get 42`, `project use 123`) |
| Committing a token in `unlayer.config.json` | The CLI ignores it; use `UNLAYER_TOKEN` |

## Resources

- [Unlayer CLI on GitHub](https://github.com/unlayer/cli)
- [Documentation](https://docs.unlayer.com)
- [Dashboard](https://console.unlayer.com)
