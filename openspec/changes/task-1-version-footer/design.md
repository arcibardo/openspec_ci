## Context

Vite + React + TypeScript app (`src/App.tsx`, `vite.config.ts`). `package.json` version is `0.0.0`.

## Goals / Non-Goals

**Goals:** show the package version in a footer, resolved at build time.
**Non-Goals:** git SHA, build date, rich styling.

## Decisions

- Use Vite's `define` in `vite.config.ts` to inject `__APP_VERSION__`, read from `package.json` (via `readFileSync` + `JSON.parse`, avoiding JSON-import config changes). Alternative considered: `import pkg from '../package.json'` in a component; rejected because it bundles the whole manifest into client code and is less explicit about build-time substitution.
- Declare the constant in `src/globals.d.ts` (`declare const __APP_VERSION__: string`) so `tsc -b` and eslint pass.
- Add the `<footer>` inside the `App` fragment after the last section; add a few lines of CSS in `App.css`.

## Risks / Trade-offs

- `vite dev` reads the version at server start; a version change needs a restart. Acceptable, since the requirement is rebuild.
- `vite.config.ts` is under `tsconfig.node.json`; `node:fs` use needs `@types/node` (already a devDependency).
