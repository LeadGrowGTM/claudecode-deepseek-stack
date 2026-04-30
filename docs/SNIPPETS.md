# Invocation Snippets

Copy-paste commands to launch Flash or Pro directly — no profile setup required.

---

## DeepSeek v4-Flash (fast, cheap — Haiku equivalent)

**PowerShell (interactive session):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-flash-settings.json" --model claude-haiku-4-5-20251001 --dangerously-skip-permissions
```

**One-shot (non-interactive):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-flash-settings.json" --model claude-haiku-4-5-20251001 --dangerously-skip-permissions -p "your prompt here"
```

**Bash/zsh:**
```bash
claude --bare --settings ~/.config/mg-deepseek/deepseek-flash-settings.json --model claude-haiku-4-5-20251001 --dangerously-skip-permissions
```

---

## DeepSeek v4-Pro (reasoning — Opus equivalent)

**PowerShell (interactive session):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json" --model claude-opus-4-6 --dangerously-skip-permissions
```

**One-shot (non-interactive):**
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json" --model claude-opus-4-6 --dangerously-skip-permissions -p "your prompt here"
```

**Bash/zsh:**
```bash
claude --bare --settings ~/.config/mg-deepseek/deepseek-pro-settings.json --model claude-opus-4-6 --dangerously-skip-permissions
```

---

## With Profile Aliases (after POWERSHELL-SETUP.md)

```powershell
dsp          # Pro session (interactive)
dsf          # Flash session (interactive)
dsp -p "x"  # Pro one-shot
dsf -p "x"  # Flash one-shot
```

---

## Which to Use

| Task | Model |
|------|-------|
| Search, triage, file ops, quick edits | Flash |
| Planning, architecture, complex reasoning, coding agents | Pro |
| Voice-critical content (LinkedIn, email copy) | Anthropic via `claude-sonnet` |

---

## Verify You're on DeepSeek

The Claude Code header shows `API Usage Billing` for DeepSeek sessions and your plan name (`Claude Max`, etc.) for Anthropic sessions.

Debug check:
```powershell
claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json" --model claude-opus-4-6 --debug-file "$env:TEMP\ds-check.log" -p "test"
Select-String "API REQUEST" "$env:TEMP\ds-check.log" | Select-Object -First 1
```
Should show `/anthropic/v1/messages` — not `projects/.../publishers/anthropic` (Vertex) or `bedrock-runtime` (AWS).
