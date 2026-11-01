# OSTF 4.0 — Parameter Reference Coverage Audit

> English edition. Japanese: [`PARAMETER_REFERENCE_COVERAGE.ja.md`](PARAMETER_REFERENCE_COVERAGE.ja.md)

## Result

- Recursive Schema `$defs`: **156 / 156 covered**
- Top-level `$defs`: **91 / 91 covered**
- Nested `$defs`: **65 / 65 covered**
- Unique property names across the full recursive schema: **207 / 207 covered**

The English [`PARAMETER_REFERENCE.md`](PARAMETER_REFERENCE.md) is generated recursively from the release schema, including nested legacy/backward-compatibility definitions. Structural conditional logic remains authoritative in the JSON Schema; semantic rules remain authoritative in [`FORMAT_SPECIFICATION.md`](FORMAT_SPECIFICATION.md).

Result: **PASS**.
