---
name: openpm-demo
description: "Triggered when the user types openpm:demo. Generates an interactive HTML prototype from a spec. Focuses on faithful representation of business logic: BR annotations, Open Question markers, and a walkable happy path. Visual style is left to AI discretion."
---

# openpm:demo — Spec to Interactive Prototype

Generate an interactive HTML prototype from a spec produced by openpm:new. The point is not how pretty it looks — it's whether the business logic can be correctly "walked" from start to finish.

## Trigger

Triggered when the user types `openpm:demo`.

---

## Environment Detection

Automatically detect the runtime environment on startup to determine output behavior:

**Detection method:** Attempt to access the filesystem (e.g., check if the current working directory is writable).

| Environment | Detection Result | Output Mode |
|---|---|---|
| claude.ai web | No filesystem access | **Web mode** — prototype output as HTML artifact in conversation |
| Cowork / Desktop app / Claude Code | Filesystem accessible | **Desktop mode** — prototype written to spec folder |

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
> "Found spec: [feature-name]/spec.html — want to build the prototype from this?"

## Process

### Step 1: Confirm Prototype Scope

After reading the spec, quickly confirm two things:

1. **Which pages to build?** — Infer the needed screens from the spec's "Main Flow" and "Roles & Permissions" sections, list them for PM confirmation
2. **Interaction depth?** — Confirm the level the PM wants:
   - Static pages (layout display only)
   - Clickable flow (buttons navigate, forms are fillable)
   - Stateful transitions (show state changes, e.g., "Pending → Approved")

Confirm in one question — don't split into too many steps.

### Step 2: Build the Prototype

Output as a single HTML file containing all pages and interaction logic.

**Visual style: free reign.** No restrictions on colors, border radius, or layout approach. AI judges the most appropriate presentation based on the product's nature described in the spec. If the PM has style preferences (e.g., "like an admin dashboard", "like a mobile app"), they can describe it verbally and AI adjusts accordingly.

**However, the following functional elements are mandatory:**

#### ① BR Annotations (Business Rule Mapping)
- Annotate each interactive element with its corresponding business rule ID (BR-001)
- Annotations should not disrupt reading, but show full rule description on hover or click
- Purpose: let anyone viewing the prototype trace back "what business rule is behind this button / field / decision"

#### ② OQ Markers (Open Question Alerts)
- Mark screen areas involving undecided issues with a prominent ⚠️
- Include the Open Question ID and a one-line summary next to the marker
- Do NOT make decisions on behalf of the PM for Open Questions
- Purpose: let stakeholders viewing the prototype notice "this part isn't finalized yet"

#### ③ Walkable Happy Path
- The main operation flow from first step to last must be fully clickable end-to-end
- Every step must have a corresponding screen — no skipping
- If a step involves an Open Question, show a placeholder screen with ⚠️ marker — don't omit it

#### ④ Version Info
- Display at the top or in a fixed position: feature name, version number, date
- Label "This is a prototype draft, not the final design"

#### ⑤ Business Rule Quick Reference
- Provide a collapsible business rule reference table somewhere on the page
- List all BR IDs, rule descriptions, and corresponding screen locations
- Purpose: PM uses this to quickly verify "are all spec rules reflected in the prototype"

### Step 3: Summary

After producing the prototype, attach a brief explanation:
- Which pages and flows this prototype covers
- Which parts were simplified or skipped due to Open Questions
- What the PM should do with this prototype (e.g., walk stakeholders through the flow)

## Interaction Design Priority

When resources are limited, prioritize in this order:

1. **Happy path works** — Main flow can be clicked through end-to-end (highest priority)
2. **State transitions visible** — Key state changes are demonstrated (e.g., approval flow)
3. **Forms are fillable** — Input fields have basic validation hints
4. **Exception feedback** — Error messages and edge case screens

## Technical Spec

- Single HTML file, all CSS and JS inlined
- Pure HTML/CSS/JS, no external frameworks or CDN dependencies
- Page switching via JS show/hide, no routing needed

---

## Output Behavior (Environment-Dependent)

### Web Mode (claude.ai)

- Prototype is output as an HTML artifact directly in the conversation
- PM can preview and download

### Desktop Mode (Cowork / Desktop App / Claude Code)

1. **Write to spec folder:**
   ```
   [feature-name]/
   ├── spec.html          ← existing spec
   ├── demo.html          ← newly generated prototype
   └── .openpm/
       └── meta.json      ← updated metadata
   ```

2. **Update meta.json** — add `"demo": true` and update `"updated"` date

3. **Inform the PM:**
   > "Prototype written to `[feature-name]/demo.html` — open it in any browser."

---

## Notes

- Visual quality is left to AI — but the five functional elements (BR annotations, OQ markers, walkable flow, version info, rule reference) are non-negotiable
- If the spec has Open Questions, the prototype must explicitly mark them — do not fill in answers on your own
