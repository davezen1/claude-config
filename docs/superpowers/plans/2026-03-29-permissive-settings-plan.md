# Permissive Settings Template Update — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Expand the Claude Code settings template to allow more dev commands (file ops, network, runtimes, system inspection) while adding deny rules to prevent destructive `rm` on system directories.

**Architecture:** Two files to update — `settings-template.json` gets new allow/deny entries, `README.md` gets updated documentation tables. No new files created.

**Tech Stack:** JSON config, Markdown docs

---

### Task 1: Add new allow rules to settings-template.json

**Files:**
- Modify: `settings-template.json:1-120`

- [ ] **Step 1: Add file operation commands**

In `settings-template.json`, add the following entries to the `allow` array after the existing `Bash(docker *)` entry:

```json
      "Bash(rm *)",
      "Bash(touch *)",
      "Bash(tee *)",
      "Bash(ln *)",
      "Bash(file *)",
      "Bash(stat *)",
      "Bash(readlink *)",
      "Bash(realpath *)",
      "Bash(rsync *)",
```

- [ ] **Step 2: Add internet/network commands**

Add after the file operation entries:

```json
      "Bash(ssh *)",
      "Bash(scp *)",
      "Bash(nc *)",
```

- [ ] **Step 3: Add dev runtimes & tools**

Add after the network entries:

```json
      "Bash(node *)",
      "Bash(deno *)",
      "Bash(ruby *)",
      "Bash(gem *)",
      "Bash(bundle *)",
      "Bash(dotnet *)",
      "Bash(swift *)",
      "Bash(swiftc *)",
      "Bash(gh *)",
      "Bash(jq *)",
      "Bash(yq *)",
```

- [ ] **Step 4: Add text/file inspection commands**

Add after the dev tools entries:

```json
      "Bash(less *)",
      "Bash(more *)",
      "Bash(xxd *)",
      "Bash(od *)",
      "Bash(xargs *)",
      "Bash(basename *)",
      "Bash(dirname *)",
```

- [ ] **Step 5: Add system inspection commands**

Add after the text inspection entries:

```json
      "Bash(lsof *)",
      "Bash(df *)",
      "Bash(du *)",
      "Bash(uname *)",
      "Bash(whoami *)",
      "Bash(hostname *)",
      "Bash(date *)",
      "Bash(uptime *)",
```

- [ ] **Step 6: Add macOS utilities**

Add after the system inspection entries:

```json
      "Bash(open *)",
      "Bash(pbcopy *)",
      "Bash(pbpaste *)",
```

- [ ] **Step 7: Add test runner commands**

Add after the macOS utilities:

```json
      "Bash(npx mocha *)",
      "Bash(npx tsx *)"
```

- [ ] **Step 8: Verify JSON is valid**

Run: `python3 -c "import json; json.load(open('settings-template.json'))"`
Expected: No output (valid JSON)

- [ ] **Step 9: Commit**

```bash
git add settings-template.json
git commit -m "feat: add permissive allow rules for file ops, network, runtimes, and system commands"
```

---

### Task 2: Add new deny rules to settings-template.json

**Files:**
- Modify: `settings-template.json`

- [ ] **Step 1: Add rm guardrails for system directories**

In `settings-template.json`, add the following entries to the `deny` array after the existing `Bash(rm -fr ~/*)` entry:

```json
      "Bash(rm -rf /System*)",
      "Bash(rm -rf /Library*)",
      "Bash(rm -rf /bin*)",
      "Bash(rm -rf /sbin*)",
      "Bash(rm -rf /tmp*)",
      "Bash(rm -rf /Users*)",
      "Bash(rm -fr /System*)",
      "Bash(rm -fr /Library*)",
      "Bash(rm -fr /bin*)",
      "Bash(rm -fr /sbin*)",
      "Bash(rm -fr /tmp*)",
      "Bash(rm -fr /Users*)"
```

- [ ] **Step 2: Verify JSON is valid**

Run: `python3 -c "import json; json.load(open('settings-template.json'))"`
Expected: No output (valid JSON)

- [ ] **Step 3: Commit**

```bash
git add settings-template.json
git commit -m "feat: add rm deny rules for system directories"
```

---

### Task 3: Update README.md allow rules table

**Files:**
- Modify: `README.md:17-27`

- [ ] **Step 1: Update the Allow Rules table**

Replace the existing Allow Rules table with:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: update allow rules table with new commands"
```

---

### Task 4: Update README.md deny rules table and limitations

**Files:**
- Modify: `README.md:29-39` and `README.md:55-59`

- [ ] **Step 1: Update the Deny Rules table**

Replace the existing Deny Rules table with:

```markdown
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
```

- [ ] **Step 2: Update the Limitations section**

Replace the existing Limitations section with:

```markdown
## Limitations

- Deny rules are not exhaustive. Commands like `less`, `head`, `grep`, and other tools that can read file contents are not blocked via deny rules for sensitive file patterns.
- `Bash(git *)` allows all git subcommands without prompting, including destructive operations like `git push --force`.
- `Bash(rm *)` is allowed for local project use but guarded by deny rules for system directories (`/`, `/System`, `/Library`, `/bin`, `/sbin`, `/tmp`, `/Users`, `/etc`, `/usr`, `/var`, `/home`, `/Applications`, `~`, `.git`). The deny list may not cover all dangerous paths.
- Use defense in depth: `.gitignore` to keep secrets out of repos, filesystem permissions to restrict access, and secret managers to avoid storing credentials in files.
```

- [ ] **Step 3: Commit**

```bash
git add README.md
git commit -m "docs: update deny rules table and limitations for rm guardrails"
```
