---
name: obsidian
description: Work with Obsidian vaults (plain Markdown notes) and automate via obsidian-cli.
homepage: https://help.obsidian.md
metadata:
  {
    "openclaw":
      {
        "emoji": "💎",
        "requires": { "bins": ["obsidian-cli"] },
        "install":
          [
            {
              "id": "brew",
              "kind": "brew",
              "formula": "yakitrak/yakitrak/obsidian-cli",
              "bins": ["obsidian-cli"],
              "label": "Install obsidian-cli (brew)",
            },
          ],
      },
  }
---

# Obsidian

Obsidian vault = a normal folder on disk.

Vault structure (typical)

- Notes: `*.md` (plain text Markdown; edit with any editor)
- Config: `.obsidian/` (workspace + plugin settings; usually don’t touch from scripts)
- Canvases: `*.canvas` (JSON)
- Attachments: whatever folder you chose in Obsidian settings (images/PDFs/etc.)

## Find the active vault(s)

Obsidian desktop tracks vaults here (source of truth):

- `~/Library/Application Support/obsidian/obsidian.json`

`obsidian-cli` resolves vaults from that file; vault name is typically the **folder name** (path suffix).

Fast “what vault is active / where are the notes?”

- If you’ve already set a default: `obsidian-cli print-default --path-only`
- Otherwise, read `~/Library/Application Support/obsidian/obsidian.json` and use the vault entry with `"open": true`.

Notes

- Multiple vaults common (iCloud vs `~/Documents`, work/personal, etc.). Don’t guess; read config.
- Avoid writing hardcoded vault paths into scripts; prefer reading the config or using `print-default`.

## obsidian-cli quick start

Pick a default vault (once):

- `obsidian-cli set-default "<vault-folder-name>"`
- `obsidian-cli print-default` / `obsidian-cli print-default --path-only`

Search

- `obsidian-cli search "query"` (note names)
- `obsidian-cli search-content "query"` (inside notes; shows snippets + lines)

Create

- `obsidian-cli create "Folder/New note" --content "..." --open`
- Requires Obsidian URI handler (`obsidian://…`) working (Obsidian installed).
- Avoid creating notes under “hidden” dot-folders (e.g. `.something/...`) via URI; Obsidian may refuse.

Move/rename (safe refactor)

- `obsidian-cli move "old/path/note" "new/path/note"`
- Updates `[[wikilinks]]` and common Markdown links across the vault (this is the main win vs `mv`).

Delete

- `obsidian-cli delete "path/note"`

Prefer direct edits when appropriate: open the `.md` file and change it; Obsidian will pick it up.

---

## Daniel Journal Procedures

When working with the **daniel-journal** vault (`~/.openclaw/workspace/obsidian/daniel-journal/`), follow the domain-specific procedures below. These complement the general Obsidian operations above.

⚠️ **Source of truth**: vault-internal rules in `99-agent/` take precedence. If any conflict exists between this skill and `99-agent/AGENTS.md`, `AGENTS.md` wins.

### Quick Reference

| Procedure | Trigger | Target |
|-----------|---------|--------|
| [voice-journal](procedures/voice-journal.md) | Daniel dictates a journal entry | `Journal/YYYY-MM-DD.md` |
| [article-archive](procedures/article-archive.md) | Daniel sends a link/article to save | `01-raw-articles/<source>/<slug>.md` |

### Shared Iron Rules (both procedures)

1. `git pull --ff-only` before any write — abort on non-ff
2. `git add <exact-files>` only — **never `git add .`**
3. Commit format: `assistant(<area>): <summary>`
4. Push failure → stop, save patch to `04-logs/conflicts/`, notify Daniel
5. Log every action to `04-logs/assistant-runs/YYYY-MM-DD.md`
6. File names: **English kebab-case only**, no Chinese in filenames
7. Ghostwriting ≠ rewriting — never alter existing content without explicit instruction

### Domain-Specific Documents

| File | Purpose |
|------|---------|
| `99-agent/AGENTS.md` | Permissions matrix, red lines, authorship rules (highest authority) |
| `99-agent/WIKI_WORKFLOW_SOP.md` | Daily execution manual (pre-flight, commit conventions, templates) |
| `99-agent/LEGACY_SPLIT_PLAYBOOK.md` | Legacy snapshot split procedure |

→ Read the full procedures for detailed step-by-step execution.
