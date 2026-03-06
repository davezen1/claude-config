# Claude Settings Template Design

## Overview

A shareable Claude Code security settings template that allows common development commands (git, package managers, build/test, filesystem reads) and denies access to sensitive files (.env, keys, credentials, SSH, cloud configs). Delivered as a copy-paste ready `settings-template.json` with an explanatory `README.md`.

## Repository Structure

```
claude-config/
├── README.md              # Rationale, usage, customization guide
├── settings-template.json # Copy-paste ready settings.json
```

The template contains only permission rules (allow/deny). Hooks and plugins are excluded as personal/project-specific.

## Allow Rules

Auto-approved commands (no prompting):

| Category | Pattern | Covers |
|----------|---------|--------|
| Git | `Bash(git *)` | All git subcommands |
| Filesystem (read) | `Bash(ls *)`, `Bash(find *)`, `Bash(cat *)`, `Bash(head *)`, `Bash(tail *)`, `Bash(wc *)` | Read-only file inspection |
| Node.js | `Bash(npm *)`, `Bash(yarn *)`, `Bash(bun *)`, `Bash(npx *)` | Package management and scripts |
| Python | `Bash(pip *)`, `Bash(pytest *)` | Package management and testing |
| Rust | `Bash(cargo *)` | Build, test, package management |
| Go | `Bash(go test *)` | Test runner |
| Make | `Bash(make *)` | Build system |

Intentionally excluded: `rm`, `chmod`, `chown`, `sudo`, `curl | sh` — these always prompt.

## Deny Rules

Blocked from `Read` tool and `Bash(cat ...)`:

| Category | Patterns | Protects |
|----------|----------|----------|
| Environment files | `.env*` | API keys, database URLs, secrets |
| Certificates/keys | `*.pem`, `*.key`, `*.p12`, `*.pfx` | TLS certs, private keys |
| Named sensitive files | `*credentials*`, `*secret*`, `*token*` | Various credential files |
| Keystores | `*.keychain`, `*.keystore` | OS/Java keystores |
| SSH keys | `id_rsa*`, `id_ed25519*`, `id_ecdsa*` | SSH private keys |
| Registry auth | `.npmrc`, `.pypirc` | Package registry tokens |
| Terraform | `*.tfvars` | Infrastructure secrets |
| Cloud/SSH dirs | `~/.aws/*`, `~/.ssh/*` | Cloud credentials, SSH config |

Deny rules cover both `Read(...)` and `Bash(cat ...)` patterns to prevent circumvention.

## Limitations

- Deny rules are not exhaustive — `less`, `head`, `grep` on sensitive files are not blocked
- Defense in depth recommended: `.gitignore`, filesystem permissions, and secret managers
- `Bash(git *)` allows `git push --force` without prompting — acceptable trade-off for simplicity

## Deliverables

1. `settings-template.json` — standalone permissions config
2. `README.md` — quick start, rule tables, customization notes, limitations
3. Merge permissions into existing `~/.claude/settings.json` preserving hooks and plugins
