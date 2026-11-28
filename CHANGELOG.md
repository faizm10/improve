# Changelog

From here on, each change merged to `main` is a release. Claude Code tracks the plugin by its git commit, so there's no version number to bump.

## 2026-11-27: Auto-updates

- **Updates reach you automatically.** The plugin no longer pins a version number, so Claude Code treats each new commit on `main` as an update. Before, it ignored new commits until the version string changed.
- **README:** how to turn on auto-update, from `/plugin` → Marketplaces or in `settings.json`, and what to check if updates don't arrive.

## 1.1.0

- **Request changes asks what to change.** Picking it with no feedback no longer leaves Claude guessing. You get quick tweaks to pick from (smaller scope, different target, more detail, less detail), or you can type your own.
- **Guesses come first.** A new **Check this** section at the top lists the assumptions most likely to be wrong, such as which page "this" means.
- **Faster to read.** The reply opens with what you asked and what Claude understood. The full refined prompt comes after.
- **Revisions show what changed**, so you don't have to re-read the whole prompt.
- **A clearer approval question.** It names the task and says what approving will do, such as "edit 3 files, then open a PR".
- **A short final report**: what changed, how each acceptance criterion was checked, and anything left out.
- **Examples when you type `/improve` on its own.**
- **README:** an example reply, plus sections on updating, troubleshooting and uninstalling.

## 1.0.0

- First release.
