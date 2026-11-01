# OSTF 4.0 — Demo / Fixture Static Validation

> English: [`DEMO_STATIC_VALIDATION.md`](DEMO_STATIC_VALIDATION.md)

Validation date: 2026-11-01

## Scope

Public reference examples in this release package. Runtime playback is intentionally outside this validation boundary.

## Files checked

- Canonical examples: 5
- Golden Musical Demo Songs: 12
- Feature / boundary fixtures: 41
- Total public `.ostf`: **58**

## Results

- OSTF 4.0 JSON Schema: **58 / 58 PASS**
- Core ID/reference/channel/bus/MPE/voice/targetNote semantic checks: **58 / 58 PASS**
- Golden Demo strict structural/music-data checks: **12 / 12 PASS**
- 3.5 Compatibility View for Golden Demos: **12 / 12 PASS**

## Golden Demo strict checks

- contiguous Section coverage
- contiguous Chord timeline coverage
- Note/Event timeline bounds
- monophonic Lead/Vocal overlap detection
- targetNoteId event occurs inside target Note lifetime
- MPE same-member-channel overlap detection
- member-channel per-note expression has targetNoteId
- every Lyric Voice note aligned exactly once
- no unreferenced lyric units in vocal demos
- minimum Primary Melody density / pitch variety / rhythm variety
- exact full-arrangement duplicate detection across all 12 Demos

Result: **all checks PASS**.

## Runtime tests

Not performed by project decision:

- OSTF Player / Composer / Editor
- Ableton Live / Max for Live
- MIDI hardware / MPE hardware
- plugin instruments

These are implementation conformance tests, not format example-set validation.


## Publication Final documentation audit

- Total repository files: **78**
- Public `.ostf`: **58**
- Complete Manual: expanded and synchronized with the 24–40 bar Golden Demo Set
- Parameter Reference `$defs`: **91 / 91**
- Parameter names represented from Schema: **207 / 207**
- Stale pre-Golden statements used as current documentation: **0 detected**

The only remaining references to the old 16-bar demo set are historical descriptions explicitly stating that the old set was replaced.
