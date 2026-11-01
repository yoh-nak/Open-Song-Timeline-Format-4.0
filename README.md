# Open Song Timeline Format (OSTF) 4.0.0

Open Song Timeline Format (OSTF) is an open, DAW-independent JSON interchange format for structured songs.

Release status: **4.0.0 — Release GO (2026-11-01)**

OSTF 4.0 keeps the OSTF 3.5 song/note model and adds a standard MIDI performance layer plus explicit multi-melody and multi-vocal relationships.

## Documentation languages

English documentation is the default public navigation. Complete Japanese editions are provided alongside the English files using the `.ja.md` suffix. See [`docs/README.md`](docs/README.md).

## Golden Reference Demo Set

OSTF 4.0 includes 12 independently composed Golden Musical Demo Songs, 41 technical Feature Fixtures, and 5 Canonical Examples. The songs are intended as long-term reference material rather than schema-only test data. See `docs/DEMO_SONG_GUIDE.md`, `docs/DEMO_COVERAGE_MATRIX.md`, and `docs/DEMO_QUALITY_AUDIT.md`.


## OSTF 4.0.0 scope

New Core features include:

- MIDI Channel 1–16
- `singleChannel`, `multiChannel`, and `mpe`
- Logical MIDI Buses
- Control Change
- Channel Mode messages
- Program Change
- Bank Select through CC 0 / CC 32
- 14-bit Pitch Bend
- Channel Pressure
- Polyphonic Key Pressure
- MPE lower/upper zones and per-note expression
- deterministic same-tick event ordering
- multiple melody-track declarations
- explicit lyric voices and voice-specific lyric alignments

OSTF 4.0 does **not** turn OSTF into a DAW project file. Physical MIDI ports, DAW routing, plug-in state, audio routing, SysEx, MIDI Clock/MTC/MMC, and MIDI 2.0 UMP are outside OSTF 4.0.0 Core.

## Compatibility

OSTF 3.5.0 remains the final OSTF 3.x specification. Its existing Note and song semantics are retained.

A Player that recognizes `4.0.0` but has not implemented every new performance feature may use **Compatibility Playback**:

- inherited Section / Chord / Track / Clip / Note / Lyrics data remain usable;
- unsupported 4.0 MIDI-performance and multi-voice relationships may be ignored for playback;
- unsupported 4.0 data must not be silently deleted on save.

The 12 musical demo songs in this repository were also checked as 3.5 compatibility views after removal of 4.0-only fields: **12 / 12 pass the OSTF 3.5 Schema**.

## Core documents

- `schema/ostf-4.0.0.schema.json` — authoritative structural schema
- `docs/FORMAT_SPECIFICATION.md` — normative semantic specification
- `docs/COMPLETE_MANUAL.md` — comprehensive implementation and usage manual
- `docs/PARAMETER_REFERENCE.md` — complete parameter reference
- `docs/PLAYER_COMPATIBILITY.md` — compatibility playback and safe-save rules
- `docs/MIGRATION_3_5_TO_4_0.md` — migration / downgrade rules
- `docs/SCHEMA_DELTA_3_5_TO_4_0.md` — field-level delta
- `docs/OSTF4_SCOPE.md` — Core responsibility boundary
- `docs/DEMO_SONG_GUIDE.md` — musical demo design and catalog
- `docs/DEMO_COVERAGE_MATRIX.md` — 4.0 feature/value coverage map
- `docs/EXAMPLE_INDEX.md` — index of all 58 public `.ostf` examples
- `docs/PARAMETER_REFERENCE_COVERAGE.md` — Schema/reference coverage audit
- `RELEASE_READINESS.md` — release decision
- `DEMO_STATIC_VALIDATION.md` — demo/fixture static validation report
- `SHA256SUMS.txt` — checksums

## Canonical examples

- `examples/01-midi-performance.ostf`
- `examples/02-mpe.ostf`
- `examples/03-multi-voice.ostf`
- `examples/04-multi-bus.ostf`
- `examples/05-multi-channel-order.ostf`

