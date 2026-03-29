---
name: openpm-flow
description: "Triggered when the user types openpm:flow. Generates flowcharts from a spec. Outputs both an interactive HTML flowchart and Mermaid source code."
---

# openpm:flow — Spec to Flowchart

Generate visual flowcharts from a spec produced by openpm:new. Outputs two formats so PMs can use whichever fits their workflow.

## Trigger

Triggered when the user types `openpm:flow`.

---

## Environment Detection

Automatically detect the runtime environment on startup to determine output behavior:

**Detection method:** Attempt to access the filesystem (e.g., check if the current working directory is writable).

| Environment | Detection Result | Output Mode |
|---|---|---|
| claude.ai web | No filesystem access | **Web mode** — flowchart as HTML artifact, Mermaid displayed in conversation |
| Cowork / Desktop app / Claude Code | Filesystem accessible | **Desktop mode** — files written to spec folder |

**Do not inform the user about environment detection.** Detect silently and use the corresponding mode.

---

## Prerequisites

Requires a spec as input. Sources can be:
- A spec just produced by `openpm:new` in the same conversation
- Spec text pasted by the user
- A spec file uploaded by the user
- **Desktop mode:** `spec.html` in the current directory or subdirectory (auto-detected)

If no spec is found, ask directly:
> "Please provide the spec — you can paste the text or upload a file."

In desktop mode, if `[feature-name]/spec.html` is detected in the current directory:
> "Found spec: [feature-name]/spec.html — want to generate flowcharts from this?"

## Process

### Step 1: Identify Flows

Extract everything from the spec that can be visualized as a flowchart:

- **Main flow** (Happy Path) — must always be included
- **Exception flows** — extracted from "Exceptions & Edge Cases"
- **Role interactions** — if multi-role collaboration is involved, show who's responsible at each step
- **State transitions** — if there's a state machine (e.g., order status), show the state diagram

List the identified flows and let the PM choose which ones to produce:

> "I identified the following flows from the spec:
> 1. [Main operation flow] — recommended
> 2. [Approval flow] — involves multiple roles
> 3. [Exception handling flow]
> 
> Want all of them, or just specific ones?"

### Step 2: Confirm Chart Type

Based on the flow's nature, suggest the appropriate chart type:

| Scenario | Suggested Chart |
|---|---|
| Single role's operation steps | Flowchart |
| Multi-role collaboration | Swimlane diagram |
| State transitions | State diagram |
| Time-sequenced interactions | Sequence diagram |

Confirm the PM's preference in one question.

### Step 3: Generate Flowcharts

Each flow is output in two formats:

#### Format 1: Interactive HTML Flowchart

Output as an HTML file with these features:
- **Zoomable** — mouse wheel zoom, drag to pan
- **Clickable nodes** — clicking a node shows the corresponding business rule or description
- **Color-coded** — different node types use different colors:
  - Start/End: dark gray
  - Regular steps: white background, blue border
  - Decisions/branches: yellow background
  - Exceptions/errors: red background
  - Open Question related: orange background + ⚠️ marker
- **BR ID labels** — each step annotated with its corresponding business rule
- **Legend** — fixed in the top-right corner showing the color scheme

**Technical spec:**
- Single HTML file, CSS and JS inlined
- Pure SVG or Canvas rendering, no external libraries
- Built-in layout algorithm, no manual positioning needed
- Desktop-first, minimum width 800px

#### Format 2: Mermaid Source Code

Also provide a plain-text Mermaid syntax version so the PM can:
- Paste into Markdown documents or Notion
- Paste into Mermaid-compatible tools (HackMD, GitLab, GitHub)
- Version control (plain text diffs easily)
- Make quick edits (intuitive syntax)

Present Mermaid code in a code block with a brief note:
> "Here's the Mermaid source code. You can paste it into HackMD, Notion, or any Mermaid-compatible platform for instant preview."

### Step 4: Summary

After producing the flowcharts, include:
- Which parts of the spec this chart covers
- Which steps were simplified due to Open Questions
- If there are multiple flowcharts, how they relate to each other

## Output Rules

1. **Both formats provided** — Interactive HTML + Mermaid text
2. **Concise node text** — No more than 8-10 words per node; details go in click popups
3. **Business rule mapping** — Each step annotated with BR ID
4. **Open Questions marked** — ⚠️ on all nodes involving undecided items
5. **Consistent flow direction** — Main flow goes top-to-bottom or left-to-right, don't mix directions
6. **Branches must converge** — Every decision branch must eventually rejoin the main flow or reach an endpoint. No dead ends

## Quality Checklist

Self-check before output:
- [ ] Can the main flow be traced from start to end?
- [ ] Are all decision branch conditions clearly labeled? (yes/no, condition A/B)
- [ ] Are there any orphaned nodes (unreachable or no exit)?
- [ ] Do exception flows have proper endings? (where does error handling return to)
- [ ] Are node labels short enough to read at a glance?
- [ ] Is the Mermaid syntax valid and renderable?

---

## Output Behavior (Environment-Dependent)

### Web Mode (claude.ai)

- HTML flowchart is output as an artifact — PM can preview or download directly
- Mermaid code is displayed directly in the conversation for easy copying

### Desktop Mode (Cowork / Desktop App / Claude Code)

1. **Write to spec folder:**
   ```
   [feature-name]/
   ├── spec.html          ← existing spec
   ├── demo.html          ← possibly existing prototype
   ├── flow.html           ← newly generated interactive flowchart
   ├── flow.mermaid        ← Mermaid source code
   └── .openpm/
       └── meta.json      ← updated metadata
   ```

2. **If multiple flowcharts**, use descriptive filenames:
   ```
   flow-main.html           ← main flow
   flow-main.mermaid
   flow-approval.html       ← approval flow
   flow-approval.mermaid
   ```

3. **Update meta.json** — add `"flow": true`, list flowchart files, update `"updated"` date

4. **Inform the PM:**
   > "Flowcharts written to `[feature-name]/`:
   > - `flow.html` — open in browser for interactive view
   > - `flow.mermaid` — paste into HackMD or Notion"

---

## Notes

- For complex systems, split into multiple charts — one flow per chart. Don't cram all logic into a single diagram
- If the spec has Open Questions, mark them in the flowchart — do not decide the flow direction on your own
