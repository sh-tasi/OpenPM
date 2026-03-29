---
name: openpm-wireflow
description: "Triggered when the user types openpm:wireflow. Generates a blue-and-white Mobile App style horizontal wireflow HTML from a spec. Each flow step is rendered as a phone frame, connected by arrows with labels and BR annotations. Ideal for stakeholder walkthroughs and engineering handoff."
---

# openpm:wireflow — Spec to Mobile Wireflow

Generate a blue-and-white Mobile App style horizontal wireflow from a spec produced by openpm:new.
Each flow step is rendered as a phone screen frame, connected by labeled arrows.
All business rule IDs are annotated on corresponding screens — ready for stakeholder review or engineering handoff.

## Trigger

Triggered when the user types `openpm:wireflow`.

---

## Environment Detection

Automatically detect the runtime environment on startup:

| Environment | Detection Result | Output Mode |
|---|---|---|
| claude.ai web | No filesystem access | **Web mode** — wireflow output as HTML artifact in conversation |
| Cowork / Desktop app / Claude Code | Filesystem accessible | **Desktop mode** — wireflow written to spec folder |

**Do not inform the user about environment detection.**

---

## Prerequisites

Requires a spec as input. Sources can be:
- A spec just produced by `openpm:new` in the same conversation
- Spec text pasted or uploaded by the user
- **Desktop mode:** `spec.html` in the current directory or subdirectory (auto-detected)

If no spec is found:
> "Please provide the spec — you can paste the text or upload a file."

---

## Process

### Step 1: Extract Flows from Spec

Read the spec's "Main Flow" section and identify:

1. **How many flows** — each flow becomes a horizontal phone screen sequence
2. **How many steps per flow** — each step becomes a phone screen
3. **Where are branch points** — decision nodes (timeout/confirm/reject) become forked arrows
4. **Role switches** — different roles get different role badges

**No need to ask** — infer directly from the spec and produce. Use tabs when there are multiple flows.

### Step 2: Generate HTML Wireflow

Single HTML file containing all flows.

---

## Visual Spec

### Overall Style
- Blue-and-white palette, background `#f0f4ff`, primary `#2563eb`
- Font: `-apple-system, 'PingFang TC', 'Noto Sans TC', sans-serif`
- Feel: Mobile App design mockup, clean, client-presentable

### Phone Frame
- Width: 200px, border-radius 22px
- Frame color: `#b0bec5`, outer glow: `0 0 0 6px #e8edf5`
- Top notch (dark short bar)
- Step label above each phone (gray capsule: S1, S2…)

### In-Screen UI Components (Unified Style)
Use the following CSS classes — **do not invent new styles**:

| Component | Class | Description |
|-----------|-------|-------------|
| Top nav | `.phone-nav` | Back button, title, right action |
| Gradient header | `.ui-header` | Blue gradient, white text |
| Banner | `.ui-banner` | Dark blue gradient, rounded |
| Content card | `.ui-card` | White, rounded, thin border |
| Selected card | `.ui-card.selected` or `.ui-card.blue-border` | Blue border |
| Large amount | `.ui-amount` | 22px bold |
| Table row | `.ui-row` | flex justify-between, thin divider |
| Primary button | `.ui-btn` | Full-width blue, white text |
| Secondary button | `.ui-btn.outline` | White, blue border |
| Danger button | `.ui-btn.red` | Red background |
| Success button | `.ui-btn.green` | Green background |
| Timer | `.ui-timer` | Yellow background, countdown |
| Lock bar | `.ui-lock-bar` | Red background, "🔒 Cannot cancel" |
| Upload area | `.ui-upload` | Dashed border, centered icon |
| Progress bar | `.ui-progress-wrap` + `.ui-progress` | Blue progress |
| Spinner | `.ui-spinner` | CSS rotating circle |
| Status tag | `.ui-tag.tag-pending/active/process/error` | Colored capsule |
| Dispute box | `.ui-dispute` | Red background, ⚖️ icon |
| BR annotation | `.br-note` | Blue capsule, shows BR-xxx |

### Arrow Connectors
Screens connected by `.arrow-wrap`:
- Default (blue): normal flow progression
- `.arrow-wrap.green`: confirm/agree
- `.arrow-wrap.red`: reject/timeout/exception
- `.arrow-label` above arrow describes what happens

### Annotations
Floating callout boxes next to screens:
```css
.annotation { red border text, absolute positioned next to phone }
.annotation.blue { blue }
.annotation.green { green }
.annotation.yellow { yellow }
```
Used for: timeout rules, BR IDs, special logic notes.

