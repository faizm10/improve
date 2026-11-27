# /improve

A Claude Code plugin that turns a rough request into a clear, actionable prompt, checks it with you, and runs it only after you approve.

You type something rushed:

```
/improve please improve hte ui of this
```

Claude reads your code without changing anything, works out what you meant, and replies with a short summary you can decide from at a glance:

```
You asked:     "please improve hte ui of this"
I understood:  A polish pass on the dashboard in this repo, inside its existing design system.
Check this:    "this" = the whole dashboard, not one page. Say which page if you meant one.

Refined prompt: objective, context from your repo, requirements, edge cases,
                how to test it, and acceptance criteria

What I added:   why each part is there
```

Then you choose:

| Option | What happens |
|---|---|
| **Approve & execute** | Claude runs the refined prompt in the same session |
| **Request changes** | Type what to change, or pick a quick tweak: smaller scope, different target, more detail, less detail. It refines again and shows what changed |
| **Execute original** | It skips the refinement and runs your request exactly as typed |
| **Cancel** | It stops. Nothing is changed |

When it's done, you get a short report: what changed, how each acceptance criterion was checked, and anything left out.

## Install

In Claude Code:

```
/plugin marketplace add faizm10/improve
/plugin install improve@improve
```

Restart Claude Code if the command doesn't show up right away.

## Usage

```
/improve <what you want done, rough is fine>
```

It works for features, debugging, refactors, UI/UX, architecture decisions, and research:

```
/improve add dark mode toggle to settings, match existing theme stuff
/improve fix the login bug where it sometimes redirects back to login after signin
/improve refactor the analytics fetchers theyre duplicated everywhere
/improve make the dashboard less cluttered on mobile
/improve should we move our cron jobs to a queue? pros and cons
/improve rename getUserData to fetchUserProfile
```

Type `/improve` on its own to get a few examples.

## Guarantees

- **Read-only until you approve.** While refining, it doesn't edit files, install packages, run builds, or commit.
- **Keeps your goal.** It clarifies and fills gaps, but it never quietly changes what you asked for. If it thinks the goal is off, it says so up front.
- **Shows its guesses first.** Anything it had to guess that would change the result is listed at the top, so you can correct it before anything happens.
- **Scales to the task.** A one-line rename gets a one-line prompt, not a spec.
- **Follows your project.** It uses the conventions, commands, and patterns your repo already has before suggesting anything new.
- **Approval covers only the prompt.** Pushes, deploys, and deletes the prompt didn't name still need your confirmation as usual.

## Update

From a terminal:

```bash
claude plugin marketplace update improve
claude plugin update improve@improve
```

Then restart Claude Code. See [CHANGELOG.md](CHANGELOG.md) for what changed.

## Troubleshooting

- **`/improve` doesn't appear:** restart Claude Code, then run `/plugin` to check the plugin is installed and enabled.
- **It shows up as `/improve:improve`:** that's the same command, prefixed with the plugin name. Use either one.
- **Two `/improve` commands:** you probably also copied the skill to `~/.claude/skills/improve`. Remove one of them.

## Uninstall

From a terminal:

```bash
claude plugin uninstall improve@improve
claude plugin marketplace remove improve
```

## Install without the plugin system

Copy the skill into your global skills folder:

```bash
git clone https://github.com/faizm10/improve /tmp/improve
mkdir -p ~/.claude/skills && cp -r /tmp/improve/skills/improve ~/.claude/skills/
```

## License

MIT
