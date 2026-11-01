# OSTF 3.5 → 4.0 Migration Guide

> English edition. Japanese: [`MIGRATION_3_5_TO_4_0.ja.md`](MIGRATION_3_5_TO_4_0.ja.md)


## 1. Principle

Migration is conservative. Do not invent information that the 3.5 source does not contain. A 3.5 file may become a valid 4.0 file with no MIDI-performance additions at all.

## 2. Minimum migration

Preserve all 3.5 Core content and semantics. Change the declared version only when intentionally saving as 4.0 and after validating the resulting document against the 4.0 schema/semantics.

Do not create empty MIDI buses, empty voice structures, or fabricated channels simply because 4.0 supports them.

## 3. Multiple melodies

If `primaryMelodyTrackId` exists, a migration tool MAY initialize `melodyTrackIds` with that same track. It MUST NOT guess additional melody tracks from names/roles without an explicit rule or user action.

## 4. Lyric voices

Create `lyrics.voices` only when there is known information about vocal-part identity. Existing lyric documents/alignments remain valid without voices.

When several parts sing the same text, prefer one lyric document plus separate voices and `voiceId` alignments. Use separate documents for genuinely different text.

## 5. MIDI Channel

Only add channels when source MIDI/DAW information is known. Preserve the distinction between a track-wide single channel and concrete per-note/per-event channels.

Use file channel values 1–16.

## 6. Logical MIDI Bus

Do not migrate physical device identifiers directly into portable Core routing. Create meaningful logical buses and let the host map them to physical destinations.

Several buses can reuse the same channel number because each bus is a separate channel namespace.

## 7. Control Change

Known source CC messages map to `midiEvents` with `type: controlChange`, controller 0–119, value 0–127, clip-local start, and the appropriate channel/inheritance context.

## 8. Bank Select / Program Change

Represent bank selection as CC0/CC32 followed by Program Change. Program values are 0–127 in the file even if a UI displays 1–128.

## 9. Pitch Bend

Preserve MIDI 1.0 bend as 14-bit `value14` (0–16383, center 8192). Do not scale it to 7-bit.

## 10. Aftertouch / pressure

Map channel aftertouch to Channel Pressure and polyphonic aftertouch to Polyphonic Key Pressure. Where a specific OSTF note is known, `targetNoteId` may make the semantic relationship explicit.

## 11. RPN / NRPN / 14-bit controller pairs

Preserve these as their underlying CC sequences in 4.0.0. Maintain ordering and channel identity.

## 12. MPE

When the source is known to be MPE, create a track `midi.mode: mpe` and the appropriate lower/upper zone configuration. Concrete notes/events retain member-channel identity. Use `targetNoteId` for per-note expression where the target is known.

Do not reinterpret arbitrary multi-channel MIDI as MPE without evidence.

## 13. Same-tick ordering

Preserve source message order when it affects meaning. OSTF defaults to Note Off → MIDI Event → Note On, with explicit event `order` available for other required positions.

## 14. 4.0 → 3.5 downgrade

A downgrade may lose:

- logical MIDI buses;
- track/note/event channel relationships;
- `midiEvents`;
- MPE configuration and targeted expression;
- multiple-melody declarations;
- lyric voices and voice-specific relationships.

A downgrade tool MUST warn about this loss. It may keep inherited tracks/notes/chords/lyrics that are valid in 3.5.

## 15. Non-inference rule

Migration tools must prefer “unspecified” over fabricated certainty. Names such as `Lead`, `MPE`, `Synth 2`, or `Port A` are not sufficient by themselves to reconstruct semantic routing/performance relationships unless the migration policy explicitly defines that inference and the user accepts it.
