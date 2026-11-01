# Changelog

## 4.0.0 — 2026-11-01

### Added

- MIDI Channel-aware performance data
- Logical MIDI Buses
- Control Change and Channel Mode events
- Program Change and Bank Select via CC0/32
- 14-bit Pitch Bend
- Channel Pressure and Polyphonic Key Pressure
- MPE Lower / Upper Zone declarations
- `targetNoteId` semantic associations
- deterministic same-tick ordering
- multiple melody-track declarations
- explicit Lyric Voice relationships
- voice-specific lyric alignment
- Compatibility Playback rules
- 12 independent musical demo songs
- 41 feature / boundary fixtures
- Demo / Fixture Coverage Matrix

### Demo remediation before release

- Removed the initial repeated-base-song demo set
- Rebuilt MPE demos as genuinely polyphonic per-note-expression examples
- Replaced fixed chromatic vocal harmony with scale-aware harmony
- Added full voice-specific lyric alignments
- Added separate-document Call & Response example
- Added mixed-meter / key-change / drum-kit / fixed-trigger song
- Added 40-bar Complete Showcase
- Added 3.5 compatibility-view static checks for all musical demos

### Preserved from 3.5

- Existing Note semantics
- song structure / harmony / lyrics
- Sound Slots / Drum Kits
- note state / probability / release velocity / velocity deviation
- fixed MIDI trigger semantics
- generation, phrase, presentation, localization, provenance and merge-related Core structures

### Golden Demo Set refinement
- Rebuilt all 12 musical demos as long-term reference songs.
- Expanded song forms to 24–40 bars with richer melody, rhythm and arrangement.
- Rebuilt vocal lyric alignment, MPE expression, multiChannel layers and mixed-meter material.
- Added strict `DEMO_QUALITY_AUDIT.ja.md`.
