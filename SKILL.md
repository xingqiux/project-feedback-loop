---
name: project-feedback-loop
description: Use when the user explicitly designates one or more local development projects as active feedback targets, says the agent is a project user or developer, asks to evaluate project feedback/issues, or asks to maintain a project from GitHub issues.
---

# Project Feedback Loop

## Overview

This skill turns user-authorized agent experience with a project into quiet, self-evolving GitHub issues. It is a high-priority operating loop for local personal or second-development projects that have code, CLIs, scripts, docs, or project-specific skills.

## Activation Priority

Treat this skill like a process skill: load it early when it might apply.

Use it when any of these are true:

- The user says they are a user of a project, such as "you are the user of `/Users/youla/proj/wx-digest`".
- The user says they are a developer of a project, such as "you are the developer of `/Users/youla/proj/wx-digest`".
- The user explicitly names a project as the feedback target.
- The user designates one or more active feedback targets, such as `/Users/youla/proj/wx-digest` and `/Users/youla/proj/skills/project-feedback-loop`.
- The user asks to inspect, triage, or implement project feedback or GitHub issues.
- The current task is creating, editing, installing, symlinking, validating, or using skills under `/Users/youla/proj/skills` or `/Users/youla/.agents/skills`.

Do not use it for one-off files, generic web research, unrelated system setup, or projects whose root and repository cannot be identified.

Critical gate: feedback belongs only to user-designated active feedback targets. If the user has not named or approved a feedback target, do not submit, accumulate, or discuss feedback for any project. Continue the user's task normally.

Developer designation is not feedback designation. If the user says the agent is the developer or maintainer of a project, that project is a development target for issue triage and implementation; it is not automatically an active feedback target.

## Active Feedback Targets

Before user-mode feedback, establish the active feedback targets. Do not infer targets from the current directory or from whichever project the agent happens to edit or use.

Preferred signals for active feedback targets:

1. explicit project paths in the user's message
2. an explicit statement such as "be the feedback user for these projects"
3. user approval after the agent asks whether to add a project as a feedback target

A session may have one active feedback target or several. When multiple targets are active:

- Route each feedback item to the project it concerns.
- If one friction point touches several active targets, choose the primary affected project or submit separate issues with clear cross-reference notes.
- If the affected project is not active, do not submit feedback for it. Ask whether to add that project as an active feedback target only when the feedback opportunity is important and cannot be routed to an active target.
- Use "active feedback targets" rather than "current project" when describing user-mode feedback.

For each active target, confirm repository metadata when issue submission or developer triage is needed:


```bash
git -C <project-root> rev-parse --show-toplevel
git -C <project-root> remote -v
```

If the root is uncertain, ask one concise question for the project path. If the root is certain but there is no GitHub remote, do not show a user-facing draft; skip submission and mention the blocker only if the user asks about feedback status or issue creation.

## Skills Project

`/Users/youla/proj/skills` can itself be a project, but only when the user designates it as the feedback or maintenance target. Treat it as the local Skills Project when the user asks to author, edit, validate, install, symlink, use, or maintain personal skills as a project.

For the Skills Project:

- User mode means the agent is using a skill as a consumer and should quietly submit user-authorized friction such as unclear triggers, missing workflow steps, weak validation guidance, bad default prompts, broken bundled scripts, or symlink/install confusion.
- Developer mode means the agent is maintaining skills and should first inspect feedback issues for the skill being changed, evaluate their breadth and ROI, then report relevant user-mode or agent-discovered needs before editing.
- If `/Users/youla/proj/skills` does not have a GitHub remote, do not show a user-facing draft or pretend live issue submission happened.
- A skill can feedback on itself. For `project-feedback-loop`, use this carefully: only file self-feedback when its own workflow caused a concrete problem or missed a project-feedback opportunity.

## Mode Selection

There are two modes.

### User Mode

Use when the user says the agent is a project user, or explicitly asks the agent to use a project and provide feedback.

User mode is quiet by default. Do not narrate feedback collection, do not show issue drafts, and do not ask for confirmation for each issue. The point is automatic self-evolution, not turning the user's session into a feedback review meeting.

The agent should:

