# Changelog

All notable changes to the Fruit Engine addon. Format based on
[Keep a Changelog](https://keepachangelog.com/), versioning
[semver](https://semver.org/).

Newest first. For the current draft (unreleased work), see
[current.md](current.md).

---

## [1.5.0] — 2026-04-29

Live colour editing + per-banana scope hardening + bundled ML
auto-trace. Colour sliders now repaint the active banana in
~30 ms (was ~3 s mesh rebuild) and Random Generation adds bananas
instead of overwriting. Full detail → [v1.5.0.md](v1.5.0.md).

### Added
- Live recolour: colour swatches (Outer / Inner / Tip / Membrane /
  Pulp / Warm) + 🎨 Color Style palette update the active banana
  instantly via vertex-attribute foreach_set + BSDF default_value.
- Bundled `u2netp` ML auto-trace (~4.5 MB inside the addon, no
  external download).
- Random Generation = "+1 banana": auto-pins existing default
  before each click so multi-banana scenes build up.
- `tools/test_regressions.py` — 14 automated MCP-driven regression
  tests for the colour live + per-banana scope path.

### Changed
- Selected Banana panel: 3 buttons → 1. Removed `Load to Panel`
  (msgbus auto-sync covers it) and `Update Snapshot (no rebake)`
  (rare sculpt workflow). Renamed `Apply Panel` to
  `Apply Mesh Changes` to reflect colour-live semantics.
- Color picker rows render unconditionally (no longer gated on
  the advanced + override_raw toggles).
- Default ML auto-trace strategy is `REMBG` (u2netp) instead of
  `GRADIENT_ONLY`.

### Fixed
- Color slider drag on a pinned banana modified the wrong banana
  (target_prefix wasn't captured at slider-callback time).
- Color Style dropdown spawned a new default banana every click;
  now scoped to the active.
- Material lookup for PeelInnerMat / MembraneMat / PulpMat now
  tolerates Blender's `.NNN` auto-suffix (Pin always triggers it).
- Strip 4-12 amounts dropped from snapshot on 5-strip bananas
  because the registry only declared strip 1-3.
- Transform reset on every in-place rebuild — captured + restored
  in `op_generate_v2`.
- `_BATCH_LOADING` flag in `randomize_stylized` wasn't in
  try/finally — a mid-batch raise would have left every property
  callback silent for the rest of the session.

---

## [1.4.2] — 2026-04-25

Generation-time optimization round. LOW Realistic Generate
**6.08 s → ~2.05 s (≈3× faster)**, no behavioural drift. Higher
pack/density benefits proportionally (pack=3.0 ~20 s → ~6 s).
Full detail → [releases/v1.4.2.md](releases/v1.4.2.md).

### Changed
- 9 perf passes across `tube_distribution`, `tube_cluster`,
  `peel_curled/bake`, `peel_volume/build`, `teardrops_ovule`,
  `common/membrane`, `banana/honey_halo` — all numpy-vectorised
  bulk ops replacing per-element Python loops.

### Added
- `primitives/common/_profile.py` — nested stage timers + report
  dump (gated by `meta.profile_generate` or `KOO_FRUIT_PROFILE=1`).

### Fixed
- Realistic↔Stylized round-trip dispatch (workflow_mode tab,
  not marker presence).
- Strip-mode UV reads `fe_rest_co` so the strip stays aligned
  after the peel curl bake.

---

## [1.4.1] — 2026-04-24

UI shape/color expansion + `op_stylized` package modularization
(925-line file → package). Profiler scaffolding landed.

---

## [1.4.0] — 2026-04-24

Pre-release for Superhive submission. Marker-driven Realistic
pipeline, Pin Banana operator, tabbed N-panel, Save as Default,
manual + screenshots. First package shipped to Adrian for closed
testing.

---

## [1.3.0] — 2026-04-18

Extension format conversion + GPL compliance. Driven by Adrian's
(Superhive reviewer) direct request. `sangkoo_fruit_engine` is now a
proper Blender Extension (4.2+) with manifest, LICENSE, and per-file
SPDX headers. Full detail → [releases/v1.3.0.md](releases/v1.3.0.md).

### Added
- `blender_manifest.toml` (Extension format metadata)
- `LICENSE` (GPL-3.0 full text)
- SPDX headers on every `.py` (60 files)
- `tools/_add_spdx_headers.py` (idempotent bulk-add)
- `FE_DEV_LOG_PATH` env var for debug log override

### Changed
- `__init__.py` — `bl_info` removed (manifest is source-of-truth)
- `debug_log._default_log_path()` — 4-layer path resolution
  (env override → extension user dir → classic user_resource → tempdir)

### Fixed
- GPL compliance gap (no LICENSE, no headers)
- Extension compliance gap (debug_log wrote under own folder)

### Known carry-over
- BUG-002, BUG-004, BUG-005, BUG-011, BUG-012 (all low severity)

---

## [1.2.0] — 2026-04-18

Phase 1.8 release. Root-cause fix for interior organ poke-through at
thick peel (BUG-001). Introduces the duo-anchor architecture
(`flesh_radius(u)` for flesh-dwellers, `peel_scale()` for
peel-dwellers). Ships alongside `docs/` hygiene tree and MCP debug
helper. Full detail → [releases/v1.2.0.md](releases/v1.2.0.md).

### Added
- `BundleCluster.flesh_radius(u)` + `peel_scale()` + central reference
  constants (`REF_FLESH_RADIUS`, `REF_PEEL_THICKNESS`, `REF_OUTER_RADIUS`)
- `peel.thickness` in Randomize operator
- `tools/fe_mcp.py` MCP debug client
- `addon/fruit_engine/debug_log.py` session log API
- `docs/{bugs,debug,versions}/` tree

### Changed
- 5 interior primitives migrated to duo-anchor (flesh_s / peel_s)

### Removed
- `_body_clamp()` + magic `0.0003` safety in honey/teardrops (redundant
  after flesh-scaling)
- Unused `_REF_RADIUS = 0.020` breadcrumbs in 5 primitive files

### Fixed
- BUG-001 (interior poke-through)
- BUG-003 (photo-marker tip_radius_frac dropped)
- BUG-009 (randomize peel.thickness missing)
- BUG-010 (r_frac semantics undocumented)

### Known issues (carry-over)
- BUG-002, BUG-004, BUG-005, BUG-011, BUG-012 (all low severity)

---

## [1.1.1] — 2026-04-18 (pre-Phase-1.8 snapshot)

Baseline. Phase 1.0 through 1.7 were bundled under this version during
rapid development; release granularity improves starting v1.2.0.
See [releases/v1.1.1.md](releases/v1.1.1.md).

---

## History prior to 1.1.1

Reconstructed from git log. Not exhaustive; from here forward each
release gets its own entry.

### Phase 1 highlights (v1.0.0 → v1.1.1, 2026-04-16 → 2026-04-18)

- Phase 1.1 — 🎲 Randomize operator (Toy tier hero button)
- Phase 1.2 — Built-in 8-cultivar preset system (cavendish, gros_michel,
  plantain, lady_finger, red_banana, blue_java, burro, manzano)
- Phase 1.3 — 📸 Photo → Shape extraction (marker-based role system)
- Phase 1.4 — 📸 Photo texture wrap UI
- Phase 1.5 — Toy / Advanced UI mode toggle
- Phase 1.6 — 3-tier build scripts (Free / Toy / Pro)
- Phase 1.7 — Superhive + Gumroad submission asset draft

### v2 bundle cluster branch (2026-04-17+)

- `BundleCluster` structural spine replaces v1 ad-hoc geometry
- No-singularity refactor (tip_radius_frac floor)
- Peel curl rework (per-strip, per-fiber cling, progressive tear)
- 11 primitives ported to v2 Primitive ABC
