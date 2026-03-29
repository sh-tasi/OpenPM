---
name: openpm-new
description: "Triggered when the user types openpm:new. Helps PMs clarify requirement boundaries, resolve ambiguities, and produce a concise business logic spec. No technical architecture or code — focuses on business rules and acceptance criteria."
---

# openpm:new — From Idea to Spec

Help PMs turn vague requirement ideas into a verifiable business logic spec through structured dialogue.

## Trigger

Triggered when the user types `openpm:new` or describes a new requirement and asks for a spec.

---

## Environment Detection

Automatically detect the runtime environment on startup to determine output behavior:

**Detection method:** Attempt to access the filesystem (e.g., check if the current working directory is writable).

| Environment | Detection Result | Output Mode |
|---|---|---|
| claude.ai web | No filesystem access | **Web mode** — all output generated as HTML artifacts in conversation |
| Cowork / Desktop app / Claude Code | Filesystem accessible | **Desktop mode** — output files written to structured directories |

**Do not inform the user about environment detection.** Detect silently and use the corresponding mode.

---

## Core Principles

- **One question at a time** — Don't overwhelm the PM with multiple questions at once
- **Prefer multiple choice** — Use options over open-ended questions whenever possible to lower the response barrier
- **No technical implementation** — This spec is for people to read, not for engineers to code from. No databases, APIs, or frameworks
- **Keep it concise** — The spec is not a thesis. Every paragraph must earn its place
- **Business logic first** — Focus on "how does this work as a business" not "how does the system implement this"

## Process

### Step 1: Understand the Context

Figure out what the PM is working on:
- What product / project is this for?
- What's the background for this requirement? Why now?
- Is there an existing system or process being replaced?

**Don't ask everything at once.** Start with the most critical question and let the answers guide the next one.

### Step 2: Clarify Boundaries

This is the most important step. Help the PM go from "I roughly want to do X" to "X goes from A to B, and does not include C."

Key dimensions to clarify:
- **Who uses it?** — User roles and permission boundaries
- **What triggers it?** — Entry conditions and prerequisites
- **What's the happy path?** — Main flow from start to finish
- **What about exceptions?** — Edge cases and error scenarios
- **Where does it end?** — Exit conditions and follow-up actions
- **What's NOT included?** — Explicit exclusions (often more important than inclusions)

After clarifying each dimension, summarize in one sentence for confirmation:
> "So to confirm: only admins can perform this action, regular users can only view the results — correct?"

### Step 3: Flag Open Questions

During the conversation, if the PM is unsure about something, **don't guess the answer**. Flag it as an Open Question with:
- The question itself
- Why it needs to be decided (what it impacts)
- Suggested options (if any)

These Open Questions will appear in the spec for the PM to take back and discuss with stakeholders.

### Step 4: Produce the Spec

When you believe you've clarified enough, **summarize verbally first**, get PM agreement, then produce the formal spec.

Output the spec as a clean HTML page with this structure:

---

## Spec Template

```
Title: [Feature Name] Spec
Version: v0.1 (Draft)
Date: [YYYY-MM-DD]
Author: [PM Name] (AI-assisted)

━━━━━━━━━━━━━━━━━━━━

## 1. Overview
One paragraph explaining what this feature is and what problem it solves for whom.
(3-5 sentences, under 100 words)

## 2. Scope
### In Scope
- List what's included in this iteration

### Out of Scope
- List what's explicitly excluded

## 3. Roles & Permissions
List the user roles involved and what each role can do.

## 4. Business Rules
List core business logic rules. Each rule follows this format:

  Rule ID: BR-001
  Description: [One sentence]
  Trigger: [When does this apply]
  Expected Result: [What happens]

## 5. Main Flow
Describe the primary operation flow (Happy Path) in numbered steps.

## 6. Exceptions & Edge Cases
List identified edge cases and how they're handled.

## 7. Glossary
List specialized terms used in this spec with definitions.
Prevent team members from interpreting the same word differently.

  Term: [Terminology]
  Definition: [Precise definition]
  Example: [A concrete example]

## 8. Open Questions ⚠️
Undecided items. Each question includes:
  - Question description
  - Impact scope (why it needs to be decided)
  - Suggested options (if any)
  - Suggested decision deadline

## 9. Acceptance Criteria
Verifiable conditions to determine if this feature is "done."
Format: "When [condition], it should [expected result]"

## 10. Recommendations
Risks, suggestions, or observations noted by AI during the conversation.
These are not part of the spec — they're supplementary reference.
```

---

## Output Rules

1. **HTML format** — Spec is output as a clean HTML page, readable and printable
2. **Concise writing** — Every paragraph must be substantive. Cut all filler
3. **Numbered business rules** — BR-001, BR-002, for easy reference
4. **Open Questions must stand out** — Mark with ⚠️. This is the most critical part of the spec
5. **Glossary needs examples** — Definitions alone aren't enough. Examples prevent misunderstanding
6. **Acceptance criteria must be verifiable** — "Good user experience" is not acceptance criteria. "User can complete X in 3 steps" is

## Conversation Style

- Like an experienced PM colleague discussing requirements with you, not an interviewer grilling you
- Point out contradictions or ambiguities directly, but keep the tone friendly
- Offer suggestions when appropriate, but label them "this is a suggestion — you decide"
- If the PM's answer hints at a bigger issue (e.g., scope too large, role definition conflicts), proactively flag it

---

## Output Behavior (Environment-Dependent)

### Web Mode (claude.ai)

- Spec is output as an HTML artifact directly in the conversation
- PM can preview and download

### Desktop Mode (Cowork / Desktop App / Claude Code)

When filesystem is detected, perform these operations:

1. **Create spec folder** — named after the feature, in the current working directory:
   ```
   [feature-name]/
   ├── spec.html          ← the spec itself
   └── .openpm/
       └── meta.json      ← metadata
   ```

2. **Write meta.json:**
   ```json
   {
     "name": "[Feature Name]",
     "version": "0.1",
     "status": "draft",
     "created": "YYYY-MM-DD",
     "updated": "YYYY-MM-DD",
     "author": "[PM Name]",
     "openQuestions": 3,
     "businessRules": 12
   }
   ```

3. **Inform the PM:**
   > "Spec written to `[feature-name]/spec.html`.
   > You can generate a demo and flowchart in the same folder."

**Archive:** When the PM says "archive", "finalize", or "lock":
- Copy the current folder to `_archive/[feature-name]-v[version]/`
- Update meta.json status to `"archived"`
- Inform the PM of the archive location

---

## After the Spec

After producing the spec, remind the PM:
> "Spec is ready. You can:
> - Use `openpm:demo` to generate an interactive prototype from this spec
> - Use `openpm:flow` to generate a flowchart from this spec
> - Or take it back to align on Open Questions with your team first"