1. Run the requested project workflow normally.
2. Notice real friction from the user's point of view.
3. Explore feedback across ergonomics, access, performance, docs, behavior, and project-specific skills.
4. Distinguish signal from noise.
5. Route feedback only to the relevant active feedback target.
6. Submit GitHub issues for project-level improvements only for active feedback targets.
7. Stay quiet unless a short question is required to unblock target selection, repository access, authentication, or cross-project routing.

Acceptable user-facing mentions:

- Ask a short question when the active target is missing or ambiguous.
- Ask a short question when an important feedback item concerns a non-active project.
- Report an issue URL briefly after submission when it naturally fits the final task summary.
- Mention that issue submission was blocked only if the user asks about feedback status or the blocker affects the requested work.

Avoid:

- presenting feedback drafts for approval
- saying "I recorded a feedback point" during the main task
- asking before every issue
- pausing the user's workflow for low-risk feedback bookkeeping

Issue-worthy signals:

- reproducible command failures or broken behavior
- confusing or incomplete CLI help, docs, examples, or error messages
- too many required steps before useful work, such as needing to `cd` into a specific directory and then run a long command
- commands that require long, fragile, hard-to-remember argument strings for common workflows
- slow, stuck, or noticeably laggy workflows
- awkward access patterns, poor defaults, or setup that feels harder than the value it provides
- repeated manual steps that should be automated
- mismatches between documented and actual behavior
- missing project skill instructions that caused wasted exploration
- skill content that feels unsuitable, misleading, too narrow, too broad, or poorly matched to actual use
- brittle setup or authentication flows
- outputs that are hard to inspect, parse, or reuse

### Reusable Capability Feedback

When friction appears, do not ask only "which artifact should be added?" Ask what reusable capability the project may be missing. The implementation form is a conclusion, not the starting point.

Reason from actual use:

- What repeated action, decision, lookup, setup, conversion, diagnosis, or verification happened?
- What cost did it create: cognitive, operational, performance, access, recovery, composition, maintenance, or something else?
- Who would benefit from a reusable response: another user, future agent, automation, tests, maintainers, or downstream tools?
- Where should the capability live: project core, documentation, project skill, script, CLI/API surface, configuration, tests, external wrapper, or outside the project?
- What is the smallest useful project-owned change that preserves the project's general direction?

Use this non-exhaustive exploration surface to widen thinking. It is not a mandatory checklist, and examples from the user are not limits on valid feedback:

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

Not issue-worthy by default:

- temporary network failures
- one-off user preference changes
- mistakes caused by the agent ignoring existing instructions
- generic complaints without a project-specific action
- problems already covered by an open issue unless there is useful new evidence
- friction from a project the user did not designate for feedback

### Developer Mode

Use when the user says the agent is a project developer, asks to implement or maintain the project, or asks to check user or AI-generated feedback.

Developer mode does not automatically create new issues in the project being maintained. If new friction appears while running, testing, or maintaining that project, report the trade-off, fix it directly when it is normal-scope and high-ROI, or create an issue only when the user explicitly asks to record or submit it. Do not treat incidental startup, setup, or local workflow friction as user-mode feedback merely because it was observed during maintenance.

Before development work:

1. Inspect open GitHub issues for the project.
2. Separate user-requested needs from agent-discovered needs.
3. Explore the feedback in detail: reproduce if possible, inspect affected code/docs/CLI/skills, and understand who benefits.
4. Evaluate whether the request is too narrow, over-specialized, or likely to bias the project toward a small scenario.
5. Report relevant issues, trade-offs, and the recommended next step.
6. For normal, high-ROI improvements, the agent may proceed autonomously through implementation, verification, commit, authorized push, and issue closure.

Developer evaluation questions:

- Is this feedback a real project-level problem or a one-user edge case?
- Does the fix generalize across normal users and workflows?
- Would the proposed change require a broad refactor for a tiny special case?
- Would it make the project lean toward a narrow scenario at the expense of its main purpose?
- Is there a smaller change, clearer doc, better default, shortcut, or helper command that gets most of the value?
- If the issue implies a reusable capability, is that capability real and recurring or just a one-off artifact request?
- Where should the capability live: project core, CLI, script, skill, docs, tests, config, API, external wrapper, or outside the project?
- What is the expected ROI compared with implementation and maintenance cost?

Communicate with the user before implementing when the feedback is narrow, ambiguous, architectural, likely to cause a broad refactor, or likely to shift the project toward a special-case workflow.

