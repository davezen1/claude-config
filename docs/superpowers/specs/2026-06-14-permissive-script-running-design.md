# Permissive Script-Running Settings Template — Design

**Date:** 2026-06-14
**Status:** Approved (pending spec review)

## Problem

The current `settings-template.json` allowlist matches **named commands**
(`git`, `npm`, `python`, …). A script invoked any other way — `./build.sh`,
`bash deploy.sh`, a project task runner, or an arbitrary inline pipeline — does
not match a named verb and triggers a permission prompt. The user runs scripts
constantly and does not want to approve each one.

## Decision

Replace the strict, enumerated allowlist with a **fully permissive Bash**
configuration that runs any command without prompting, and make the **deny list
the sole guardrail**. Harden that deny list meaningfully, while being honest
that glob-based denies are a speed bump, not a wall.

This is a deliberate trade-off: maximum convenience, thinnest security
guarantee. It changes the project's identity from "a reasonable security
baseline" to "a low-friction template with best-effort guardrails."

### Decisions locked in

- **Replace** `settings-template.json` in place (single file, not a second profile).
- Allow list collapses to `Bash(*)` plus the non-Bash tools.
- Deny list hardened to cover more secret-readers and destructive operations.
- README reframed away from "safe baseline"; add a Limitations section.
- **Approach A** — pure-JSON glob deny list. No PreToolUse hook, no external
  script, no `defaultMode` change. Keeps the repo a portable, dependency-free
  settings template.

## Design

### 1. File structure & architecture

Single file, `settings-template.json`, pure JSON, no external dependencies. The
top-level `permissions` object keeps exactly its two keys: `allow` and `deny`.
No hooks, no `defaultMode`.

**Allow list (complete):**

```json
"allow": [
  "Bash(*)",
  "Edit(*)",
  "Write(*)",
  "WebFetch(*)",
  "WebSearch(*)",
  "Agent(*)",
  "mcp__plugin_playwright_playwright__*"
]
```

The ~90 individual `Bash(...)` entries are removed — `Bash(*)` subsumes them all.

### 2. Deny list — the guardrail

The deny list is now load-bearing: under `Bash(*)`, anything not explicitly
denied runs silently. Deny rules take precedence over allow. Built as two
auditable matrices.

**(a) Secret-file reads** — block a set of *reader verbs* against a set of
*sensitive globs*. Today only `cat` is covered; extend to common readers.

- **Readers:** `cat, head, tail, less, more, nl, xxd, od, strings, grep,
  egrep, fgrep, rg, awk, sed, cut, source, ., tee, cp, scp, rsync, dd`
- **Targets:** `*.env*, *.pem, *.key, *.p12, *.pfx, *credentials*, *secret*,
  *token*, id_rsa*, id_ed25519*, id_ecdsa*, .npmrc, .pypirc, *.tfvars,
  ~/.aws/*, ~/.ssh/*, *.keychain, *.keystore`
- The existing `Read(...)` denies are **kept** — they cover the Read tool; Bash
  needs its own `Bash(...)` denies because `Read(.env*)` does not apply to a
  Bash command.

The reader×target combinations are generated programmatically during
implementation, but the **expanded JSON is committed** as a static file. There
is no runtime generation — the file remains a plain settings template.

To keep the matrix from being unreadably large, the implementation may scope
combinations to the highest-value pairs (every reader × the core secret globs)
rather than a full cartesian product of every reader against every target;
the implementation plan will decide the exact cut and document what was
included. Coverage decisions must be explicit, not silent.

**(b) Destructive operations** — keep all current `rm -rf` / `rm -fr` patterns,
and add:

- `rm -fr` variants for the paths currently only covered under `rm -rf`
- `dd *`
- `mkfs*`
- `:(){ :|:& };:` (fork bomb)
- `* | sh` and `* | bash` (pipe-to-shell)
- `chmod -R 000 *`
- `> /dev/sd*`

### 3. README reframing

- Rewrite the opening framing from "a reasonable baseline of security" to a
  permissive, low-friction template that runs any command without prompting,
  backed by a best-effort deny list.
- Replace the large allow-rules table with the short allow list from Section 1.
- Expand the deny-rules table to reflect the new secret-reader and
  destructive-op coverage.
- Add an explicit **Limitations** section: glob denies are a speed bump, not a
  wall. They will not stop alternate readers (e.g. `python -c
  "open('.env')"`), novel obfuscations, compound/obfuscated commands, or every
  `rm` variant. Do not use this template on untrusted repositories or sensitive
  machines.

### 4. Testing / verification

Deny-matching semantics depend on the installed Claude Code version, so
verification is partly manual:

1. `jq . settings-template.json` parses cleanly (valid JSON).
2. **Should run silently:** `./build.sh`, `bash deploy.sh`, an inline pipeline
   (e.g. `ls | grep foo`).
3. **Should still block:** `head .env`, `rm -rf ~`, `dd if=/dev/zero of=...`.
4. Results confirmed against the local Claude Code install and noted; any
   pattern that does not block as expected is documented as a known gap rather
   than silently left in.

## Out of scope (YAGNI)

- PreToolUse hook enforcement (Approach B) — more robust but turns the repo
  into "settings + tooling." Explicitly declined for this iteration.
- A second strict profile (`settings-template-permissive.json`) — user chose
  in-place replacement.
- `defaultMode` toggles.
- Git-safety denies (`git push --force`, `git reset --hard`) — would
  reintroduce babysitting for normal work.

## Known limitations

Glob/prefix deny rules under `Bash(*)` are inherently porous. They reduce
accidental exposure but provide no guarantee against a determined or novel
command construction. This is an accepted trade-off, documented for users.
