# Reusable Capability Feedback Design

## Goal

Teach `project-feedback-loop` to help agents recognize reusable capability gaps from real, user-authorized project use. The skill should guide agents to abstract from friction without reducing the user's examples into a fixed checklist.

## Context

The current skill already handles user-designated feedback targets, user/developer modes, GitHub issue creation, and developer ROI judgment. It also mentions ergonomics, repeated manual steps, helper commands, scripts, and skill content problems.

The gap is conceptual: the skill does not yet give agents a broad enough model for noticing when a one-time pain point suggests the project should grow a reusable capability. A narrow list such as "new CLI command, new script, new skill workflow" is not sufficient because it turns examples into categories and misses other valid forms.

There is also an ownership gap: feedback targets must be selected by the user, not inferred from whichever project the agent happens to be editing or using. A user may designate multiple feedback targets in the same session, such as `/Users/youla/proj/wx-digest` and `/Users/youla/proj/skills/project-feedback-loop`.

## Design

Add a User Mode section named `Reusable Capability Feedback`.

## Feedback Target Selection

Replace AI-driven project auto-identification with user-specified feedback targets.

Rules:

- The agent may not decide on its own that a project should receive feedback.
- The user can designate one feedback target or several feedback targets in a session.
- Feedback targets are explicit project roots, such as `/Users/youla/proj/wx-digest` or `/Users/youla/proj/skills/project-feedback-loop`.
- When multiple targets are active, each feedback item must be routed to the project it concerns.
- If friction touches several active targets, the issue draft should either choose the primary affected project or produce separate drafts, one per project, with clear cross-reference notes.
- If the affected project is not an active feedback target, the agent should not draft or submit feedback for it. It may ask whether the user wants to add that project as a feedback target.
- Developer mode can still inspect and maintain a project when the user names it as the development target, but user-mode feedback remains limited to designated feedback targets.

The skill should use language like "active feedback targets" rather than "current project" when describing user-mode feedback.

The section should start with an abstract rule:

> Do not ask only "which artifact should be added?" Ask what repeated capability the project is missing, who needs it, what cost it lowers, and what the smallest useful project-owned response could be. The implementation form is a conclusion, not the starting point.

The agent should reason from actual use:

- What repeated action, decision, lookup, setup, conversion, diagnosis, or verification happened?
- What cognitive, operational, performance, access, recovery, composition, or maintenance cost did it create?
- Would another user, future agent, automation, test, or maintainer benefit from a reusable response?
- Is the capability best owned by the project, by documentation, by a project skill, by a script, by a CLI/API surface, by configuration, by tests, or by an external wrapper?
- What is the smallest useful change that preserves the project's general direction?

## Non-Exhaustive Exploration Surface

The skill should include a clearly labeled non-exhaustive surface to widen agent thinking. It must not be written as a mandatory checklist.

Examples of capability surfaces:

- Entry and discovery: canonical entrypoints, environment discovery, working-directory independence, command discovery, examples, onboarding paths.
- Configuration and intent capture: defaults, profiles, templates, saved options, parameter files, context inference, project-local state.
- Composition and orchestration: batch flows, pipelines, multi-step workflows, idempotent runners, task graphs, partial reruns.
- State and continuity: progress, checkpoints, cache, resumability, health checks, status inspection, cleanup.
- Failure and diagnosis: dry runs, validation, error explanation, automatic diagnostics, retry, rollback, repair suggestions.
- Output and interoperability: structured output, stable file formats, logs, reports, machine-readable JSON, artifacts intended for downstream tools.
- Agent and automation affordances: project skills, tool contracts, command wrappers, MCP/API/CLI calling conventions, context files, deterministic scripts.
- Development and maintenance affordances: fixtures, smoke tests, reusable dev helpers, release helpers, migration helpers, verification commands.
- Performance and scale: avoiding repeated expensive work, parallelism, incremental processing, timeout visibility, resource limits.
- Boundary choices: documenting a pattern, keeping it external, declining it, or warning that the request is too special-case for the project core.

## Issue Template Change

Add an optional issue section:

```markdown
## Reusable Capability Angle
Does this feedback imply the project should grow a reusable capability? If so, describe the capability goal, who benefits, and the cost it reduces. Do not force a specific implementation form unless it is clearly implied.
```

This section should be omitted when the issue is a simple bug with no capability-design implication.

## Developer Mode Behavior

Developer mode should consume this section as a hypothesis, not an order.

When an issue proposes or implies reusable capability, the developer agent should evaluate:

- whether the capability is real and recurring
- whether it generalizes beyond the exact observed task
- whether a smaller response captures most of the value
- whether the implementation belongs in project core, CLI, script, skill, docs, tests, config, API, or outside the project
- whether the change has high enough ROI to implement autonomously
- whether the change would overfit a narrow scenario or shift the project direction

High-ROI, normal-scope changes may proceed through implementation, verification, and `git-commit`. Narrow, ambiguous, architectural, or direction-shifting changes require discussion with the user first.

## Acceptance Criteria

- The skill no longer frames reusable feedback as a short list of artifact types.
- Feedback targets are selected manually by the user, not automatically inferred by the agent.
- The skill supports multiple active feedback targets in one session.
- Feedback is routed to the correct active target, with separate drafts when needed.
- User Mode contains an abstract reusable-capability reasoning model.
- The exploration surface is broad and explicitly non-exhaustive.
- Issue drafts can capture a reusable capability angle without prescribing implementation.
- Developer Mode treats reusable capability suggestions as hypotheses to evaluate.
- Existing gates remain intact: no feedback without user-designated project, no pretending to submit without GitHub access, and no commits without `git-commit`.
