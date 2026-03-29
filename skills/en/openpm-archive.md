---
name: openpm-archive
description: "Triggered when the user types openpm:archive. Archives finalized spec folders with version snapshots. Supports archiving, listing archived versions, and restoring old versions."
---

# openpm:archive — Spec Archive Management

Manage the version lifecycle of specs: archive finalized specs, browse version history, restore when needed.

## Trigger

Triggered when the user types `openpm:archive` or says "archive", "finalize", "lock version".

---

## Environment Detection

| Environment | Output Mode |
|---|---|
| claude.ai web | **Web mode** — no filesystem; provide archiving guidance and version labeling only |
| Cowork / Desktop app / Claude Code | **Desktop mode** — perform actual file archive operations |

**Do not inform the user about environment detection.**

---

## Web Mode (claude.ai)

Without filesystem access, archiving means "mark this spec as finalized."

When PM requests archiving:

1. **Update spec version** — change from `v0.x (Draft)` to `v1.0 (Finalized)`
2. **Produce finalized spec** — regenerate HTML with header changed to "✅ Finalized — [date]", remove "Draft" label
3. **Remind PM to save:**
   > "Spec marked as finalized (v1.0). I recommend:
   > - Download this HTML for safekeeping
   > - If you need changes later, re-upload and describe what to change — I'll produce v1.1"

---

## Desktop Mode (Cowork / Desktop App / Claude Code)

### Archive Operation

When PM says "archive [feature name]":

1. **Identify archive target:**
   - If PM specified a feature name, locate the folder
   - If not specified, list all spec folders in current directory for PM to choose
   - Read `.openpm/meta.json` for version number

2. **Execute archive:**
   ```
   # Original location
   [feature-name]/
   
   # Copied to _archive/
   _archive/[feature-name]-v[version]/
   ├── spec.html
   ├── demo.html          ← if exists
   ├── wireflow.html      ← if exists
   ├── flow.html           ← if exists
   ├── flow.mermaid        ← if exists
   └── .openpm/
       └── meta.json
   ```

3. **Update meta.json:**
   ```json
   {
     "status": "archived",
     "archivedAt": "YYYY-MM-DD",
     "archivedVersion": "0.3"
   }
   ```

4. **Inform PM:**
   > "Archived to `_archive/[feature-name]-v0.3/`.
   > Original folder is unchanged — you can continue editing for the next version."

### List Archives

When PM says "list archives", "show archives", "what's archived":

1. Scan `_archive/` directory
2. List all archives:

   > **Archive List:**
   > | Spec | Version | Archived Date | Includes |
   > |---|---|---|---|
   > | buy-order-flow | v0.3 | 2026-03-15 | spec, demo, flow, wireflow |
   > | sell-order-flow | v0.1 | 2026-03-10 | spec, flow |

### Restore

When PM says "restore [feature name] v[version]":

1. **Confirm:**
   > "Restore `[feature-name]-v[version]` to working directory?
   > ⚠️ This will overwrite the current `[feature-name]/` folder. Back up current version first?"

2. **If PM wants backup** — archive current version first, then restore
3. **If PM skips backup** — copy directly from `_archive/` to working directory
4. **Update meta.json** — set status back to `"draft"`, increment version

---

## Version Number Convention

- **Draft stage:** v0.1 → v0.2 → v0.3 (increment on major revisions)
- **Finalized:** v1.0 (first archive)
- **Post-finalize edits:** v1.1 → v1.2 (minor) or v2.0 (major rework)
- Version numbers are PM's decision — AI can suggest but not enforce

---

## Notes

- Archive is a "snapshot", not "delete". Original folder stays untouched
- `_archive/` directory is auto-created if it doesn't exist
- After archiving, the original folder can continue being edited as the starting point for the next version
- In web mode, archiving is conceptual (version label). In desktop mode, it's actual file operations
