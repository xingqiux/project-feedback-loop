# Reusable Capability Feedback Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Update `project-feedback-loop` so user-mode feedback uses manually designated active feedback targets, stays quiet by default, submits self-evolution issues automatically, and can abstract reusable capability gaps without reducing examples to a fixed checklist.

**Architecture:** This is a documentation-only skill change. `SKILL.md` remains the source of runtime behavior; the existing spec documents the design rationale.

**Tech Stack:** Markdown skill file, YAML frontmatter validation through `quick_validate.py`, Git.

---

### Task 1: Active Feedback Targets

**Files:**
- Modify: `/Users/youla/proj/skills/project-feedback-loop/SKILL.md`

- [x] **Step 1: Replace auto-identification wording**

Update Activation Priority and Project Identity language so feedback is based on user-designated active feedback targets, not agent-inferred current projects.

- [x] **Step 2: Add multi-target routing**

Add rules for one or more active feedback targets, routing each feedback item to the correct project, splitting cross-project feedback when needed, and asking before adding non-active projects.

- [x] **Step 3: Verify wording**

Run: `rg -n "active feedback targets|current project|user-designated|auto" /Users/youla/proj/skills/project-feedback-loop/SKILL.md`

Expected: active feedback target wording is present; user-mode feedback no longer depends on "current project" auto-selection.

### Task 2: Reusable Capability Feedback

**Files:**
- Modify: `/Users/youla/proj/skills/project-feedback-loop/SKILL.md`

- [x] **Step 1: Add abstract capability reasoning**

Add a User Mode section that tells the agent to ask what repeated capability is missing, who benefits, which cost is reduced, and what the smallest useful project-owned response could be.

- [x] **Step 2: Add non-exhaustive exploration surface**

Add broad examples covering entry/discovery, configuration, composition, state, failure recovery, output, agent/automation affordances, development helpers, performance, and boundary choices. Mark the surface explicitly non-exhaustive and not a mandatory checklist.

- [x] **Step 3: Extend issue template**

Add optional `Reusable Capability Angle` to submitted issue bodies and developer-mode evaluation as a hypothesis rather than an order.

### Task 3: Validation

**Files:**
- Validate: `/Users/youla/proj/skills/project-feedback-loop/SKILL.md`

- [x] **Step 1: Validate skill structure**

Run: `python /Users/youla/.codex/skills/.system/skill-creator/scripts/quick_validate.py /Users/youla/proj/skills/project-feedback-loop`

Expected: `Skill is valid!`

- [x] **Step 2: Check for placeholders and contradictions**

Run: `rg -n "TODO|TBD|placeholder|without the user designating|current task is inside" /Users/youla/proj/skills/project-feedback-loop/SKILL.md`

Expected: no placeholders; no old rule that treats being inside a project as enough for feedback.

- [x] **Step 3: Inspect diff**

Run: `git -C /Users/youla/proj/skills/project-feedback-loop diff --check && git -C /Users/youla/proj/skills/project-feedback-loop diff --stat`

Expected: no whitespace errors; only intended markdown files changed.

### Task 4: Quiet Self-Evolution

**Files:**
- Modify: `/Users/youla/proj/skills/project-feedback-loop/SKILL.md`
- Modify: `/Users/youla/proj/skills/project-feedback-loop/docs/superpowers/specs/2026-06-05-reusable-capability-feedback-design.md`

- [x] **Step 1: Remove draft-first user-mode behavior**

User Mode now says not to present issue drafts, not to ask for confirmation for each issue, and not to narrate feedback collection.

- [x] **Step 2: Make automatic issue submission the default**

Feedback Workflow now says active feedback targets should be submitted silently with `gh issue create` when repo and issue destination are clear.

- [x] **Step 3: Keep only minimal unblock questions**

The skill now asks only for missing/ambiguous target selection, non-active project routing, repository destination, authentication status when requested, or sensitive disclosure risk.

### Task 5: Developer Mode Boundary And Issue Language

**Files:**
- Modify: `/Users/youla/proj/skills/project-feedback-loop/SKILL.md`
- Modify: `/Users/youla/proj/skills/project-feedback-loop/docs/superpowers/specs/2026-06-05-reusable-capability-feedback-design.md`

- [x] **Step 1: Separate developer designation from feedback designation**

The skill now says that naming the agent as a project developer or maintainer creates a development target, not an active feedback target.

- [x] **Step 2: Scope automatic issue creation to User Mode**

Developer Mode now says newly discovered friction should be reported, directly fixed when normal-scope/high-ROI, or turned into an issue only when the user explicitly asks for issue creation or designates the project as an active feedback target.

- [x] **Step 3: Add issue language policy**

Issue titles, headings, and bodies now default to the current conversation language. Chinese conversations, Chinese projects, or Chinese feedback produce Chinese GitHub issues unless the user asks otherwise.

- [x] **Step 4: Add pressure scenarios**

The skill now includes checks for Developer Mode not auto-filing incidental friction and for Chinese feedback producing Chinese issue titles and bodies.
