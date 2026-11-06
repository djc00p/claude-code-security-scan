# 🛡️ Claude Code Security Scan

[![ClawHub Skill](https://img.shields.io/badge/ClawHub-Skill-blue)](https://clawhub.ai/djc00p/claude-code-security-scan) [![Agent Skill](https://img.shields.io/badge/Agent-Skill-blue)](#) [![AgentShield](https://img.shields.io/badge/AgentShield-Scan-purple.svg)](https://github.com/affanm/everything-claude-code) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Claude Code Security Scan** audits Claude Code configuration for security vulnerabilities, misconfigurations, and injection risks using **AgentShield**.

Your `.claude/` directory is executable configuration. `settings.json` defines which shell commands run automatically, `mcp.json` wires in third-party servers, `hooks/` can interpolate shell variables, and `agents/` can grant broad tool access. A single sloppy line in any of those is a hole. This skill finds them.

---

## 🔍 What It Scans

| Target | What it looks for |
|--------|-------------------|
| `CLAUDE.md` | Hardcoded secrets, auto-run instructions, injection patterns |
| `settings.json` | Overly permissive allow lists, missing deny lists |
| `mcp.json` | Risky MCP servers, hardcoded env secrets |
| `hooks/` | Command injection via interpolation, data exfiltration |
| `agents/` | Unrestricted tool access, missing model specs |

---

## 🔐 Supply Chain & Scope Safety

AgentShield runs as an npm package. **How you invoke it matters.**

* **Pin the version.** Always use an exact pinned version. Never use a floating tag like `ecc-agentshield@latest` — it can execute changed third-party code at any later run.
* **Keep it project-local.** Install into the project with a lockfile rather than globally, so the exact audited version is pinned in the repository and the install is reviewable in version control.
* **Use an isolated work tree.** Run the scan against a version-controlled or throwaway working tree so any file changes are reviewable and reversible.
* **Hold off on `--fix`.** Read the proposed changes before letting the tool apply them. `--fix` modifies your configuration — that authority should be deliberate.
* **Hold off on `init`.** `init` writes a starter configuration. Read what it would create before letting it write to your config directory.
* **Scope the API key.** If you enable deep analysis, use a limited API key rather than your primary credential.

---

## 🛠 Setup & Usage

### Install (project-local, pinned)

```bash
# Pin an exact version into the project. The lockfile records the exact tree.
npm install --save-dev ecc-agentshield@1.2.3
```

Do **not** use a global install (`npm install -g`) and do **not** use an unpinned `npx ecc-agentshield@latest`. Both defeat the pinning that makes this scan reproducible and reviewable.

### Run (pinned, local)

```bash
# Scan the current project's .claude directory using the pinned local install
npx --no-install ecc-agentshield scan --path .claude
```

`npx --no-install` refuses to fetch anything that isn't already installed, so the scan cannot silently pull a different version at run time.

### Common commands

```bash
# Filter by severity
npx --no-install ecc-agentshield scan --path .claude --min-severity medium

# Output formats
npx --no-install ecc-agentshield scan --path .claude --format json
npx --no-install ecc-agentshield scan --path .claude --format markdown

# Deep analysis (requires ANTHROPIC_API_KEY) — use a limited key
npx --no-install ecc-agentshield scan --path .claude --opus --stream

# AUTO-FIX: modifies your configuration. Run only after reviewing a plain
# scan, and only against a version-controlled or throwaway working tree
# so the changes are reviewable and reversible.
npx --no-install ecc-agentshield scan --path .claude --fix

# INITIALIZE: writes a starter configuration. Read what it would create
# before letting it write to your config directory.
npx --no-install ecc-agentshield init
```

---

## 📊 Severity Grades

| Grade | Score | Meaning |
|-------|-------|---------|
| **A** | 90–100 | Secure |
| **B** | 75–89 | Minor issues |
| **C** | 60–74 | Needs attention |
| **D** | 40–59 | Significant risks |
| **F** | 0–39 | Critical |

---

## 🚨 Critical Findings (Fix Immediately)

* Hardcoded API keys in config
* `Bash(*)` unrestricted shell access
* Command injection via `${file}` interpolation
* Shell-running MCP servers

## ⚠️ High Findings (Fix Before Production)

* Auto-run instructions in `CLAUDE.md`
* Missing deny lists
* Unnecessary Bash access in agents

---

## 🎯 When to Activate

* Setting up a new Claude Code project
* After modifying `settings.json`, `CLAUDE.md`, or MCP configs
* Before committing configuration changes
* Onboarding to a repo with existing configs
* Periodic security hygiene checks

---

## 📚 References

* `references/commands-and-findings.md` — full command reference, severity grades, and critical/high finding detail

---

**Adapted from [everything-claude-code](https://github.com/affanm/everything-claude-code) by [@affaan-m](https://github.com/affaan-m) (MIT).**

**Published by [@djc00p](https://github.com/djc00p)**
