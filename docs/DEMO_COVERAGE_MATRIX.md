# OSTF 4.0 — Demo / Fixture Coverage Matrix

> English edition. Japanese: [`DEMO_COVERAGE_MATRIX.ja.md`](DEMO_COVERAGE_MATRIX.ja.md)


This matrix tracks public `.ostf` examples for the Core areas introduced in 4.0. Demo Songs are musical integration examples; Feature Fixtures cover boundaries and semantic edge cases.

## 1. 4.0-added Core fields

| 4.0 addition | Demo Song | Feature Fixture | Status |
|---|---|---|---|
| root `midi` | 01/05/07/08/12 and others | Multiple Buses | Covered |
| `midi.buses[].id/name` | 05/12 | Multiple Buses | Covered |
| `tracks[].midi` | MIDI demos | Channel/MPE fixtures | Covered |
| `mode=singleChannel` | 01 etc. | Single Channel | Covered |
| `mode=multiChannel` | 02/12 | MultiChannel 1/16 | Covered |
| `mode=mpe` | 07/08/12 | MPE Lower/Upper | Covered |
| `track.midi.busId` | 05/12 | Multiple Buses | Covered |
| `track.midi.channel` | 01 etc. | Channel 1/16 | Covered |
| `mpe.zone` | 07/08/12 | Lower/Upper | Covered |
| `mpe.memberChannelCount` | 07/08/12 | 1/15 boundaries | Covered |
| `notes[].channel` | 02/07/08/12 etc. | Channel fixtures | Covered |
| `clips[].midiEvents` | 01/02/03/05/07/08/12 | Event fixtures | Covered |
| Event `id/start/type/channel` | MIDI demos | Event fixtures | Covered |
| Event `order` | — | Same Tick Order | Covered |
| CC `controller/value` | 01/05/07/12 | CC min/max/common | Covered |
| Program `program` | 01/02/04/12 | Program 0/127 | Covered |
| Pitch Bend `value14` | 02/03/05/07/08/12 | 0/8192/16383 | Covered |
| Channel/Poly Pressure `value` | 03/07/08/12 | 0/127 | Covered |
| Poly Pressure `pitch` | 03/08/12 | Poly Pressure fixtures | Covered |
| `targetNoteId` | 02/03/07/08/12 | all supported targeted events | Covered |
| `song.melodyTrackIds` | 06/09/10/12 | Multiple Melody Tracks | Covered |
| `lyrics.voices` | 03/09/10/12 | Shared/Separate Docs | Covered |
| Lyric Voice fields | 09/10/12 | Voice fixtures | Covered |
| `alignments[].voiceId` | 03/09/10/12 | Voice fixtures | Covered |

All schema properties newly introduced by OSTF 4.0 appear in the public reference example set.

## 2. Boundary values

| Item | Public fixture coverage |
|---|---|
| MIDI Channel min/max | 1 / 16 |
| CC controller min/max | 0 / 119 |
| CC value min/max | 0 / 127 |
| Channel Mode range | 120–127, individual fixtures |
| Program min/max | 0 / 127 |
| Pitch Bend | 0 / 8192 / 16383 |
| Channel Pressure | 0 / 127 |
| Poly Pressure | 0 / 127 |
| MPE member count | 1 / 15, lower and upper |
| Event order | below Note Off / 0 / default region / Note On / above Note On |
| Negative clip-local MIDI event start | covered |

## 3. Semantic rules

Covered fixtures/demos include single-channel inheritance, concrete multi-channel identity, MPE lower/upper, non-overlapping lower+upper on one bus, same channel number on different buses, explicit event channel without track MIDI settings, Bank Select CC0/32+Program, raw RPN/NRPN, all supported targeted-expression event families, probability + targeted expression, and same-tick ordering.

## 4. Multi-melody / multi-voice

Covered: primary + counter melody; lead + high + low harmony; call + response + counter; shared lyric document across voices; separate lyric documents; voice-specific alignment; and primary melody membership in the melody set.

## 5. Coexistence with inherited 3.x features

Golden demos combine 4.0 with chord structure/voicing, scales/modes, 3/4, 7/8 and 5/4 beat grouping, tempo/key changes, sound slots/drum kits, release velocity, probability, velocity deviation, fixed triggers, document identity, credits/rights, provenance, localization, presentation, generation, phrase library, relative phrases, and clip choices.

## 6. Compatibility Playback static check

All 12 Golden Demo Songs pass a 3.5 schema validation after removing only 4.0-specific fields and changing the declared version to 3.5.0. This is a static structural compatibility check, not a claim that an unmodified 3.x player accepts a raw 4.0 file.