Treat any reusable capability suggestion as a hypothesis to evaluate, not an implementation order.

For high-ROI, normal-scope improvements:

1. Inspect the issue and relevant code/docs.
2. Implement the focused change.
3. Verify with the project's tests or the original workflow.
4. Commit the change.
5. Push the current branch when the user explicitly requested automatic push or the project/session maintenance preference already says to push after commit.
6. After a successful push, close the completed issue by default with a concise comment covering what changed, branch, commit, verification commands, and remaining risk.

Commit rule: before staging or committing, invoke and follow the `git-commit` skill. Do not hand-roll the commit process.

Push and issue closure rule:

- If automatic push is authorized by the user or project/session maintenance preference, push the current implementation branch after a successful commit.
- If automatic push is not authorized, ask whether to push after committing; do not leave the user guessing why the remote issue cannot see the change.
- After implementation, verification, commit, and push all succeed, close the related issue by default unless the user asked to keep it open, review is required, or the fix is partial.
- The closing comment should mention the solved feedback, branch, commit hash, verification commands, and any remaining risk.
- If push fails, do not close the issue; report the blocker and keep the local commit intact.

Issue creation in developer mode:

- Do not automatically create issues for newly discovered friction in the project being maintained.
- Create or submit a new issue only when the user asks to record feedback, asks to create an issue, designates the project as an active feedback target, or asks the agent to act as a feedback user.
- When the user has not requested a new issue, include important newly discovered friction in the developer report or implement the small fix directly when it meets the high-ROI rule.

Useful commands:

```bash
gh issue list --repo <owner>/<repo> --limit 30
gh issue view <number> --repo <owner>/<repo>
```

If `gh` is unavailable or unauthenticated, use the GitHub web URL from the remote when possible, or ask the user to authenticate before live issue operations.

## User-Mode Feedback Workflow

This workflow applies only in User Mode for active feedback targets. It does not authorize automatic issue creation in Developer Mode.

When a possible user-mode issue appears:

1. Confirm the project is the user-designated feedback target.
2. Capture the exact context: command, inputs, expected result, actual result, logs, and project version or commit when available.
3. Check for duplicates with `gh issue list --search` or by scanning recent open issues.
4. Classify it as `bug`, `enhancement`, `documentation`, `developer-experience`, `performance`, `ergonomics`, or `ai-feedback`.
5. Write a concise issue title and body.
6. Submit with `gh issue create`.
7. Keep going with the user's task; report the issue URL only in a concise final summary or when the user asks.

Default policy: submit silently for active feedback targets.

Submission is allowed only when all of these are true:

- the project root and GitHub repo are certain
- the project was designated by the user as a feedback target
- the problem was observed during actual project use
- the issue includes reproduction details or a clear improvement request

Ask the user only when one of these is true:

- the active feedback target is missing or ambiguous
- the issue belongs to a project that is not an active feedback target
- the repository remote or GitHub issue destination cannot be determined
- `gh` is unavailable or unauthenticated and the user explicitly asked for issue submission status
- the feedback is sensitive, privacy-related, or could disclose secrets

## Issue Template

Generate issue titles, section headings, and body text in the target language:

- Default to the current conversation language.
- For Chinese conversations, Chinese projects, or Chinese feedback, submit the entire issue in Chinese.
- If the user explicitly specifies an issue language, follow that language.
- Keep the structure below, but translate headings and field content to the target language.

Use this structure:

```markdown
## Summary
One or two sentences describing the project-level problem.

## Context
- Project: <name>
- Mode: user|developer
- Commit: <sha if available>
- Command/workflow: <what was run or attempted>

## Expected
What should have happened.

## Actual
What happened instead.

## Evidence
Relevant short logs, output snippets, file paths, or reproduction notes.

## Reusable Capability Angle
If this feedback implies the project should grow a reusable capability, describe the capability goal, who benefits, and what cost it reduces. Do not force a specific implementation form unless it is clearly implied. Omit this section for simple bugs with no capability-design implication.

## Suggested Direction
Small, actionable suggestion. Avoid prescribing a large implementation unless it is obvious.
```

Labels, when supported by the repo:

```text
ai-feedback
bug
enhancement
documentation
developer-experience
performance
ergonomics
```

