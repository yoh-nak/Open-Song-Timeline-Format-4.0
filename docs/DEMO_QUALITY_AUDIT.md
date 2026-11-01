# OSTF 4.0 — Golden Demo Set Quality Audit

> English edition. Japanese: [`DEMO_QUALITY_AUDIT.ja.md`](DEMO_QUALITY_AUDIT.ja.md)


Audit date: 2026-11-01

## Purpose

This audit evaluates the 12 files in `examples/demo-songs/` as long-term Golden reference songs, not merely schema test vectors. Runtime DAW/player/hardware testing is outside this audit by project decision.

## Golden requirements

Every official demo must:

1. be independently composed rather than a parameter-swapped copy;
2. contain at least 24 bars of practical song structure;
3. use multiple sections with continuous, non-overlapping section coverage;
4. cover the song timeline with coherent chord ranges;
5. use a primary melody with meaningful pitch/rhythm variation (sustained MPE texture demos excepted from the rhythm criterion);
6. avoid unintended overlap on monophonic lead/vocal/response tracks;
7. keep targeted MIDI events inside the target note's sounding range;
8. avoid simultaneous-note collision on the same MPE member channel;
9. use `targetNoteId` for intended per-note MPE expression;
10. align every sung note exactly once for each declared lyric voice;
11. avoid orphan lyric units;
12. retain a valid 3.5 Compatibility View after removal of 4.0-only relationships/performance data.

## Audit results

- Public `.ostf`: **58 / 58** pass OSTF 4.0 Schema.
- Core semantic reference checks: **58 / 58 PASS**.
- Golden Demo strict musical/structural audit: **12 / 12 PASS**.
- Golden Demo 3.5 Compatibility View: **12 / 12 PASS**.
- Exact full-arrangement duplicates among 12 demos: **0**.
- Section coverage gaps/overlaps: **0**.
- Chord timeline gaps/overlaps: **0**.
- Vocal-note alignment omissions: **0**.
- Targeted MIDI events outside target note: **0**.
- MPE same-channel simultaneous collisions: **0**.

## Music-specific corrections from earlier drafts

The earlier repeated 16-bar base-song approach was removed. The Golden set uses distinct Pop, Alternative Rock, 3/4 Ballad, Jazz Swing, Trance, Neo Soul, MPE Lower, MPE Upper, three-part Vocal Pop, Call & Response, mixed-meter Progressive Rock, and a Complete Showcase.

Fixed +4-semitone vocal harmony was removed. Vocal harmonies use scale-aware diatonic relationships and checked voice ordering. MPE demos use simultaneous member-channel polyphony rather than monophonic channel rotation.

## Release decision

**Golden Demo Set: Release GO** as a format reference set. This does not constitute runtime conformance certification for OSTF Player, Ableton Live/Max for Live, MIDI hardware, MPE hardware, or plug-in instruments.
