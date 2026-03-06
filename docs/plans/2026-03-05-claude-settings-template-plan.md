# Claude Settings Template Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Create a shareable Claude Code security settings template with allow/deny permission rules and apply it to the user's global config.

**Architecture:** Two files in `claude-config/` repo — a JSON template and a README. Then merge the permission rules into the existing `~/.claude/settings.json`.

**Tech Stack:** JSON, Markdown

---

### Task 1: Create settings-template.json

**Files:**
- Create: `settings-template.json`

**Step 1: Write the template file**

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(ls *)",
      "Bash(find *)",
      "Bash(cat *)",
      "Bash(head *)",
      "Bash(tail *)",
      "Bash(wc *)",
      "Bash(npm *)",
      "Bash(yarn *)",
      "Bash(bun *)",
      "Bash(npx *)",
      "Bash(pip *)",
      "Bash(pytest *)",
      "Bash(cargo *)",
      "Bash(go test *)",
      "Bash(make *)"
    ],
    "deny": [
      "Read(.env*)",
      "Read(*.pem)",
      "Read(*.key)",
      "Read(*.p12)",
      "Read(*.pfx)",
      "Read(*credentials*)",
      "Read(*secret*)",
      "Read(*token*)",
      "Read(*.keychain)",
      "Read(*.keystore)",
      "Read(id_rsa*)",
      "Read(id_ed25519*)",
      "Read(id_ecdsa*)",
      "Read(.npmrc)",
      "Read(.pypirc)",
      "Read(*.tfvars)",
      "Read(~/.aws/*)",
      "Read(~/.ssh/*)",
      "Bash(cat *.env*)",
      "Bash(cat *.pem)",
      "Bash(cat *credentials*)",
      "Bash(cat *secret*)",
      "Bash(cat *token*)",
      "Bash(cat id_rsa*)",
      "Bash(cat ~/.aws/*)",
      "Bash(cat ~/.ssh/*)"
    ]
  }
}
```

**Step 2: Validate JSON**

Run: `python3 -c "import json; json.load(open('settings-template.json'))"`
Expected: No output (valid JSON)

**Step 3: Commit**

```bash
git add settings-template.json
git commit -m "feat: add claude settings template with allow/deny rules"
```

---

### Task 2: Create README.md

**Files:**
- Create: `README.md`

**Step 1: Write the README**

Content should include:
- Title and one-paragraph overview
- Quick start section: copy to `~/.claude/settings.json` or merge into existing
- Allow rules table (category, pattern, what it covers)
- Deny rules table (category, pattern, what it protects)
- Customization section: how to add rules, note about hooks/plugins being separate
- Limitations section: deny rules not exhaustive, defense in depth recommended

**Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README with usage guide and rule explanations"
```

---

### Task 3: Merge permissions into existing global settings

**Files:**
- Modify: `~/.claude/settings.json`

**Step 1: Read current settings**

Read `~/.claude/settings.json` to confirm current state.

**Step 2: Merge allow rules**

Add all allow rules from the template to the existing `permissions.allow` array. Preserve the existing ralph-loop allow entry.

**Step 3: Add deny rules**

Add the full `permissions.deny` array from the template (does not currently exist in settings).

**Step 4: Validate JSON**

Run: `python3 -c "import json; json.load(open('/Users/davezen1/.claude/settings.json'))"`
Expected: No output (valid JSON)

**Step 5: Verify settings load**

Run: `claude --version` (or similar) to confirm Claude doesn't error on the updated config.

**Step 6: Commit (in ~/.claude repo)**

```bash
cd ~/.claude && git add settings.json && git commit -m "feat: add allow/deny permission rules from claude-config template"
```

---

### Task 4: Initialize git repo for claude-config

**Files:**
- Create: `.gitignore`

**Step 1: Initialize repo**

Run: `cd /Users/davezen1/projects/ai/claude-config && git init`

**Step 2: Create .gitignore**

```
.DS_Store
```

**Step 3: Initial commit with all files**

```bash
git add .gitignore settings-template.json README.md docs/
git commit -m "feat: initial claude-config with settings template and design docs"
```