### Role Badge
Each flow's top uses `.role-strip` to show role:
- `.role-buyer`: blue, buyer perspective
- `.role-seller`: purple, seller perspective
- `.role-system`: green, system automated

---

## Tab Structure

Multiple flows use `.flow-tabs` / `.flow-tab` for switching:
```js
function showFlow(id, el) {
  document.querySelectorAll('[id^="flow-"]').forEach(f => {
    f.style.display = f.id === 'flow-' + id ? 'block' : 'none';
  });
  document.querySelectorAll('.flow-tab').forEach(t => t.classList.remove('active'));
  el.classList.add('active');
}
```

---

## Branching Flows

When a flow has decision points (confirm/reject/timeout):

1. **Main line continues right**: normal path
2. **Branch goes down to a new row**: exception path (dispute, cancel)
3. Use `.annotation.red` next to the previous screen to label branch condition

---

## CSS Variables (Complete List)

```css
:root {
  --bg: #f0f4ff;
  --bg2: #e6ecfa;
  --white: #ffffff;
  --blue: #2563eb;
  --blue2: #1d4ed8;
  --blue-light: #dbeafe;
  --blue-pale: #eff6ff;
  --text: #1e293b;
  --text2: #475569;
  --text3: #94a3b8;
  --border: #e2e8f0;
  --border2: #cbd5e1;
  --red: #ef4444;
  --red-light: #fee2e2;
  --green: #10b981;
  --green-light: #d1fae5;
  --yellow: #f59e0b;
  --yellow-light: #fef3c7;
  --shadow: 0 4px 20px rgba(37,99,235,0.10);
  --shadow2: 0 2px 8px rgba(0,0,0,0.08);
}
```

---

## Screen Content Principles

**Each phone screen corresponds to one spec step.** Fill content by:

1. **Screen title**: taken directly from spec step name
2. **Core UI components**: show only the 1–2 most important elements per step, don't overcrowd
3. **Amounts/numbers**: use example figures from spec (e.g., from BR examples)
4. **BR annotations**: add `.br-note` next to every key component
5. **Timer**: any step marked with timer in spec gets `.ui-timer`
6. **🔒 Lock bar**: all post-payment screens show `.ui-lock-bar` at top

---

## Output Rules

1. **Single HTML file** — all CSS/JS inlined, download and open
2. **Horizontally scrollable** — `.canvas-outer { overflow-x: auto }` + `.flow-row { min-width: max-content }`
3. **All BR IDs annotated** — every key action has `.br-note`
4. **Branches rendered** — exception flows cannot be omitted
5. **Roles clearly marked** — role badges for instant role identification
6. **Annotations complete** — timeout rules, cancel conditions, BR notes all present

---

## Quality Checklist

Before output:
- [ ] Every phone screen has a step label (S1, S2…)?
- [ ] Every arrow has a label explaining what happens?
- [ ] Branch flows (exceptions/disputes) have their own screens?
- [ ] Post-payment screens have 🔒 lock bar?
- [ ] Timed steps have `.ui-timer`?
- [ ] Key components have `.br-note` annotations?
- [ ] Horizontal scrolling works when there are many screens?

---

## Output Behavior (Environment-Dependent)

### Web Mode (claude.ai)

- Wireflow output as HTML artifact in conversation
- PM can preview and download

### Desktop Mode (Cowork / Desktop App / Claude Code)

1. **Write to spec folder:**
   ```
   [feature-name]/
   ├── spec.html          ← existing spec
   ├── wireflow.html      ← newly generated wireflow
   └── .openpm/
       └── meta.json      ← updated metadata
   ```

2. **Update meta.json** — add `"wireflow": true`, update `"updated"` date

3. **Inform the PM:**
   > "Wireflow written to `[feature-name]/wireflow.html` — open in any browser."

---

## After Output

> "Wireflow is ready. You can:
> - Use `openpm:demo` to generate a clickable interactive prototype
> - Use `openpm:flow` to generate a flowchart (with state machine)
> - Or hand this wireflow directly to engineering for implementation reference"

---

## Notes

- The more complete the spec, the more precise the wireflow. If spec only has happy path, exception flows can be simplified
- Phone screen width is fixed at 200px — don't exceed, or horizontal layout becomes too wide
- Each flow should have at most 8–10 phone screens. Beyond that, consider splitting into sub-flows
