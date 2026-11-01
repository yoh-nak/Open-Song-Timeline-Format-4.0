# OSTF 4.0.0 — Release Readiness

**Decision: Release GO. OSTF 4.0.0 is ready for GitHub publication as a format specification.**

Date: 2026-11-01.

## Package status

- 12 independently composed Golden Musical Demo Songs
- 41 Feature / Boundary Fixtures
- 5 Canonical Examples
- 58 public `.ostf` files total
- English + Japanese documentation
- JSON Schema and semantic specification
- complete parameter reference and coverage audit

## Static validation

- OSTF 4.0 Schema: 58/58 public `.ostf` PASS
- Core semantic checks: 58/58 PASS
- Golden Demo strict audit: 12/12 PASS
- Golden Demo 3.5 Compatibility Views: 12/12 PASS

## Runtime boundary

Per project decision, runtime tests in OSTF Player, Ableton Live/Max for Live, MIDI hardware, MPE hardware, and plug-in instruments are not format-release requirements. Those belong to implementation conformance testing.

## Decision

The previous demo-quality hold has been resolved. The public reference set demonstrates all OSTF 4.0-added Core properties, while technical boundary values remain in dedicated fixtures. **Release GO.**
