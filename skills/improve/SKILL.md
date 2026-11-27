---
name: improve
description: Refine a rough prompt into a clear, actionable engineering prompt, show it for approval, then execute it. Use when the user types /improve <rough prompt>.
argument-hint: <rough prompt>
disable-model-invocation: true
---

# /improve: refine the prompt, get approval, then execute

The user's rough prompt:

<rough_prompt>
$ARGUMENTS
</rough_prompt>

If the rough prompt is empty, ask the user what they want to do and stop. Don't refine anything yet.

## Hard rule: nothing changes before approval

Until the user explicitly approves (step 4), you are **read-only**. Don't edit or create files, install dependencies, run builds, migrations, or tests that write output, commit, push, or call anything outward-facing. Allowed: reading files, searching with Glob/Grep/`git log`/`git status`/`ls`, and read-only commands that help you understand the codebase.

## 1. Understand the intent

- Work out what the user is trying to do, even through typos, fragments, or vague wording. Read it charitably and literally. Don't swap in a different goal.
- Size the task first. If it's a one-liner ("rename X to Y", "fix the typo in README"), skip deep inspection and keep the refined prompt short.
- For anything non-trivial, look at the codebase before you write the prompt, read-only: the files or modules involved, how similar things are already done, the stack and versions (package manifests, configs), project instructions (CLAUDE.md, AGENTS.md, CONTRIBUTING), and test and lint setup. Take only what you need. Don't survey the whole repo.
- Note missing requirements, constraints, edge cases, and real ambiguities.
- Settle ambiguities yourself with reasonable, disclosed assumptions. Ask a clarifying question only when the answer would change the objective and you can't infer it from the code or a sensible default. If you do need to ask, put it in the approval question (step 4). Don't add a separate round.

## 2. Write the refined prompt

Turn the input into a precise prompt you could execute as written. Use only the sections that earn their place. A simple request might be two sentences. A feature might use most of these:

- **Objective**: one or two sentences.
- **Context**: the relevant files, modules, existing patterns, and stack, with real paths you checked.
- **Requirements**: functional, then technical.
- **Approach**: the implementation path, following existing project conventions.
- **Constraints and non-goals**: what must not change, and what's out of scope.
- **Edge cases**
- **Validation**: how to test or check it, using the project's real test, lint, and typecheck commands.
- **Acceptance criteria**: checkable statements.
- **Deliverables**: files changed, a PR, a report, a design, and so on.

Rules:
- Keep the user's objective. Never quietly widen, narrow, or swap it. If you think the goal itself is off, say so under Assumptions or Key improvements, and still keep it as the objective.
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

Use exactly this structure:

**Original intent:** one or two sentences on what the user wants, in plain words.

**Refined prompt:**

```
<the complete refined prompt>
```

**Key improvements:** short bullets on what you clarified, added, or corrected, and why.

**Assumptions:** short bullets on each decision you made for the user. Write "None" if there aren't any.

## 4. Ask for approval

Use the AskUserQuestion tool with one question (header: "Approval") and these four options, in this order:

1. **Approve & execute**: run the refined prompt now.
2. **Request changes**: give feedback and I'll refine it again.
3. **Execute original**: skip the refinement and run your original prompt as written.
4. **Cancel**: stop without changing anything.

If AskUserQuestion isn't available, list the same four options in plain text and wait for a reply.

How to handle the answer:
- **Approve & execute**: go to step 5 with the refined prompt.
- **Request changes**, or free-text feedback through "Other": apply the feedback, then repeat steps 2–4 with the updated prompt. Keep going until the user approves, picks Execute original, or cancels. If the feedback is vague, make your best revision and point out what you changed.
- **Execute original**: go to step 5 with the original rough prompt as the task, read as written, with none of your added requirements.
- **Cancel**: confirm in one line that nothing was changed, and stop.

Treat only an explicit choice or reply as approval. Silence, or a question about the prompt, doesn't count.

## 5. Execute

- Make the approved prompt, refined or original, the active task in this session. The user shouldn't have to paste it again.
- Follow the project's normal working rules: CLAUDE.md and AGENTS.md instructions, git conventions, and confirmation for destructive or outward-facing actions. Approving the prompt doesn't authorize pushes, deploys, or deletes the prompt didn't name.
- If what you find during execution invalidates an assumption in a way that changes the objective, stop and check with the user. Don't adjust quietly.
- When done, report against the acceptance criteria, if there were any: what you did, what you verified and how, and anything left undone.
