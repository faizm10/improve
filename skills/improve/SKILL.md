---
name: improve
description: Turn a rough request into a clear prompt, check it with you, then run it. Use when the user types /improve <rough request>.
argument-hint: <what you want done, rough is fine>
disable-model-invocation: true
---

# /improve: refine the prompt, check it with the user, then run it

The user's rough request:

<rough_prompt>
$ARGUMENTS
</rough_prompt>

## If the request is empty

Reply with this, adapted to the current repo if you can tell what it is, and stop:

> What do you want done? Rough is fine, even typos and half-sentences. For example:
> - `/improve fix the login redirect bug`
> - `/improve add a dark mode toggle to settings`
> - `/improve should we switch to a queue for background jobs?`
>
> I'll turn it into a clear prompt and show it to you before changing anything.

## Hard rule: nothing changes before approval

Until the user explicitly approves (step 4), you are **read-only**. Don't edit or create files, install dependencies, run builds, migrations, or tests that write output, commit, push, or call anything outward-facing. Allowed: reading files, searching with Glob/Grep/`git log`/`git status`/`ls`, and read-only commands that help you understand the codebase.

## 1. Understand the intent

- Work out what the user wants, even through typos, fragments, or vague wording. Read it charitably and literally. Don't swap in a different goal.
- Size the task first. If it's a one-liner ("rename X to Y", "fix the typo in README"), skip deep inspection and keep the refined prompt short.
- For anything non-trivial, look at the codebase before you write the prompt, read-only: the files or modules involved, how similar things are already done, the stack and versions (package manifests, configs), project instructions (CLAUDE.md, AGENTS.md, CONTRIBUTING), and test and lint setup. Take only what you need. Don't survey the whole repo.
- Note missing requirements, constraints, edge cases, and real ambiguities.
- Settle ambiguities yourself with reasonable, disclosed assumptions. Ask only when the answer would change the objective and you can't infer it from the code or a sensible default. Even then, don't add a separate round of questions: make your best guess, flag it under **Check this** (step 3), and let the approval step settle it.

## 2. Write the refined prompt

Turn the request into a precise prompt you could run as written. Use only the sections that earn their place:

- **Objective**: one or two sentences.
- **Context**: the relevant files, modules, existing patterns, and stack, with real paths you checked.
- **Requirements**: functional, then technical.
- **Approach**: the implementation path, following existing project conventions.
- **Constraints and non-goals**: what must not change, and what's out of scope.
- **Edge cases**
- **Validation**: how to test or check it, using the project's real test, lint, and typecheck commands.
- **Acceptance criteria**: checkable statements.
- **Deliverables**: files changed, a PR, a report, a design, and so on.

Keep it as short as the task allows. A rough guide: a one-line change gets 1–3 lines, a typical task about 10–25 lines, and only a large, multi-part task more than that.

Rules:
- Keep the user's objective. Never quietly widen, narrow, or swap it. If you think the goal itself is off, say so under **Check this**, and still keep it as the objective.
- Don't invent requirements, technologies, libraries, or architecture without a reason grounded in the request or the codebase. Prefer the project's existing conventions to new ones.
- Don't overengineer. Match the depth to how complex the task is.
- Adapt to the kind of task:
  - **Debugging**: symptoms, a repro, the suspected area, how to confirm the root cause, and a regression test.
  - **Feature**: behavior, where it plugs in, data flow, and tests.
  - **Refactor**: behavior stays the same, the scope boundary, and how equivalence is checked.
  - **UI/UX**: the user-facing behavior, states (empty, loading, error), responsive and accessibility needs, the existing design system, and visual checks.
  - **Architecture**: the decision to make, the options worth weighing, the tradeoffs, and what the output looks like.
  - **Research**: the question, sources, depth, and output format. Make clear it's informational and doesn't change code unless asked.

## 3. Show it to the user

Make it quick to read. The user should be able to decide from the first few lines and only read the full prompt if they want to. Use this structure, in this order:

**You asked:** the rough request, quoted as typed.

**I understood:** one or two plain sentences on what you'll do.

**Check this:** the one to three guesses most likely to be wrong, each with what you'll do if the user doesn't correct it. These are things that would change the result, like which page or file "this" means. Leave the section out if there are none.

**Refined prompt:**

```
<the complete refined prompt>
```

**What I added:** short bullets on what you clarified, added, or corrected, and why.

**Other assumptions:** smaller decisions you made for the user that aren't worth flagging above. Leave the section out if there are none.

On a revision (after Request changes), start with **What changed:**, a few bullets comparing it to the previous version, then show the full updated prompt. Don't repeat sections that didn't change.

## 4. Ask for approval

Use the AskUserQuestion tool with one question (header: "Approval"). The question must stand on its own, because the user may see only the dialog. Name the task in a few words, for example: "Run the refined prompt for the settings-page dark mode toggle?" Offer these four options, in this order:

1. **Approve & execute**: start now. The description says in a few words what will happen, such as "edit 3 files, then open a PR".
2. **Request changes**: refine it again with your feedback.
3. **Execute original**: skip the refinement and run your request exactly as typed.
4. **Cancel**: stop. Nothing is changed.

If AskUserQuestion isn't available, list the same four options in plain text. Say the user can reply with a number or just type their feedback, then wait.

How to handle the answer:
- **Approve & execute**: go to step 5 with the refined prompt.
- **Free-text reply** (typed through "Other", or a plain message): treat it as feedback. Apply it, then repeat steps 2–4.
- **Request changes with no feedback attached**: don't guess. Ask once with AskUserQuestion (header: "Changes", multiSelect: true): "What should change in the refined prompt?" Offer:
  - **Smaller scope**: do less and keep it tighter.
  - **Different target**: it's about a different file, page, or thing.
  - **More detail**: spell out requirements, edge cases, or tests.
  - **Less detail**: make it shorter and simpler.
  
  The user can also type specifics through "Other". If they pick "Different target" with no specifics, ask which one in plain text. Then apply the changes and repeat steps 2–4.
- **Execute original**: go to step 5 with the rough request as the task, read as written, with none of your added requirements.
- **Cancel**: say in one line that nothing was changed, and stop.

Repeat until the user approves, picks Execute original, or cancels. Treat only an explicit choice or reply as approval. Silence, or a question about the prompt, doesn't count. Answer the question and ask again.

## 5. Execute

- Make the approved prompt, refined or original, the active task in this session. The user shouldn't have to paste it again.
- Follow the project's normal working rules: CLAUDE.md and AGENTS.md instructions, git conventions, and confirmation for destructive or outward-facing actions. Approving the prompt doesn't authorize pushes, deploys, or deletes the prompt didn't name.
- On longer tasks, post a one-line update at each milestone so the user can follow along.
- If what you find invalidates an assumption in a way that changes the objective, stop and check with the user. Don't adjust quietly.
- Finish with a short report:
  - **Done:** what changed, with file paths or links.
  - **Checked:** each acceptance criterion and how you verified it. Mark any you couldn't verify plainly as not verified, and say why.
  - **Left out:** anything deferred or skipped, if there was any.
