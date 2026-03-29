# OpenPM

**Turn vague product ideas into verifiable specs, interactive prototypes, and flowcharts — powered by AI.**

OpenPM is a set of AI skills for Product Managers. Upload a skill file to Claude, describe your requirement, and get structured output you can use immediately.

Inspired by [OpenSpec](https://github.com/shao-shenhan/openspec) (Spec-Driven Development for engineers), OpenPM brings the same structured discipline to the PM side — no code, no technical architecture, just business logic.

---

## What's Inside

| Command | Skill | What it does |
|---|---|---|
| `openpm:new` | Spec Writer | Guided conversation → Business logic spec (HTML) |
| `openpm:demo` | Prototype Builder | Spec → Interactive HTML prototype with BR annotations |
| `openpm:flow` | Flowchart Generator | Spec → Interactive HTML flowchart + Mermaid source |
| `openpm:wireflow` | Mobile Wireflow | Spec → Horizontal mobile wireflow with phone frames |
| `openpm:archive` | Archive Manager | Version snapshots, archive, list, restore |

### openpm:new

Your AI PM partner for requirement clarification. It asks one question at a time, helps you define boundaries, flags what's unclear as **Open Questions**, and outputs a clean spec with:

- Numbered business rules (BR-001, BR-002...)
- Scope definition (in/out)
- Roles & permissions
- Acceptance criteria
- Glossary with examples
- Open Questions with impact analysis ⚠️

### openpm:demo

Turns your spec into a clickable HTML prototype. Not a design tool — a **logic verification tool**. Every interactive element is annotated with its business rule ID, every undecided item is flagged with ⚠️, and the happy path is fully walkable end-to-end.

### openpm:flow

Generates flowcharts in two formats: an interactive HTML version (zoomable, clickable nodes that reveal business rules) and Mermaid source code (paste into HackMD, Notion, GitHub, or any Mermaid-compatible tool). Automatically suggests the right chart type — flowchart, swimlane, state diagram, or sequence diagram.

### openpm:wireflow

Generates a blue-and-white Mobile App style horizontal wireflow. Each flow step is rendered as a phone screen frame (200px wide), connected by labeled arrows with color-coded branches (green for confirm, red for reject/timeout). Business rules are annotated directly on screens. Perfect for stakeholder walkthroughs and engineering handoff.

### openpm:archive

Manages spec versioning. In desktop mode: archives spec folders to `_archive/` with version snapshots, lists archived versions, and restores old versions on demand. In web mode: marks specs as finalized with updated version labels.

---

## Two Modes, Zero Config

OpenPM auto-detects your environment and adapts:

| Environment | What happens |
|---|---|
| **claude.ai** (web) | Output appears as HTML artifacts in the conversation. Download when ready. |
| **Cowork / Desktop / Claude Code** | Files are written to structured folders. One folder per spec, ready for git. |

```
# Desktop mode creates this structure automatically:
buy-order-flow/
├── spec.html          ← the spec
├── demo.html          ← interactive prototype
├── wireflow.html      ← mobile wireflow
├── flow.html          ← interactive flowchart
├── flow.mermaid       ← Mermaid source (paste into HackMD/Notion)
└── .openpm/
    └── meta.json      ← version, status, timestamps

# Archive when finalized (via openpm:archive):
_archive/
└── buy-order-flow-v0.3/
```

No flags, no config. Same skill file works everywhere.

---

## Quick Start

### 1. Pick your language

| Language | Folder |
|---|---|
| English | `skills/en/` |
| 繁體中文 | `skills/zh-TW/` |

### 2. Upload to Claude

1. Open [claude.ai](https://claude.ai)
2. Start a new conversation
3. Upload the skill file (e.g., `openpm-new.md`)
4. Type the command (e.g., `openpm:new`) and describe your requirement

### 3. Follow the flow

```
openpm:new  →  Produce a spec
                    ↓
         ┌─────────┼─────────────┐
         ↓         ↓             ↓
    openpm:demo  openpm:flow  openpm:wireflow
    (prototype)  (flowchart)  (mobile wireflow)
                    ↓
              openpm:archive
              (version snapshot)
```

Each skill works independently. You can:
- Start with `openpm:new` and then use `:demo`, `:flow`, or `:wireflow` in the same conversation
- Or upload a spec you already have and go straight to any output skill
- Use `openpm:archive` to snapshot finalized specs

---

## Example Workflow

**PM:** uploads `openpm-new.md` → types `openpm:new`

> "I need a feature where sellers can list items for sale, buyers can place orders, and there's a review process before the item ships."

**Claude:** asks clarifying questions one at a time...

**Output:** A structured spec with business rules, acceptance criteria, open questions

**PM:** types `openpm:demo`

**Output:** An interactive HTML prototype where you can click through the entire buy→review→ship flow, with BR-001 annotations on every element

**PM:** types `openpm:flow`

**Output:** A zoomable flowchart + Mermaid code showing the complete order lifecycle

---

## Design Philosophy

**For PMs, not engineers.** No databases, no APIs, no system architecture. Pure business logic.

**Specs are short.** Every paragraph earns its place. If it's filler, it gets cut.

**Open Questions are first-class citizens.** Unclear requirements don't get swept under the rug — they get flagged with ⚠️ and tracked with impact analysis.

**Prototypes verify logic, not aesthetics.** The prototype exists so you can walk stakeholders through the flow and check if the business rules are correctly represented. Visual polish is secondary.

**Flowcharts come in two flavors.** Interactive HTML for presentations, Mermaid source for documentation. Same content, different consumption contexts.

---

## Repo Structure

```
openpm/
├── README.md
├── README.zh-TW.md
├── LICENSE
├── skills/
│   ├── en/                    # English skills
│   │   ├── openpm-new.md
│   │   ├── openpm-demo.md
│   │   ├── openpm-flow.md
│   │   ├── openpm-wireflow.md
│   │   └── openpm-archive.md
│   └── zh-TW/                 # 繁體中文技能
│       ├── openpm-new.md
│       ├── openpm-demo.md
│       ├── openpm-flow.md
│       ├── openpm-wireflow.md
│       └── openpm-archive.md
└── examples/                  # Example specs & outputs (coming soon)
```

---

## FAQ

**Q: Do I need Claude Pro?**
A: These skills work with any Claude plan on claude.ai. Longer specs may benefit from Pro's extended context.

**Q: Does it work with Cowork / Claude Desktop?**
A: Yes. The skills auto-detect filesystem access and switch to desktop mode — creating structured folders, writing files, and supporting archive/versioning. Same skill file, no config needed.

**Q: Can I use these with other AI models?**
A: They're optimized for Claude but the SKILL.md format is plain Markdown. Other models that accept system-level instructions may work with some adaptation.

**Q: Can I customize the spec template?**
A: Absolutely. The skill files are plain Markdown — edit the template section to match your team's conventions.

**Q: What if my spec is in a different format?**
A: `openpm:demo` and `openpm:flow` accept any spec format — just paste the text or upload the file. They work best with `openpm:new` output but don't require it.

---

## Contributing

Issues and PRs welcome. If you've adapted OpenPM for a specific domain (healthcare, fintech, e-commerce, etc.), consider sharing your customized skill files.

---

## License

[MIT](LICENSE)

---

## Credits

Created by [Sheng Hao](https://github.com/shao-shenhan). Inspired by the Spec-Driven Development philosophy and [OpenSpec](https://github.com/shao-shenhan/openspec).

> *"The spec is the product. Everything else is implementation."*
