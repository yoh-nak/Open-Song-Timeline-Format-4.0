# OSTF 3.5 → 4.0 — Schema Delta

> English edition. Japanese: [`SCHEMA_DELTA_3_5_TO_4_0.ja.md`](SCHEMA_DELTA_3_5_TO_4_0.ja.md)

OSTF 4.0 preserves the 3.5 Core song/note foundation and adds channel-aware MIDI performance plus explicit multi-melody/multi-voice relationships.

## Root changes

Added root properties: `midi`.  Removed root properties: none.

## Newly introduced/renamed definitions detected by schema comparison

- `channelModeEvent`
- `channelPressureEvent`
- `controlChangeEvent`
- `lyricVoice`
- `midi`
- `midiBus`
- `midiChannel`
- `midiEvent`
- `mpeSettings`
- `pitchBendEvent`
- `polyPressureEvent`
- `programChangeEvent`
- `trackMidi`

## Semantic 4.0 additions

The principal additions are:

- root logical MIDI buses;
- track MIDI mode/bus/channel/MPE configuration;
- note channel;
- clip MIDI events;
- Control Change and Channel Mode;
- Program Change and standard Bank Select sequences;
- 14-bit Pitch Bend;
- Channel Pressure and Polyphonic Key Pressure;
- `targetNoteId` per-note semantic binding;
- MPE lower/upper zones;
- explicit multiple melody-track declarations;
- lyric voices and voice-specific alignments;
- deterministic same-tick ordering rules involving MIDI events.

Existing 3.5 note semantics are retained. 4.0 does not move notes into `midiEvents` and does not redefine 3.5 song structures.

## Compatibility consequence

A raw 4.0 document is not guaranteed to validate against the 3.5 schema because it may contain new fields. However, the inherited song body remains intentionally compatible in meaning; the Golden demo Compatibility Views validate 12/12 against 3.5 after 4.0-only fields are removed.
