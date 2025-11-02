# Relaynaut AI Agent Instructions

## Project Overview

Relaynaut is a Rust-first, low-latency overlay networking system with Vue 3 dashboard. It's a **monorepo** with Rust workspace + frontend, focused on p99 latency measurement, NAT traversal, and HFT-inspired data infrastructure.

**License:** Business Source License 1.1 (BUSL-1.1) — non-production use allowed; commercial use requires separate license.

## Architecture

### Crate Structure (Rust Workspace)

```
crates/
├── relaynaut-agent/       # Binary: performs network probes, p99 tests
├── relaynaut-controller/  # Binary: API server, coordination, reporting (Axum)
├── relaynaut-common/      # Library: shared structs, metrics, serialization
└── relaynaut-hftpack/     # Library: HFT tools (tick gen, replay, deterministic streams)
```

**Key insight:** `common` and `hftpack` are libraries shared by `agent` and `controller`. Agent runs on edge nodes; controller orchestrates and exposes REST API.

### Frontend Structure

```
web/
├── src/
│   ├── main.ts           # Entry: imports main.scss, PrimeVue, creates Vue app
│   ├── main.scss         # SCSS entry: imports style.css + custom SCSS
│   ├── style.css         # CSS reset base (imported by main.scss)
│   └── App.vue           # Root component (Phase 1: minimal "Relaynaut" heading)
├── vite.config.ts        # Vite config with @ alias, Tailwind, build settings
├── package.json          # Scripts: dev, build, lint, format, lint:fix, lint:ci
└── .oxlintrc.json        # Oxlint config (Vue + TS rules)
```

**Tech stack:** Vue 3 (script setup), TypeScript, Vite (using `rolldown-vite`), PrimeVue, Tailwind CSS v4, Sass, Lucide icons, TanStack Form, Zod, VueUse.

**Styling:** Use SCSS in `main.scss` for variables/nesting; `style.css` provides CSS reset base. Tailwind v4 plugin enabled in Vite config.

## Developer Workflows

### Rust Commands (from repo root)

```powershell
# Build entire workspace
cargo build --workspace

# Lint (strict Clippy with warnings as errors)
cargo lint  # alias defined in .cargo/config.toml

# Format check (CI-style)
cargo fmt --all -- --check

# Format fix
cargo fmt --all

# Run specific binary
cargo run -p relaynaut-agent
cargo run -p relaynaut-controller
```

**Linting standard:** Use `cargo lint` (not plain `cargo clippy`) — it runs `clippy --all-targets --all-features -- -D warnings` per `.cargo/config.toml`.

### Frontend Commands (from `web/`)

```powershell
# Dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Lint (oxlint, fails on error)
npm run lint

# Lint + auto-fix
npm run lint:fix

# Lint for CI (fails on warnings)
npm run lint:ci

# Format (Prettier)
npm run format
```

**Package manager:** npm (not pnpm or yarn). Dependencies installed via `npm install`.

**Linting:** Oxlint (not ESLint) configured in `.oxlintrc.json`. Rules enforce correctness/suspicious/style categories, Vue + TypeScript plugins. Test files allow `console`.

**Formatting:** Prettier (`.prettierrc.json` at repo root): single quotes, 2-space tabs, 100 char width, trailing commas ES5.

### Versioning & Releases

- Follow **Semantic Versioning** (semver.org).
- Update `CHANGELOG.md` (Keep a Changelog format) with Unreleased section.
- When cutting release: move Unreleased → versioned entry with date (YYYY-MM-DD).
- Current version: 0.1.0 (Phase 1 foundation).

## Code Conventions

### Rust

- Edition: 2024 (all crates use `edition = "2024"`).
- Release profile: thin LTO, 1 codegen unit, opt-level 3, no debug symbols (see root `Cargo.toml`).
- No custom lints or clippy rules beyond workspace default + `cargo lint` alias.
- Placeholder code present in `agent/main.rs` (simple loop demo) and `common/lib.rs` + `hftpack/lib.rs` (add function stubs).

### TypeScript/Vue

- **Strict mode enabled** in all `tsconfig` files (`strict: true`, `noUnusedLocals`, `noUnusedParameters`).
- Use `<script setup lang="ts">` in `.vue` files (Composition API).
- Import alias: `@/` maps to `web/src/` (configured in `vite.config.ts`).
- Prefer alphabetically sorted imports (oxlint `sort-imports` rule).
- No `console.log` in production code (warn level), allowed in tests.
- Component naming: single-word names allowed (oxlint `vue/multi-word-component-names: off`).

### File Structure Patterns

- **Rust:** One `main.rs` per binary crate, `lib.rs` for libraries. No modules yet (placeholder stage).
- **Frontend:** `main.ts` imports `main.scss` (which imports `style.css`). App uses PrimeVue globally (`app.use(PrimeVue)`).

## Key Integration Points

### Rust ↔ Frontend

- **Not yet implemented.** Controller will expose REST API (likely Axum); frontend will fetch metrics and render charts (Chart.js planned).
- Expect JSON serialization via `serde` in `common` crate.

### External Dependencies (Planned)

- **Backend:** Tokio (async runtime), Axum (HTTP), Prometheus exporter, SQLite (storage), MinIO (report PDFs).
- **Frontend:** Chart.js (metrics viz), PrimeVue components, Tailwind utility classes.

### Build/Deploy Notes

- Vite uses `rolldown-vite` (custom Vite fork) specified in `package.json` overrides.
- Frontend build outputs to `web/dist/`, ignored in `.gitignore`.
- Rust binaries compile to `target/debug/` or `target/release/`.
- **No CI workflows yet** — CI scaffolding in progress (lint/test checks will run `cargo lint` + `npm run lint:ci`).

## Common Pitfalls & Quirks

- **Don't use pnpm or yarn** — this project uses npm (confirmed by user).
- **Oxlint, not ESLint** — `.oxlintrc.json` in `web/`, CLI commands use `npx --yes oxlint`.
- **Sass enabled** — `main.scss` imports CSS, allows SCSS features (variables, nesting). Don't import `style.css` directly in `main.ts`.
- **Vite config sorted keys** — oxlint complains if object keys aren't alphabetically sorted (style rule). Auto-fix with `npm run lint:fix`.
- **Cargo.lock committed** — this is an application workspace, keep `Cargo.lock` in version control.
- **No tests yet** — placeholder test stubs in `common` and `hftpack`; no Vitest/Playwright config yet (planned).

## Phase 1 Status (Current)

- Workspace builds cleanly (`cargo build --workspace` succeeds).
- Frontend runs locally (`npm run dev` → http://localhost:5173).
- Minimal UI: centered "Relaynaut" heading + "Phase 1" subtext.
- Linting/formatting configured but codebase is mostly placeholder.
- No networking or metrics logic yet — focus is on toolchain foundation.

## When Making Changes

- **Rust:** Run `cargo lint` before committing. Use `cargo fmt --all`.
- **Frontend:** Run `npm run lint:fix` and `npm run format` before committing.
- **CHANGELOG:** Add entry to Unreleased section for notable changes (features, fixes, breaking changes).
- **Git:** Conventional Commits recommended (e.g., `feat:`, `fix:`, `chore:`).

## Questions or Gaps?

- If architectural decisions are unclear, check `README.md` for high-level goals.
- For Rust linting standards, see `docs/rust-notes.md`.
- For versioning/release process, see `CHANGELOG.md` header (full guide in `docs/semver-notes.md` when added).
- If you need HFT-specific context or networking design patterns, ask the user — those details are not yet documented.
