---
name: gsd:remote-control
description: Print instructions for starting a Claude Code Remote Control session so long-running GSD phases can be driven from a browser or phone
allowed-tools:
  - Bash
  - Read
---

<objective>
Guide the user into a Remote Control session for the current GSD project so they can drive long-running phases (`/gsd:execute-phase`, `/gsd:verify-phase`) from a browser or mobile app on claude.ai.

This command does NOT start Remote Control itself. Remote Control must run in the terminal that owns `claude`; a nested tool call can't take that over. It prints the exact command to paste and the eligibility checks worth running.
</objective>

<context>
Remote Control bridges the current `claude` process to claude.ai/code. Requires a claude.ai login (not `ANTHROPIC_API_KEY`), and on Team/Enterprise plans, an Owner must have enabled the toggle at <https://claude.ai/admin-settings/claude-code>.

Full feature docs: `docs/REMOTE-CONTROL.md` in this repo, or <https://code.claude.com/docs/en/remote-control>.
</context>

<process>
1. Read the current working directory basename to use as a suggested session name (`GSD: <basename>`).

2. Print three paths and let the user pick:

   **Server mode** (recommended if you're going to walk away *before* starting the phase). In a new terminal in the project directory:
   ```
   claude remote-control --name "GSD: <basename>"
   ```
   Open the printed URL on the target device, or press spacebar for a QR code.

   **Interactive flag** (local + remote at the same time). Restart `claude` with:
   ```
   claude --remote-control
   ```

   **Slash command** (upgrade the current session, no restart). Just run:
   ```
   /remote-control
   ```

3. Then remind:
   - Log in with `claude auth login` (claude.ai account, not API key) if the current session isn't authenticated that way.
   - The current `claude` process must stay alive. Closing the terminal ends the session. For SSH sessions, wrap in `tmux` or `screen`.
   - In `/config`, enable "Push when Claude decides" and/or "Push when actions required" so the phone notifies when the orchestrator needs input.
   - To auto-enable Remote Control for every future interactive session, `/config` and set "Enable Remote Control for all sessions" to `true`.

4. If the user reports Remote Control failing to start, suggest running `claude doctor` for a per-check breakdown of what failed. Full error catalog is in `docs/REMOTE-CONTROL.md` and the agentskills skill `claude-code-remote-control`.
</process>
