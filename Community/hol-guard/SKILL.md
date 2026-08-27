---
name: hol-guard
description: Install and operate HOL Guard to protect supported local AI harnesses before tools run, review approvals and receipts, and scan agent plugins, skills, MCP servers, and marketplace packages with plugin-scanner.
compatibility: Created for Zo Computer; HOL Guard protects supported local harnesses and does not claim native Zo tool interception.
metadata:
  author: Hashgraph Online
  category: Community
---

# HOL Guard

Use HOL Guard when a user wants a local AI harness protected before tool execution, needs to review Guard approvals or receipts, or wants to scan an agent package before use or release.

## Safety rules

- Never claim a harness is protected until HOL Guard reports healthy status for that detected harness.
- Never bypass a Guard approval or retry a blocked mutation outside the protected launch path.
- Treat deny, review, and Guard errors as stop conditions. Do not fall back to an unprotected agent process.
- Preserve the harness's own authentication, permissions, confirmations, sandboxing, and provider-side controls.
- Do not claim HOL Guard intercepts Zo itself. Use it for harnesses returned by `hol-guard detect --json` and for package scanning.

## Install

Probe the actual CLIs rather than relying on shell-specific executable lookup:

```bash
hol-guard --version
plugin-scanner --version
```

If HOL Guard is unavailable and the user asked to set it up, prefer an isolated install:

```bash
pipx install hol-guard
```

If `plugin-scanner` is unavailable and package scanning is requested, install it separately:

```bash
pipx install plugin-scanner
```

Do not assume the `hol-guard` package provides the `plugin-scanner` command.

## Protect a detected local harness

Start by asking HOL Guard for the authoritative runtime identifier:

```bash
hol-guard status
hol-guard detect --json
```

Choose the exact supported harness identifier returned by `detect`. Then use the Guard-owned setup and verification path:

```bash
hol-guard bootstrap
hol-guard install <harness>
hol-guard run <harness> --dry-run
hol-guard doctor <harness> --json
hol-guard run <harness>
hol-guard status
```

The dry run and `doctor` must succeed before claiming protection. If detection finds no supported harness, or bootstrap/install/dry-run/doctor fails, stop and report the failure instead of launching the agent without Guard.

## Review blocked or approval-gated work

```bash
hol-guard approvals
hol-guard approvals open
hol-guard receipts
hol-guard diff <harness>
```

For terminal-only resolution, use the request ID shown by Guard:

```bash
hol-guard approvals approve <request-id>
hol-guard approvals deny <request-id>
```

Only approve after reading the risk reason and understanding the requested scope.

## Audit evidence

```bash
hol-guard receipts
hol-guard inventory
hol-guard abom --format json
hol-guard events
hol-guard explain <artifact-id>
```

Cloud connection and sync are optional and should only be used when the user requests them:

```bash
hol-guard connect
hol-guard connect status
hol-guard sync
```

## Scan an agent package

Use scanner mode for skills, plugins, MCP server packages, marketplace roots, and mixed agent workspaces:

```bash
plugin-scanner lint <path>
plugin-scanner verify <path>
```

For machine-readable verification:

```bash
plugin-scanner verify <path> --json
```

Treat a scanner failure as real until the finding is inspected and resolved.

## Report results

When HOL Guard is used, report the command that ran, what Guard found, what remains blocked or risky, the evidence produced, and the exact next command if user action is required. Never claim protection, approval, or release readiness without Guard output proving it.
