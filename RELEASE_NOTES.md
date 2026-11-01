# OSTF 4.0.0 Release Notes

OSTF 4.0.0 adds a standard MIDI performance layer and explicit multi-melody / multi-voice relationships while preserving the OSTF 3.5 song and Note model.

## New Core areas

- MIDI Channel
- Logical MIDI Bus
- Control Change
- Channel Mode
- Program Change / Bank Select
- 14-bit Pitch Bend
- Channel Pressure
- Polyphonic Key Pressure
- MPE lower / upper zones
- same-tick MIDI event ordering
- multiple melody tracks
- lyric voices and voice-specific alignment

## Demo package

The final release contains:

- 5 canonical examples
- 12 independently composed musical demo songs
- 41 feature / boundary fixtures
- a Demo Song Guide
- a detailed Demo / Fixture Coverage Matrix
- static validation report

The musical demos were rebuilt after an internal review found the first demo set reused too much identical musical material.

## Compatibility Playback

A 4.0-aware Player may ignore unsupported optional 4.0 performance semantics during playback while continuing to use inherited Track / Clip / Note / Chord / Lyrics data.

Unsupported 4.0 data must not be silently discarded on save.

## Validation boundary

All public `.ostf` examples pass the 4.0 Schema and static semantic checks. The 12 musical demos also pass a 3.5-schema compatibility-view check after removal of 4.0-only fields.

Runtime DAW / M4L / hardware testing is intentionally outside the format release criterion.

## Golden Demo Set
The official demo songs were rebuilt as durable musical reference material: 12 independent 24–40 bar songs plus 41 technical fixtures. Strict structural, lyric-alignment, target-event, MPE-channel and 3.5 compatibility audits pass for all demos.
