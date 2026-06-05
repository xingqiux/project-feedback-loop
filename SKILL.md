---
name: project-feedback-loop
description: Use when the user explicitly designates a local development project for agent feedback, says the agent is a project user or developer, asks to evaluate feedback/issues for a project, or asks to maintain a project from GitHub issues.
---

# Project Feedback Loop

## Overview

This skill turns user-authorized agent experience with a project into useful feedback and GitHub issues. It is a high-priority operating loop for local personal or second-development projects that have code, CLIs, scripts, docs, or project-specific skills.

## Activation Priority

Treat this skill like a process skill: load it early when it might apply.

Use it when any of these are true:

- The user says they are a user of a project, such as "you are the user of `/Users/youla/proj/wx-digest`".
- The user says they are a developer of a project, such as "you are the developer of `/Users/youla/proj/wx-digest`".
- The user explicitly names a project as the feedback target.
- The user asks to inspect, triage, or implement project feedback or GitHub issues.
- The current task is inside a clearly identified project root under `/Users/youla/proj/`.
- The current task is creating, editing, installing, symlinking, validating, or using skills under `/Users/youla/proj/skills` or `/Users/youla/.agents/skills`.

Do not use it for one-off files, generic web research, unrelated system setup, or projects whose root and repository cannot be identified.

Critical gate: feedback belongs only to a user-designated project. If the user has not named or approved a feedback target, do not draft, submit, or accumulate feedback for any project. Continue the user's task normally.

## Project Identity

Before feedback or developer triage, establish the project root.

Preferred signals:

1. explicit path in the user's message
2. current working directory inside a git repository
3. a local project with code plus docs, CLI, scripts, or skills

Confirm the repository metadata with:

```bash
git -C <project-root> rev-parse --show-toplevel
git -C <project-root> remote -v
```

If the root is uncertain, ask one concise question for the project path. If the root is certain but there is no GitHub remote, keep a local issue draft instead of trying to submit.

## Skills Project

`/Users/youla/proj/skills` can itself be a project, but only when the user designates it as the feedback or maintenance target. Treat it as the local Skills Project when the user asks to author, edit, validate, install, symlink, use, or maintain personal skills as a project.

For the Skills Project:

- User mode means the agent is using a skill as a consumer and should record user-authorized friction such as unclear triggers, missing workflow steps, weak validation guidance, bad default prompts, broken bundled scripts, or symlink/install confusion.
- Developer mode means the agent is maintaining skills and should first inspect feedback issues for the skill being changed, evaluate their breadth and ROI, then report relevant user-mode or agent-discovered needs before editing.
- If `/Users/youla/proj/skills` does not have a GitHub remote, keep issue drafts locally in the response rather than pretending live issue submission happened.
- A skill can feedback on itself. For `project-feedback-loop`, use this carefully: only file self-feedback when its own workflow caused a concrete problem or missed a project-feedback opportunity.

## Mode Selection

There are two modes.

### User Mode

Use when the user says the agent is a project user, or explicitly asks the agent to use a project and provide feedback.

The agent should:

1. Run the requested project workflow normally.
2. Notice real friction from the user's point of view.
3. Explore feedback across ergonomics, access, performance, docs, behavior, and project-specific skills.
4. Distinguish signal from noise.
5. Draft or submit GitHub issues for project-level improvements only for the designated feedback project.

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

Not issue-worthy by default:

- temporary network failures
- one-off user preference changes
- mistakes caused by the agent ignoring existing instructions
- generic complaints without a project-specific action
- problems already covered by an open issue unless there is useful new evidence
- friction from a project the user did not designate for feedback

### Developer Mode

Use when the user says the agent is a project developer, asks to implement or maintain the project, or asks to check user or AI-generated feedback.

Before development work:

1. Inspect open GitHub issues for the project.
2. Separate user-requested needs from agent-discovered needs.
3. Explore the feedback in detail: reproduce if possible, inspect affected code/docs/CLI/skills, and understand who benefits.
4. Evaluate whether the request is too narrow, over-specialized, or likely to bias the project toward a small scenario.
5. Report relevant issues, trade-offs, and the recommended next step.
6. For normal, high-ROI improvements, the agent may proceed autonomously through implementation, verification, and commit.

