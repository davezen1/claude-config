# Claude Code Settings Template

A shareable security settings template for Claude Code that allows common development commands and blocks access to sensitive files. Copy it into your Claude Code configuration to get a reasonable baseline of permission allow and deny rules.

## Quick Start

**Fresh install** — copy the template directly:

```sh
cp settings-template.json ~/.claude/settings.json
```

**Existing config** — merge the `permissions` block from `settings-template.json` into your existing `~/.claude/settings.json`. Keep your other settings intact and add or merge the `allow` and `deny` arrays under the `permissions` key.

## Allow Rules

| Category | Pattern | Covers |
|----------|---------|--------|
| Git | `Bash(git *)` | All git subcommands |
| Filesystem | `Bash(ls *)`, `Bash(find *)`, `Bash(cat *)`, `Bash(head *)`, `Bash(tail *)`, `Bash(wc *)` | Read-only file inspection |
| Node.js | `Bash(npm *)`, `Bash(yarn *)`, `Bash(bun *)`, `Bash(npx *)` | Package management and scripts |
| Python | `Bash(pip *)`, `Bash(pytest *)` | Package management and testing |
| Rust | `Bash(cargo *)` | Build, test, package management |
| Go | `Bash(go test *)` | Test runner |
| Make | `Bash(make *)` | Build system |

## Deny Rules

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

Deny rules cover both `Read(...)` and `Bash(cat ...)` patterns to prevent circumvention through alternative read methods.

## Customization

**Adding allow rules** — add `Bash(command *)` entries to the `allow` array. For example, to allow Docker commands, add `"Bash(docker *)"`.

**Adding deny rules** — add file patterns to the `deny` array for both `Read` and `Bash(cat ...)` variants to cover all access paths. For example, to block vault files:

```json
"Read(*.vault)",
"Bash(cat *.vault)"
```

**Hooks and plugins** — these are personal or project-specific and are not included in the template. Configure them separately per your workflow.

## Limitations

- Deny rules are not exhaustive. Commands like `less`, `head`, `grep`, and other tools that can read file contents are not blocked via deny rules for sensitive file patterns.
- `Bash(git *)` allows all git subcommands without prompting, including destructive operations like `git push --force`.
- Use defense in depth: `.gitignore` to keep secrets out of repos, filesystem permissions to restrict access, and secret managers to avoid storing credentials in files.
