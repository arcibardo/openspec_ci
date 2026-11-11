## 1. Build-time version injection

- [ ] 1.1 In `vite.config.ts`, read `version` from `package.json` and expose it via `define` as `__APP_VERSION__` (covers: version resolved at build time; version bump and rebuild)
- [ ] 1.2 Add `src/globals.d.ts` declaring `__APP_VERSION__` as a string (covers: build and lint succeed)

## 2. Footer

- [ ] 2.1 Add a `<footer>` at the end of `App` in `src/App.tsx` rendering `Version {__APP_VERSION__}` (covers: footer on main page; footer shows package version; no hard-coded version)
- [ ] 2.2 Add minimal CSS in `src/App.css` so the footer is readable in light and dark schemes (covers: readable footer)

## 3. Verification

- [ ] 3.1 Run `pnpm build` and `pnpm lint`; both must succeed (covers: build and lint)
- [ ] 3.2 Confirm built output contains "Version 0.0.0"; bump version temporarily, rebuild, confirm text changes, then revert (covers: version bump and rebuild, current version displayed)
