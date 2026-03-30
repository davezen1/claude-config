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
| Filesystem (read) | `Bash(ls *)`, `Bash(find *)`, `Bash(cat *)`, `Bash(head *)`, `Bash(tail *)`, `Bash(wc *)` | Read-only file inspection |
| Filesystem (write) | `Bash(rm *)`, `Bash(touch *)`, `Bash(tee *)`, `Bash(ln *)`, `Bash(cp *)`, `Bash(mv *)`, `Bash(mkdir *)`, `Bash(rsync *)` | File creation, deletion, copying |
| Filesystem (inspect) | `Bash(file *)`, `Bash(stat *)`, `Bash(readlink *)`, `Bash(realpath *)` | File metadata inspection |
| Text processing | `Bash(grep *)`, `Bash(rg *)`, `Bash(sed *)`, `Bash(awk *)`, `Bash(sort *)`, `Bash(uniq *)`, `Bash(cut *)`, `Bash(tr *)`, `Bash(diff *)`, `Bash(jq *)`, `Bash(yq *)`, `Bash(xargs *)`, `Bash(basename *)`, `Bash(dirname *)` | Search, transform, filter |
| Text viewing | `Bash(less *)`, `Bash(more *)`, `Bash(xxd *)`, `Bash(od *)` | Paging and hex/octal dumps |
| Node.js | `Bash(npm *)`, `Bash(yarn *)`, `Bash(bun *)`, `Bash(npx *)`, `Bash(node *)`, `Bash(deno *)` | JS/TS runtimes and package management |
| Python | `Bash(python *)`, `Bash(python3 *)`, `Bash(pip *)`, `Bash(pip3 *)`, `Bash(uv *)`, `Bash(pytest *)`, `Bash(ruff *)`, `Bash(mypy *)`, `Bash(black *)`, `Bash(poetry *)` | Runtime, package management, linting |
| Rust | `Bash(cargo *)` | Build, test, package management |
| Go | `Bash(go *)` | Build, test, all subcommands |
| Java | `Bash(java *)`, `Bash(javac *)`, `Bash(mvn *)`, `Bash(gradle *)`, `Bash(./gradlew *)` | Runtime, compiler, build systems |
| Ruby | `Bash(ruby *)`, `Bash(gem *)`, `Bash(bundle *)` | Runtime, package management |
| .NET | `Bash(dotnet *)` | Build, test, run |
| Swift | `Bash(swift *)`, `Bash(swiftc *)` | Runtime and compiler |
| Make | `Bash(make *)` | Build system |
| Docker | `Bash(docker *)` | Container management |
| Network | `Bash(curl *)`, `Bash(wget *)`, `Bash(ssh *)`, `Bash(scp *)`, `Bash(nc *)` | HTTP requests, remote access |
| GitHub CLI | `Bash(gh *)` | Issues, PRs, releases |
| System inspection | `Bash(lsof *)`, `Bash(df *)`, `Bash(du *)`, `Bash(uname *)`, `Bash(whoami *)`, `Bash(hostname *)`, `Bash(date *)`, `Bash(uptime *)`, `Bash(ps *)`, `Bash(which *)`, `Bash(env *)` | Process and system info |
| System management | `Bash(kill *)`, `Bash(chmod *)`, `Bash(tar *)`, `Bash(zip *)`, `Bash(unzip *)`, `Bash(echo *)`, `Bash(printf *)` | Process control, permissions, archives |
| macOS utilities | `Bash(open *)`, `Bash(pbcopy *)`, `Bash(pbpaste *)` | Open files/URLs, clipboard |
| Test runners | `Bash(npx playwright *)`, `Bash(npx cypress *)`, `Bash(npx jest *)`, `Bash(npx vitest *)`, `Bash(npx tsc *)`, `Bash(npx eslint *)`, `Bash(npx prettier *)`, `Bash(npx mocha *)`, `Bash(npx tsx *)` | Framework-specific test/lint runners |

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
| Destructive rm (root) | `rm -rf /`, `rm -rf /*`, `rm -fr /`, `rm -fr /*` | Root filesystem |
| Destructive rm (home) | `rm -rf ~`, `rm -rf ~/*`, `rm -fr ~`, `rm -fr ~/*` | Home directory |
| Destructive rm (cwd) | `rm -rf .`, `rm -rf ./*`, `rm -rf ..` | Current working directory |
| Destructive rm (git) | `rm -rf .git`, `rm -rf .git/*` | Git repository |
| Destructive rm (system) | `rm -rf /etc*`, `rm -rf /usr*`, `rm -rf /var*`, `rm -rf /home*`, `rm -rf /Applications*`, `rm -rf /System*`, `rm -rf /Library*`, `rm -rf /bin*`, `rm -rf /sbin*`, `rm -rf /tmp*`, `rm -rf /Users*` (and `-fr` variants) | System directories |

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
- `Bash(rm *)` is allowed for local project use but guarded by deny rules for system directories (`/`, `/System`, `/Library`, `/bin`, `/sbin`, `/tmp`, `/Users`, `/etc`, `/usr`, `/var`, `/home`, `/Applications`, `~`, `.git`). The deny list may not cover all dangerous paths.
- Use defense in depth: `.gitignore` to keep secrets out of repos, filesystem permissions to restrict access, and secret managers to avoid storing credentials in files.