Developer evaluation questions:

- Is this feedback a real project-level problem or a one-user edge case?
- Does the fix generalize across normal users and workflows?
- Would the proposed change require a broad refactor for a tiny special case?
- Would it make the project lean toward a narrow scenario at the expense of its main purpose?
- Is there a smaller change, clearer doc, better default, shortcut, or helper command that gets most of the value?
- What is the expected ROI compared with implementation and maintenance cost?

Communicate with the user before implementing when the feedback is narrow, ambiguous, architectural, likely to cause a broad refactor, or likely to shift the project toward a special-case workflow.

For high-ROI, normal-scope improvements:

1. Inspect the issue and relevant code/docs.
2. Implement the focused change.
3. Verify with the project's tests or the original workflow.
4. Commit the change.

Commit rule: before staging or committing, invoke and follow the `git-commit` skill. Do not hand-roll the commit process.

Useful commands:

```bash
gh issue list --repo <owner>/<repo> --limit 30
gh issue view <number> --repo <owner>/<repo>
```

If `gh` is unavailable or unauthenticated, use the GitHub web URL from the remote when possible, or ask the user to authenticate before live issue operations.

## Feedback Workflow

When a possible issue appears:

1. Confirm the project is the user-designated feedback target.
2. Capture the exact context: command, inputs, expected result, actual result, logs, and project version or commit when available.
3. Check for duplicates with `gh issue list --search` or by scanning recent open issues.
4. Classify it as `bug`, `enhancement`, `documentation`, `developer-experience`, `performance`, `ergonomics`, or `ai-feedback`.
5. Write a concise issue title and body.
6. Ask for confirmation before submitting unless the user explicitly allowed direct issue creation for this project and the failure is clear and reproducible.
7. Submit with `gh issue create`, then report the issue URL.

Default policy: prepare a draft and ask the user before creating an issue.

Direct submission is allowed only when all of these are true:

- the project root and GitHub repo are certain
- the project was designated by the user as a feedback target
- the problem was observed during actual project use
- the issue includes reproduction details or a clear improvement request
- the user has previously allowed direct issue creation for this project or this session

## Issue Template

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
- If the user designates a project for feedback and its CLI requires `cd` plus a long fragile command for a common task, it drafts an ergonomics issue.
- If the user designates a project for feedback and a CLI workflow is noticeably slow, it drafts a performance issue with timing or observed symptoms.
- If the user designates a project for feedback and the CLI fails with a reproducible traceback, it drafts a bug issue with the command and traceback.
- If the user designates a project for feedback and the agent spends time discovering undocumented setup, it drafts a documentation or developer-experience issue.
- If the user designates a skills project for feedback and skill content feels unsuitable, misleading, too narrow, too broad, or poorly matched to use, it drafts an ai-feedback or developer-experience issue.
- If the user says "you are the developer of `/Users/youla/proj/wx-digest`", it checks open issues before proposing implementation work.
- If the user says "you are the developer of the skills project", it treats `/Users/youla/proj/skills` as the project root, checks feedback, evaluates breadth and ROI, then reports before editing skills.
- If a developer-mode issue would require a broad refactor for a tiny special case, it discusses the trade-off with the user instead of implementing directly.
- If a developer-mode issue is normal-scope and high-ROI, it may implement, verify, and then invoke `git-commit` before committing.
- If the project has no GitHub remote or `gh` is not authenticated, it does not pretend to submit; it provides a ready issue draft.
- If the friction was caused by the agent skipping known instructions, it fixes its own process instead of blaming the project.

## Common Mistakes

- Do not submit vague issues like "improve UX"; include the actual workflow and evidence.
- Do not provide feedback for projects the user did not designate as feedback targets.
- Do not create duplicate issues without adding new evidence.
- Do not interrupt the user's primary task for minor friction; collect and submit at a natural pause.
- Do not perform code changes just because an issue was discovered in user mode.
- Do not implement narrow or architectural feedback without discussing trade-offs with the user.
- Do not commit without invoking and following `git-commit`.
- Do not skip issue review in developer mode.
