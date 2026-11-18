# LibAnimate AGENTS.md

## Core Design

- **OnUpdate-based rendering** - single shared driver frame; avoids WoW's buggy Animation/AnimationGroup system.
- **Keyframe interpolation** - animations are keyframe lists with property values at progress points.
- **Per-segment easing** - named presets plus full cubic-bezier via Newton-Raphson solver.
- **`RegisterAnimation` API** - external addons can register custom animations.
- **State tables** - `lib.animations`, `lib.activeAnimations`, `lib.animationQueues`.
- Private functions stay local; public API methods attach to the `lib` table and take `self`.
- LuaLS annotations are used extensively for public types and APIs.
- Luacheck: ignore code 212 for `self` parameters.

## Supported WoW Versions

Retail (110207 / 120001), TBC Anniversary (20505), MoP Classic (50502 / 50503).

## Animation Definition Format

```lua
{
    type = "entrance",        -- "entrance", "exit", or "attention"
    defaultDuration = 0.6,    -- seconds
    defaultDistance = 300,     -- pixels
    keyframes = {
        { time = 0.0, translateX = 0, translateY = 1.0, scale = 0.7, alpha = 0.7 },
        { time = 0.8, translateY = 0, scale = 0.7, alpha = 0.7 },
        { time = 1.0, scale = 1.0, alpha = 1.0 },
    },
}
```

**Properties:** `translateX`, `translateY` (fraction of distance), `scale` (uniform), `alpha` (opacity).
**Defaults:** translateX=0, translateY=0, scale=1.0, alpha=1.0 (`PROPERTY_DEFAULTS` table).
**Coordinates:** WoW system (positive Y = up, positive X = right).

## Driver Error Handling

The shared OnUpdate driver must never crash. Two rules:

- Use `error("message", 2)` (level 2) for public-API input validation so the report points at the caller.
- Wrap user callbacks with `pcall()` + `geterrorhandler()()`. An error in one animation must not kill the driver loop or other active animations.

## Packaging

`.pkgmeta`:
- `package-as`: LibAnimate
- `enable-toc-creation`: yes
- External dependency: LibStub (from wowace repos)

## Pitfalls

- The OnUpdate driver runs every frame for every active animation - keep per-tick work allocation-free; do not create tables or closures inside the interpolation path.
- Frame recycled while an animation is active: callers must call the lib's stop API before releasing/repooling, or stale entries linger in `lib.activeAnimations`.
- Cubic-bezier easing solves `t` numerically; supply only well-formed control points (x in [0, 1]) or the Newton-Raphson loop misbehaves.

## AI Code of Conduct

Applies to every AI coding agent working in this repo. Human contributors: see [CONTRIBUTING.md](CONTRIBUTING.md).

- **Disclose.** State in the PR description which tool was used and how much of the change it produced.
- **Keep a human in the loop.** A human must review, understand, and be able to explain every line submitted. Never submit unreviewed agent output.
- **Verify.** Run `luacheck .` and test in-game before submitting (this repo has no unit tests). Never claim a check passed without running it.
- **Do not invent WoW API behaviour.** Verify against [warcraft.wiki.gg](https://warcraft.wiki.gg) and Blizzard's FrameXML source.
- **No spam.** No bulk or drive-by AI-generated issues or PRs. One scoped change per PR, linked to an issue.
- **Licensing.** Contributors are responsible for licensing. Do not paste code of unknown origin.
- **Stay in bounds.**
  - Follow this AGENTS.md.
  - Never push to `master`.
  - Never merge release-please PRs (`chore(master): release X.Y.Z`); the owner merges those.
  - Do not touch secrets or workflow permissions without explicit approval.
- **PR titles** must be conventional commits (`feat:`, `fix(scope):`, `chore:` ...). Squash merges use the PR title as the commit message, and release-please derives versions from it.

Maintainers may close undisclosed or unreviewed AI-assisted PRs without review.
