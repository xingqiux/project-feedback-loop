# Reusable Capability Feedback Design

## Goal

Teach `project-feedback-loop` to help agents recognize reusable capability gaps from real, user-authorized project use and turn them into quiet self-evolution issues. The skill should guide agents to abstract from friction without reducing the user's examples into a fixed checklist or repeatedly asking the user to review drafts.

## Context

The current skill already handles user-designated feedback targets, user/developer modes, GitHub issue creation, and developer ROI judgment. It also mentions ergonomics, repeated manual steps, helper commands, scripts, and skill content problems.

The gap is conceptual: the skill does not yet give agents a broad enough model for noticing when a one-time pain point suggests the project should grow a reusable capability. A narrow list such as "new CLI command, new script, new skill workflow" is not sufficient because it turns examples into categories and misses other valid forms.

There is also an ownership and interaction gap: feedback targets must be selected by the user, not inferred from whichever project the agent happens to be editing or using, and developer designation is not feedback designation. Once user-mode feedback targets are active, the agent should not interrupt the user with feedback drafts or repeated confirmation prompts. A user may designate multiple feedback targets in the same session, such as `/Users/youla/proj/wx-digest` and `/Users/youla/proj/skills/project-feedback-loop`.

## Design

Add a User Mode section named `Reusable Capability Feedback`.

## Feedback Target Selection

Replace AI-driven project auto-identification with user-specified feedback targets.

Rules:

- The agent may not decide on its own that a project should receive feedback.
- The user can designate one feedback target or several feedback targets in a session.
- Feedback targets are explicit project roots, such as `/Users/youla/proj/wx-digest` or `/Users/youla/proj/skills/project-feedback-loop`.
- When multiple targets are active, each feedback item must be routed to the project it concerns.
- If friction touches several active targets, the agent should either choose the primary affected project or submit separate issues, one per project, with clear cross-reference notes.
- If the affected project is not an active feedback target, the agent should not submit feedback for it. It may ask whether the user wants to add that project as a feedback target.
- Developer mode can still inspect and maintain a project when the user names it as the development target, but user-mode feedback remains limited to designated feedback targets.
- A project named as a developer or maintenance target is not automatically an active feedback target.

The skill should use language like "active feedback targets" rather than "current project" when describing user-mode feedback.

## Quiet Self-Evolution

User mode should be quiet by default:

- Do not present issue drafts for user approval.
- Do not ask for confirmation before every issue.
- Do not announce every feedback point while the user is trying to complete the main task.
- When an active feedback target has a clear repository and issue destination, submit the issue automatically.
- Mention submitted issue URLs only briefly in a natural final summary or when the user asks.
- Ask the user only when required to unblock target selection, repository access, authentication, sensitive disclosure, or cross-project routing.
- If submission is blocked by missing GitHub remote or authentication, do not pretend to submit and do not spam a draft into the conversation.

Quiet automatic submission applies only to User Mode. Developer Mode may inspect and implement issues, but should not create new issues for incidental friction discovered during maintenance unless the user explicitly asks for issue creation or designates the project as an active feedback target.

## Issue Language

GitHub issues should follow the user's language context:

- Default to the current conversation language.
- For Chinese conversations, Chinese projects, or Chinese feedback, submit issue titles, headings, and bodies in Chinese.
- If the user explicitly specifies an issue language, follow that language.
- Keep the issue structure stable, but translate section headings and field content to the target language.

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

This section should be omitted when the issue is a simple bug with no capability-design implication. It is part of the submitted issue body, not a user-facing draft.

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

Developer mode should not automatically create new issues for newly discovered friction in the project being maintained. The agent should report the trade-off, fix normal-scope/high-ROI friction directly, or create a new issue only when the user asks to record feedback, asks to create an issue, designates the project as an active feedback target, or asks the agent to act as a feedback user.

## Acceptance Criteria

- The skill no longer frames reusable feedback as a short list of artifact types.
- Feedback targets are selected manually by the user, not automatically inferred by the agent.
- The skill supports multiple active feedback targets in one session.
- Developer designation is separate from feedback designation.
- Feedback is routed to the correct active target, with separate submitted issues when needed.
- User Mode contains an abstract reusable-capability reasoning model.
- The exploration surface is broad and explicitly non-exhaustive.
- Submitted issues can capture a reusable capability angle without prescribing implementation.
- User Mode is quiet by default: no routine issue drafts, no per-issue confirmation prompts, and no feedback chatter during the main task.
- Developer Mode treats reusable capability suggestions as hypotheses to evaluate.
- Developer Mode does not auto-create new issues for incidental maintenance friction.
- Submitted issues follow the current conversation language by default, including Chinese issue titles and bodies for Chinese conversations.
- Existing gates remain intact: no feedback without user-designated project, no pretending to submit without GitHub access, and no commits without `git-commit`.
