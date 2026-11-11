## Why

Users reporting a problem cannot tell which version of the app they are running. Showing the version in a page footer lets them quote it in bug reports.

## What Changes

- Render a `<footer>` at the bottom of the main page showing "Version X.Y.Z".
- X.Y.Z is the `version` field of `package.json`, injected at build time (not hard-coded in a component).
- Minimal styling only so the footer is readable.
- Out of scope: git SHA, build date, further styling.

## Capabilities

### New Capabilities
- `version-footer`: A page footer that displays the app version taken from `package.json` at build time.

### Modified Capabilities
None.

## Impact

- `vite.config.ts`: read `package.json` version and expose it as a build-time constant.
- `src/`: type declaration for the constant, footer in `App.tsx`, small CSS addition.
- No new dependencies. `pnpm build` and `pnpm lint` must keep passing.
