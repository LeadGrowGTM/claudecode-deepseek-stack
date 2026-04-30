# Invocation Snippets

The two canonical snippets for DeepSeek via Claude Code.

> **`[1m]` suffix** — tells Claude Code to use 1M token context window (DeepSeek v4 native capability). Without it, Claude Code defaults to the Anthropic model spec (200k for Opus) and triggers premature autocompact.
>
> **`--add-dir`** — `--bare` disables CLAUDE.md auto-discovery. `--add-dir` restores it so the model gets full workspace context.

---

## Snippet 1: DeepSeek v4-Flash (`dsf`)

Haiku-equivalent. Fast, cheap. Use for search, triage, file ops, quick edits.

**PowerShell (interactive):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-flash-settings.json" --model "claude-haiku-4-5-20251001[1m]" --dangerously-skip-permissions --add-dir (Get-Location).Path
```

**PowerShell (one-shot):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-flash-settings.json" --model "claude-haiku-4-5-20251001[1m]" --dangerously-skip-permissions --add-dir (Get-Location).Path -p "your prompt"
```

**Bash/zsh:**
```bash
claude --bare --settings ~/.config/mg-deepseek/deepseek-flash-settings.json --model "claude-haiku-4-5-20251001[1m]" --dangerously-skip-permissions --add-dir "$(pwd)"
```

---

## Snippet 2: DeepSeek v4-Pro (`dsp`)

Opus-equivalent with reasoning. Use for planning, architecture, complex coding, agents.

**PowerShell (interactive):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json" --model "claude-opus-4-6[1m]" --dangerously-skip-permissions --add-dir (Get-Location).Path
```

**PowerShell (one-shot):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json" --model "claude-opus-4-6[1m]" --dangerously-skip-permissions --add-dir (Get-Location).Path -p "your prompt"
```

**Bash/zsh:**
```bash
claude --bare --settings ~/.config/mg-deepseek/deepseek-pro-settings.json --model "claude-opus-4-6[1m]" --dangerously-skip-permissions --add-dir "$(pwd)"
```

---

## With Profile Aliases (after POWERSHELL-SETUP.md)

```powershell
dsp          # Pro - interactive
dsf          # Flash - interactive
dsp -p "x"  # Pro - one-shot
dsf -p "x"  # Flash - one-shot
```

---

## When to Use Which

| Task | Use |
|------|-----|
| Search, triage, file ops, quick edits | `dsf` |
| Planning, architecture, complex reasoning, coding agents | `dsp` |
| Voice-critical content (LinkedIn, email copy) | `claude-sonnet` (Anthropic) |

---

## Verify

**On DeepSeek:** Header shows `API Usage Billing` (DeepSeek) vs your plan name (Anthropic).

**Context at 1M:** Session context counter shows `/1000000` after launch.

**CLAUDE.md loaded:** System prompt token count substantially more than 556 tokens (bare minimum without workspace context).
