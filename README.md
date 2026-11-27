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

### Turn on auto-update (recommended)

Claude Code doesn't auto-update third-party plugins unless you ask it to, and a plugin can't turn this on by itself. It takes one step:

1. Run `/plugin` and open the **Marketplaces** tab.
2. Select **improve** and turn on **auto-update**.

You can set it in `~/.claude/settings.json` instead:

```json
{
  "extraKnownMarketplaces": {
    "improve": {
      "source": { "source": "github", "repo": "faizm10/improve" },
      "autoUpdate": true
    }
  }
}
```

With auto-update on, Claude Code checks for a new version a few minutes after the first message of each session. When it finds one, it shows `Plugin updated: improve`. Run `/reload-plugins` to use it right away, or it loads the next time you start Claude Code.

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

With [auto-update](#turn-on-auto-update-recommended) on, you don't need to do anything. Every change merged to `main` reaches you automatically.

To update by hand, run this from a terminal:

```bash
claude plugin marketplace update improve
claude plugin update improve@improve
```

Then restart Claude Code. See [CHANGELOG.md](CHANGELOG.md) for what changed.

## Troubleshooting

- **`/improve` doesn't appear:** restart Claude Code, then run `/plugin` to check the plugin is installed and enabled.
- **It shows up as `/improve:improve`:** that's the same command, prefixed with the plugin name. Use either one.
- **Two `/improve` commands:** you probably also copied the skill to `~/.claude/skills/improve`. Remove one of them.
- **No updates arriving:** check that auto-update is on for **improve** under `/plugin` → Marketplaces. Auto-update is also off if you've set `DISABLE_AUTOUPDATER=1`, `DISABLE_UPDATES=1` or `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1`, unless you also set `FORCE_AUTOUPDATE_PLUGINS=1`.

## Uninstall

From a terminal:

```bash
claude plugin uninstall improve@improve
claude plugin marketplace remove improve
```

## Install without the plugin system

A copied skill never updates, so use the plugin install above if you want updates. To copy it anyway, put the skill in your global skills folder:

```bash
git clone https://github.com/faizm10/improve /tmp/improve
mkdir -p ~/.claude/skills && cp -r /tmp/improve/skills/improve ~/.claude/skills/
```

## License

MIT