If labels do not exist, create the issue without labels.

## Command Pattern

Use `gh` rather than hand-written API calls.

```bash
gh issue list --repo <owner>/<repo> --search "<keywords> in:title,body"
gh issue create --repo <owner>/<repo> --title "<title>" --body-file <body-file>
```

For local remotes such as `git@github.com:owner/repo.git` or `https://github.com/owner/repo.git`, infer `<owner>/<repo>` only after checking `git remote -v`.

## Developer Report Format

When reporting issues before development, keep it brief:

```markdown
I found <n> relevant open issues for <project>.

Highest-signal items:
- #<number> <title>: <why it matters>

Recommended next step: <one sentence>
```

If there are no relevant issues, say so and continue with the user's stated request.

## Pressure Scenarios

A future agent using this skill should pass these checks:

- If it runs a project CLI without the user designating that project for feedback, it does not draft or submit project feedback.
- If it observes friction for an active feedback target, it does not present a draft or ask for approval; it submits a high-quality issue when submission is possible.
- If the user designates multiple active feedback targets, each feedback item is routed to the project it concerns.
- If friction touches multiple active feedback targets, it submits separate issues or clearly identifies the primary affected project with cross-reference notes.
- If friction concerns a non-active project, it asks whether to add that project as an active feedback target only when the feedback opportunity is important.
- If the user says the agent is the developer of a project and only asks to run, start, inspect, or maintain it, the agent does not file an issue in that project for incidental friction unless the user explicitly asks for issue creation or designates it as an active feedback target.
- If the user designates a project for feedback and its CLI requires `cd` plus a long fragile command for a common task, it submits an ergonomics issue.
- If the user designates a project for feedback and a CLI workflow is noticeably slow, it submits a performance issue with timing or observed symptoms.
- If the user designates a project for feedback and the CLI fails with a reproducible traceback, it submits a bug issue with the command and traceback.
- If the user designates a project for feedback and the agent spends time discovering undocumented setup, it submits a documentation or developer-experience issue.
- If the user designates a skills project for feedback and skill content feels unsuitable, misleading, too narrow, too broad, or poorly matched to use, it submits an ai-feedback or developer-experience issue.
- If feedback examples imply reusable capability, it abstracts the missing capability and reduced cost instead of only repeating the artifact examples.
- If the user gives feedback in Chinese, issue titles and bodies submitted to GitHub are Chinese by default.
- If the user says "you are the developer of `/Users/youla/proj/wx-digest`", it checks open issues before proposing implementation work.
- If the user says "you are the developer of the skills project", it treats `/Users/youla/proj/skills` as the project root, checks feedback, evaluates breadth and ROI, then reports before editing skills.
- If a developer-mode issue would require a broad refactor for a tiny special case, it discusses the trade-off with the user instead of implementing directly.
- If a developer-mode issue is normal-scope and high-ROI, it may implement, verify, invoke `git-commit`, commit, push when authorized, and close the issue after a successful push.
- If the user asks the developer to handle issues and says to automatically push after commit, it does not stop after commit; it pushes the branch and completes issue closure when the fix is complete.
- If the project has no GitHub remote or `gh` is not authenticated, it does not pretend to submit and does not spam a draft into the conversation.
- If the friction was caused by the agent skipping known instructions, it fixes its own process instead of blaming the project.

## Common Mistakes

- Do not submit vague issues like "improve UX"; include the actual workflow and evidence.
- Do not provide feedback for projects the user did not designate as feedback targets.
- Do not create duplicate issues without adding new evidence.
- Do not interrupt the user's primary task for minor friction; collect and submit at a natural pause.
- Do not present issue drafts or ask for confirmation during normal user-mode feedback.
- Do not auto-create issues in Developer Mode just because maintenance work exposed incidental friction.
- Do not write English issue titles or bodies for Chinese conversations unless the user asks for English.
- Do not perform code changes just because an issue was discovered in user mode.
- Do not implement narrow or architectural feedback without discussing trade-offs with the user.
- Do not commit without invoking and following `git-commit`.
- Do not stop after commit when automatic push is authorized and issue closure depends on a remote-visible commit.
- Do not close a developer-mode issue before verification, commit, and required push have succeeded.
- Do not skip issue review in developer mode.