These are short canonical implementation references.

## Musical demo songs

`examples/demo-songs/` contains **12 independently composed demo songs**. They are not variants of one shared MIDI sequence.

Highlights:

1. `OSTF4_Demo_01_Neon_Crosswalk.ostf` — Pop / Bank / Program / CC
2. `OSTF4_Demo_02_Rust_and_Rain.ostf` — Rock / true multiChannel guitar stack
3. `OSTF4_Demo_03_Last_Platform.ostf` — 3/4 ballad / aligned lyrics / aftertouch
4. `OSTF4_Demo_04_Blue_Lantern.ostf` — Jazz / chord voicings
5. `OSTF4_Demo_05_Above_the_Vector.ostf` — Trance / multiple logical buses / CC
6. `OSTF4_Demo_06_Velvet_Corners.ostf` — Neo Soul / multiple melodies
7. `OSTF4_Demo_07_Glass_Garden_MPE_Lower.ostf` — polyphonic MPE Lower Zone
8. `OSTF4_Demo_08_Orbiting_Light_MPE_Upper.ostf` — polyphonic MPE Upper Zone
9. `OSTF4_Demo_09_Two_Sides_of_Morning.ostf` — lead/high/low vocal harmony
10. `OSTF4_Demo_10_Answer_Back_Call_and_Response.ostf` — separate call/response lyrics
11. `OSTF4_Demo_11_Shifting_Ground_Mixed_Meter.ostf` — 7/8 → 4/4 → 5/4, key/tempo changes, drums/fixed trigger
12. `OSTF4_Demo_12_Signal_Bloom_Complete_Showcase.ostf` — 40-bar integrated showcase

See `docs/DEMO_SONG_GUIDE.md`.

## Feature fixtures

`examples/feature-fixtures/` contains **41 technical fixtures** for cases that should not be forced into musical demo songs.

They cover, among other things:

- Channel 1 and 16
- CC controller/value minima and maxima
- Channel Mode 120–127
- Program 0 and 127
- Pitch Bend 0 / 8192 / 16383
- Pressure 0 / 127
- MPE lower/upper with `memberChannelCount` 1 / 15
- Bank Select ordering
- raw RPN / NRPN CC sequences
- `targetNoteId`
- event `order` -1 / 0 / 1 / 2 / 3
- negative clip-local MIDI event starts
- single-channel inheritance
- multiple logical buses using the same channel number
- shared/separate lyric documents across voices
- graceful degradation

See `docs/DEMO_COVERAGE_MATRIX.md`.

## Important design rule

Notes remain authoritative in:

```text
tracks[].clips[].notes[]
```

MIDI controller/program/pitch/pressure streams are stored separately in:

```text
tracks[].clips[].midiEvents[]
```

Note On/Off events are not duplicated into `midiEvents`.

## Logical MIDI Bus

OSTF stores:

```text
Track → Logical MIDI Bus → MIDI Channel → Note / MIDI Event
```

The host maps a Logical MIDI Bus to its own physical or virtual MIDI destination. Hardware device names and DAW port assignments are not portable Core song data.


## Publication package size

This publication package contains **78 files total**, including **58 `.ostf` reference files**, and stays below the requested 100-file GitHub upload limit.

The `.ostf` set consists of:

- 12 Golden Musical Demo Songs
- 41 Feature / Boundary Fixtures
- 5 Canonical Examples

The complete manual and parameter reference are audited against the 4.0.0 Schema. See `docs/PARAMETER_REFERENCE_COVERAGE.md` and `docs/EXAMPLE_INDEX.md`.

## Validation boundary

The release package has undergone structural and semantic static checks. Per project decision, Ableton Live, Max for Live, hardware MIDI and OSTF Player runtime testing are **not** release conditions for the format specification itself.

Implementation conformance remains the responsibility of each Player / Writer / Editor.
