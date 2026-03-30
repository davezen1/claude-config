# Permissive Settings Template Update

**Date:** 2026-03-29
**Status:** Draft

## Goal

Make the settings template more permissive for local development — allow reading, writing, deleting files, accessing the internet, and running common dev commands — while keeping deny rules that prevent destructive system-level operations.

## Approach

Expand the allow list with specific commands (not a wildcard `Bash(*)`). Use deny rules as guardrails for dangerous `rm` operations targeting system directories.

## Changes

### New Allow Rules

**File operations:**
- `rm *`, `touch *`, `tee *`, `ln *`, `file *`, `stat *`, `readlink *`, `realpath *`, `rsync *`

**Internet/network:**
- `ssh *`, `scp *`, `nc *`

**Dev runtimes & tools:**
- `node *`, `deno *`, `ruby *`, `gem *`, `bundle *`, `dotnet *`, `swift *`, `swiftc *`
- `gh *` (GitHub CLI)
- `jq *`, `yq *`

**Text/file inspection:**
- `less *`, `more *`, `xxd *`, `od *`
- `xargs *`, `basename *`, `dirname *`

**System inspection:**
- `lsof *`, `df *`, `du *`, `uname *`, `whoami *`, `hostname *`, `date *`, `uptime *`

**macOS utilities:**
- `open *`, `pbcopy *`, `pbpaste *`

**Test runners:**
- `npx mocha *`, `npx tsx *`

### New Deny Rules

Additional `rm` guardrails for system directories (both `-rf` and `-fr` variants):

- `rm -rf /System*`, `rm -rf /Library*`, `rm -rf /bin*`, `rm -rf /sbin*`, `rm -rf /tmp*`
- `rm -fr /System*`, `rm -fr /Library*`, `rm -fr /bin*`, `rm -fr /sbin*`, `rm -fr /tmp*`
- `rm -rf /Users*`, `rm -fr /Users*`

Existing deny rules for sensitive files (`.env`, `.pem`, credentials, SSH keys, cloud configs) remain unchanged.

### README Updates

- Add new rows to the Allow Rules table for each new category
- Add new rows to the Deny Rules table for the additional `rm` guardrails
- Update the Limitations section to note that `rm` is now allowed but guarded by deny rules for system paths

## Files Modified

- `settings-template.json` — add new allow and deny entries
- `README.md` — update tables and limitations section
