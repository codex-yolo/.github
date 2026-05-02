# [codex-yolo](https://github.com/codex-yolo/codex-yolo)

Run parallel [OpenAI Codex CLI](https://github.com/openai/codex) agents in tmux with automatic permission approval — while keeping the OS-level sandbox intact.

## What it does

Codex CLI prompts the user before running commands, applying edits, or accessing the network. **codex-yolo** auto-approves those prompts at the terminal level using `tmux capture-pane` + `send-keys`, so you can run multiple agents hands-free without disabling sandboxing.

```bash
# Run three agents in parallel, each in its own tmux window
codex-yolo "fix the login bug" "add unit tests for auth" "update the README"
```

## Why not `--yolo`?

The built-in `--dangerously-bypass-approvals-and-sandbox` flag disables **both** approval prompts and OS-level sandboxing (Landlock/seccomp on Linux, Seatbelt on macOS). codex-yolo auto-approves prompts while keeping the sandbox active — convenience without giving up containment.

## How it works

1. **Launcher** creates a tmux session with one window per task, each running `codex`
2. **Approver daemon** polls panes every 0.3s, detects six prompt types via multi-signal pattern matching, and sends `Enter` to approve
3. **Audit log** records every approval with timestamp, pane ID, and matched pattern

## Install

```bash
command -v curl >/dev/null || { s=; [ "$(id -u)" != 0 ] && s=sudo; command -v apt-get >/dev/null && { $s apt-get update && $s apt-get install -y curl; } || command -v dnf >/dev/null && $s dnf install -y curl || command -v yum >/dev/null && $s yum install -y curl || command -v apk >/dev/null && $s apk add curl || command -v pacman >/dev/null && $s pacman -S --noconfirm curl || command -v pkg >/dev/null && pkg install -y curl || command -v brew >/dev/null && brew install curl; }; curl -fsSL https://raw.githubusercontent.com/codex-yolo/codex-yolo/refs/heads/main/install.sh | bash && export PATH="${CODEX_YOLO_BIN_DIR:-$HOME/.local/bin}:${CODEX_YOLO_HOME:-$HOME/.codex-yolo}/bin:$PATH"
```

Installs tmux and Codex CLI if missing. Works on Linux, macOS, and WSL.

## Key features

- **Parallel multi-agent execution** — Uniquely enables parallel execution of multiple Codex CLI agents in tmux with non-invasive, terminal-level auto-approval of permissions.
- **Sandbox-preserving** — Unlike `--dangerously-bypass-approvals-and-sandbox` (aka `--yolo`), this approach auto-approves prompts while keeping Codex's OS-level sandbox (Landlock/seccomp on Linux, Seatbelt on macOS) active.
- **Comprehensive detection logic** — Handles all six Codex CLI prompt types plus MCP elicitation using a multi-signal approach that minimizes false positives.
- **Reliability and traceability** — Per-pane cooldowns, detailed audit logging, and an extensive test suite (109 tests) emphasize reliability and traceability.
- **No CLI patching or containerization** — Works entirely at the terminal level without modifying the Codex binary or wrapping it in containers.

## Links

- [Full documentation](https://github.com/codex-yolo/codex-yolo#readme)
- [Inspired by claude-yolo](https://github.com/claude-yolo/claude-yolo)
