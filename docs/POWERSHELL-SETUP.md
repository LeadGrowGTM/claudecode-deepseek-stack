# PowerShell Setup (Windows)

Full setup for Windows users running Claude Code via PowerShell.

## Prerequisites

- Claude Code installed (`claude --version`)
- DeepSeek account + API key from `platform.deepseek.com`

## 1. Create Config Directory

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\mg-deepseek"
icacls "$env:USERPROFILE\.config\mg-deepseek" /inheritance:r /grant:r "${env:USERNAME}:(OI)(CI)F"
```

## 2. Create Settings Files

**`deepseek-pro-settings.json`** (v4-pro):
```json
{
  "env": {
    "CLAUDE_CODE_USE_VERTEX": "",
    "ANTHROPIC_VERTEX_PROJECT_ID": "",
    "CLOUD_ML_REGION": "",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "",
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "sk-YOUR-KEY",
    "ANTHROPIC_API_KEY": "sk-YOUR-KEY"
  }
}
```

**`deepseek-flash-settings.json`** (v4-flash): same structure, same key.

## 3. Add to PowerShell Profile

Find your profile: `echo $PROFILE`

> **Why `--add-dir "$PWD"`?** `--bare` disables CLAUDE.md auto-discovery. `--add-dir` restores it.
> **Why `[1m]`?** DeepSeek v4 supports 1M token context. Without it Claude Code defaults to 200k and suggests premature autocompact.

```powershell
$script:ClaudeBin = "$env:USERPROFILE\.local\bin\claude.exe"

function claude {
    $rest = @($args | Select-Object -Skip 1)
    switch ($args[0]) {
        'dsp' {
            Write-Host ">> DEEPSEEK v4-PRO (Opus-tier, 1M ctx, ~30x cheaper)" -ForegroundColor Cyan
            & $script:ClaudeBin --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json" --model "claude-opus-4-6[1m]" --dangerously-skip-permissions --add-dir "$PWD" @rest
        }
        'dsf' {
            Write-Host ">> DEEPSEEK v4-FLASH (Haiku-tier, 1M ctx, ~60x cheaper)" -ForegroundColor Green
            & $script:ClaudeBin --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-flash-settings.json" --model "claude-haiku-4-5-20251001[1m]" --dangerously-skip-permissions --add-dir "$PWD" @rest
        }
        default { & $script:ClaudeBin @args }
    }
}

function ds-pro   { claude dsp @args }
function ds-flash { claude dsf @args }
Set-Alias dsp ds-pro
Set-Alias dsf ds-flash

function claude-sonnet { claude --model claude-sonnet-4-6 --dangerously-skip-permissions @args }
function claude-opus   { claude --model claude-opus-4-6 --dangerously-skip-permissions @args }
function cs            { claude --model claude-sonnet-4-6 --dangerously-skip-permissions @args }

function deepseek-pro   { claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json" --model "claude-opus-4-6[1m]" --add-dir "$PWD" @args }
function deepseek-flash { claude --bare --settings "$env:USERPROFILE\.config\mg-deepseek\deepseek-flash-settings.json" --model "claude-haiku-4-5-20251001[1m]" --add-dir "$PWD" @args }

function rotate-deepseek-key {
    param([string]$NewKey)
    $files = @(
        "$env:USERPROFILE\.config\mg-deepseek\deepseek-pro-settings.json",
        "$env:USERPROFILE\.config\mg-deepseek\deepseek-flash-settings.json"
    )
    foreach ($file in $files) {
        $c = Get-Content $file -Raw
        $c = $c -replace 'sk-[a-f0-9]+', $NewKey
        Set-Content $file $c
        Write-Host "Updated: $file"
    }
}

function usage {
    try {
        $monday = (Get-Date).AddDays(-[int](Get-Date).DayOfWeek + 1).ToString("yyyyMMdd")
        $raw = ccusage weekly -j --offline -s $monday 2>&1
        $jsonStr = ($raw | Where-Object { $_ -notmatch 'WARN|INFO|Fetching|Loaded|Resolving|Resolved|Saved|\[ccusage\]' }) -join ""
        $json = $jsonStr | ConvertFrom-Json
        $spend = [math]::Round($json.totals.totalCost, 2)
        $pct = [math]::Round(($spend / 875) * 100, 1)
        $bar = "#" * [int]($pct / 5)
        Write-Host "Week: `$$spend / ~`$875"
        Write-Host "[$bar] $pct`%"
    } catch { Write-Host "ccusage not available" }
}

function ds-help {
    Write-Host ""
    Write-Host "  DEEPSEEK" -ForegroundColor Yellow
    Write-Host "  --------------------------------------------------------" -ForegroundColor DarkGray
    Write-Host "  dsp       DeepSeek v4-Pro   | Opus-tier  | 1M ctx | ~30x cheaper" -ForegroundColor Cyan
    Write-Host "  dsf       DeepSeek v4-Flash | Haiku-tier | 1M ctx | ~60x cheaper" -ForegroundColor Green
    Write-Host ""
    Write-Host "  ANTHROPIC" -ForegroundColor Yellow
    Write-Host "  --------------------------------------------------------" -ForegroundColor DarkGray
    Write-Host "  cs             Sonnet 4.6"
    Write-Host "  claude-opus    Opus 4.6"
    Write-Host "  claude-sonnet  Sonnet 4.6"
    Write-Host ""
    Write-Host "  UTILITIES" -ForegroundColor Yellow
    Write-Host "  --------------------------------------------------------" -ForegroundColor DarkGray
    Write-Host "  usage                        Weekly Anthropic spend (optional)"
    Write-Host "  rotate-deepseek-key [key]    Swap API key in both settings files"
    Write-Host "  ds-help                      Show this menu"
    Write-Host ""
    Write-Host "  ONE-SHOT / SCRIPTING" -ForegroundColor Yellow
    Write-Host "  --------------------------------------------------------" -ForegroundColor DarkGray
    Write-Host "  deepseek-pro  -p [prompt]   Pro non-interactive"
    Write-Host "  deepseek-flash -p [prompt]  Flash non-interactive"
    Write-Host ""
}
```

## 4. Verify

```powershell
. $PROFILE
dsp -p "say: works"
dsf -p "say: works"
ds-help
```

## Rotate Key

```powershell
rotate-deepseek-key sk-YOURNEWKEY
```