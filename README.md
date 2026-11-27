# /improve

A Claude Code plugin that turns a rough prompt into a clear, actionable one, shows it to you, and runs it only after you approve.

You type something rushed:

```
/improve fix the login bug where it sometimes redirects back to login after signin
```

Claude reads the codebase without changing anything, works out what you meant, and shows you:

- **Original intent**: what it thinks you want
- **Refined prompt**: a precise version, ready to run, with the objective, the context from your repo, the requirements, edge cases, how to validate it, and acceptance criteria
- **Key improvements**: what it clarified or added
- **Assumptions**: decisions it made for you, so you can catch wrong guesses

Then you pick one:

| Option | What happens |
|---|---|
| **Approve & execute** | Claude runs the refined prompt in the same session |
| **Request changes** | You give feedback, it refines again, and repeats until you're happy |
| **Execute original** | It skips the refinement and runs your prompt as written |
| **Cancel** | It stops. Nothing is changed |

## Install

In Claude Code:

```
/plugin marketplace add faizm10/improve
/plugin install improve@improve
```

Restart Claude Code if the command doesn't show up right away.

## Usage

```
/improve <rough prompt>
```

It works for features, debugging, refactors, UI/UX, architecture decisions, and research:

```
/improve add dark mode toggle to settings, match existing theme stuff
/improve refactor the analytics fetchers theyre duplicated everywhere
/improve make the dashboard less cluttered on mobile
/improve should we move our cron jobs to a queue? pros and cons
/improve rename getUserData to fetchUserProfile
```

## Guarantees

- **Read-only until you approve.** While refining, it doesn't edit files, install packages, run builds, or commit.
- **Keeps your goal.** It clarifies and fills gaps, but it never quietly changes what you asked for. If it thinks the goal is off, it says so.
- **Scales to the task.** A one-line rename gets a one-line prompt, not a spec.
- **Follows your project.** It looks for the conventions, commands, and patterns your repo already uses before suggesting anything new.
- **Asks only when needed.** It makes reasonable assumptions and lists them, and asks only when the answer would change the objective.
- **Approval covers only the prompt.** Pushes, deploys, and deletes the prompt didn't name still need your confirmation as usual.

## Install without the plugin system

Copy the skill into your global skills folder:

```bash
git clone https://github.com/faizm10/improve /tmp/improve
mkdir -p ~/.claude/skills && cp -r /tmp/improve/skills/improve ~/.claude/skills/
```

## License

MIT
