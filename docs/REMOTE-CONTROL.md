# GSD + Claude Code Remote Control

GSD orchestrates long-running phases (`/gsd:plan-phase`, `/gsd:execute-phase`, `/gsd:verify-phase`). A single phase can run 20 minutes to a couple of hours depending on how many plans fan out. Claude Code [Remote Control](https://code.claude.com/docs/en/remote-control) lets you drive the local `claude` process from a browser or mobile app on claude.ai, so a running phase doesn't chain you to your desk.

## Problem

Once `/gsd:execute-phase` kicks off, the orchestrator sits in a loop: spawn wave, wait for subagents, verify, next wave. During that time your job is mostly answering occasional `AskUserQuestion` prompts and skimming status. Doing that from a couch or a coffee shop is fine, but by default the `claude` CLI only accepts input from the terminal that started it.

## What Remote Control changes

Started before the phase begins, Remote Control bridges the local process to a session on <https://claude.ai/code>. You can prompt, answer questions, and read output from any device signed in to the same account. The phase itself still runs on your machine, with your MCP servers, your credentials, your file access. Nothing about the project moves to the cloud.

## Setup

Three ways to start a session. Pick by intent:

```bash
# Server mode: walk away first, drive entirely from the phone/browser.
# Recommended before starting a long GSD phase.
claude remote-control --name "GSD: myproject"

# Interactive flag: local session with the option to switch devices mid-flow.
claude --remote-control

# Already inside `claude`? Upgrade the current session:
/remote-control
```

Either the URL `claude` prints or `https://claude.ai/code` from another device lets you connect. Pressing spacebar in server mode toggles a QR code for phone scanning.

For auto-connect on every future interactive session, `/config` inside `claude` and set "Enable Remote Control for all sessions" to `true`.

## Recommended pattern for `/gsd:execute-phase`

1. At the desk: `cd` into the project, run `claude remote-control --name "GSD: <phase-name>"`.
2. In the session (either the browser tab that opens or the mobile app), run `/gsd:execute-phase` normally.
3. In `/config`, enable **Push when Claude decides** (proactive) and **Push when actions required** (permission prompts) so you're notified when the orchestrator needs you.
4. Walk away. Come back to the phone when the notification lands, answer any prompts, let the phase finish.

If the local `claude` dies mid-phase (laptop reclaimed, container killed, network out for more than ~10 minutes), the remote session ends. The GSD phase state is already on disk (`.planning/`), so `/gsd:resume-work` in a fresh `claude` picks up where you left off.

## When to pick `/gsd:remote-control` vs `/gsd:pause-work`

| You want to... | Use |
|---|---|
| Keep the phase running while you step away from the desk | `/gsd:remote-control` |
| Stop the phase now and pick it up in a new session later (hours, days) | `/gsd:pause-work` |
| Hand the phase to a teammate (they resume in a fresh `claude` on their machine) | `/gsd:pause-work` + commit + they `/gsd:resume-work` |

Remote Control keeps the same process. Pause writes a handoff file so a different process (yours later or someone else's) can pick up.

## Eligibility gotchas

Remote Control has several eligibility conditions. If it fails to start, `claude doctor` reports which check failed. Common issues:

- `ANTHROPIC_API_KEY` or `CLAUDE_CODE_OAUTH_TOKEN` set: unset and `claude auth login`.
- On Team/Enterprise, an Owner must enable the toggle at <https://claude.ai/admin-settings/claude-code>.
- `DISABLE_TELEMETRY`, `DO_NOT_TRACK`, `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC`, or `DISABLE_GROWTHBOOK` set: unset the one that's set.
- `ANTHROPIC_BASE_URL` pointed at a gateway/proxy, or a Bedrock/Vertex/Foundry variable set: unset it.

Full error catalog and fixes: the [Remote Control docs page](https://code.claude.com/docs/en/remote-control) or the agentskills skill `claude-code-remote-control`.

## Limitations

- The remote client shows the `claude` CLI, not any UI (n8n canvas, browser dev tools, etc.). Keep those tabs open separately when you need them.
- If the local `claude` process quits, the remote session ends. Same for a ~10 minute network outage on the machine.
- Ultraplan disconnects Remote Control (both features occupy the claude.ai/code interface). Don't start Ultraplan mid-session.
- Push fires on `claude` turn boundaries, not on GSD-internal events. If you want to be notified when an execute-phase wave completes but before the orchestrator asks for input, phrase your prompt so `claude` emits a status message at the boundary you care about.

## References

- Feature docs: <https://code.claude.com/docs/en/remote-control>
- Detailed setup, all three start modes, complete error catalog: agentskills skill `claude-code-remote-control`
