# Contributing to LibAnimate

Thanks for helping improve LibAnimate. This guide covers the basics; [AGENTS.md](AGENTS.md) has the full code style and architecture notes.

## Setup

There is no build step and no submodule setup; Lua files load directly in the WoW client. Linting uses `luacheck` with the repo's `.luacheckrc`.

## Workflow

- Open or find an issue first, and link it from your PR.
- Never work on `master`. Branch from it using `type/<issue>-short-desc`, for example `fix/12-queue-skip`.
- Keep to the code style in [AGENTS.md](AGENTS.md): 4-space indent, 120 character lines.
- Run `luacheck .` and fix all warnings. This repo has no unit tests, so also test in-game.
- Use a [conventional commit](https://www.conventionalcommits.org/) as the PR title (`feat:`, `fix(scope):`, `chore:` ...). Squash merges use the PR title as the commit message, and release-please derives versions from it.
- Fill in the pull request template.

## AI-assisted contributions

AI tools are welcome, but you are responsible for what you submit.

- **Disclose.** State in the PR description which tool you used and how much of the change it produced.
- **Review everything.** You must review, understand, and be able to explain every line you submit. Do not submit unreviewed agent output.
- **Verify.** Run `luacheck .` and test in-game before submitting. Never claim a check passed without running it.
- **Do not invent WoW API behaviour.** Verify against [warcraft.wiki.gg](https://warcraft.wiki.gg) and Blizzard's FrameXML source.
- **No spam.** No bulk or drive-by AI-generated issues or PRs. One scoped change per PR, linked to an issue.
- **Licensing.** You are responsible for licensing. Do not paste code of unknown origin.
- **Agents.** If an agent works on your behalf, it must follow [AGENTS.md](AGENTS.md): it never pushes to `master`, never merges release-please PRs (`chore(master): release X.Y.Z`; the owner merges those), and does not touch secrets or workflow permissions without explicit approval.

Maintainers may close undisclosed or unreviewed AI-assisted PRs without review.
