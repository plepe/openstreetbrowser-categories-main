# Copilot instructions for openstreetbrowser-categories-main

This file gives succinct, actionable guidance for AI coding agents working in this package.

- **Big picture**: this package defines map category descriptors as YAML files. Each category YAML contains metadata for the UI (localized `name`), an Overpass-style `query`, a `feature` Jinja template (controls title/description/marker/style) and a `const` section mapping tag-derived keys (e.g. `piste:type=ice_skate`) to rendering details.

- **Key files/locations**:
  - Category examples: `ice_skating.yaml` (root of this package).
  - Translations helper: `lang/README.md` (contains rules for copying translation entries into categories).

- **YAML + Jinja pattern**:
  - Templates use Jinja-like expressions inside YAML block scalars (`feature.pre`, `feature.title`, `feature.body`, etc.).
  - Common template helpers used in files: `attribute(tags, '...')`, `localizedTag(tags, '...')`, `tagTrans(key,value)`, `keyTrans(...)`, and `markerPointer()` / `markerCircle()` helpers.
  - Be careful with quoting and nested braces; preserve existing whitespace and indentation when editing.

- **Query conventions**:
  - `type` is frequently `overpass` and `query` holds Overpass QL blocks. Look at `ice_skating.yaml` for examples combining `way[...]`, `node[...]`, and boolean filters.
  - Category queries intentionally avoid overlap with other category packages (see inline comments). When updating queries, maintain such exclusions and add TODO comments for unresolved tagging patterns.

- **Rendering conventions**:
  - `const` keys use `tag=value` strings (e.g. `piste:type=ice_skate`) to map to `symbol`, `style`, `sign`, and `priority`.
  - `feature.pre` usually resolves which `key`/`value` pair applies and sets `constIndex = (key ~ "=" ~ value)` to lookup `const[constIndex]`.
  - `markerSign` may include raw HTML (icons). `markerSymbol`/`listMarkerSymbol` choose display helpers based on `item.symbol`.
  - Respect `restricted_access_value` logic: values like `no`, `private`, `discouraged` should gray-out markers (`#7F7F7F`) and change behavior in templates.

- **When adding a new category YAML**:
  - Copy an existing file (e.g. `ice_skating.yaml`) as the minimal scaffold: `name`, `type`, `query`, `feature`, `const`.
  - Implement `feature.pre` to deterministically set `key` and `value` then lookup `const` via the `constIndex` pattern.
  - Keep UI strings via `localizedTag` / `tagTrans` rather than hardcoding text.

- **Project-specific patterns for AI**:
  - Prefer read-only edits first: update `const` styles or `feature` templates without changing query logic unless necessary.
  - Preserve TODO comments in queries — they encode tagging ambiguity and cross-package concerns.
  - Use existing helper names (`markerPointer`, `markerCircle`, `tagTrans`) rather than inventing new ones.

- **Testing / validation (discoverable)**:
  - There are no unit tests in this package. Validate changes by comparing rendered template outputs in a running OpenStreetBrowser instance (manual step outside this package).
  - For syntax safety, run a YAML linter and ensure Jinja blocks remain properly indented and quoted.

- **Examples to inspect**:
  - `ice_skating.yaml`: demonstrates query composition, `feature.pre` logic, `const` mappings, localized `name`, and marker styling.
  - `lang/README.md`: describes how translations in `lang/` are applied to categories.

If anything here is unclear or you want the instructions tailored to a different contributor workflow, tell me which parts to expand or examples to add.
