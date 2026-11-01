# OSTF 4.0 — Golden Demo Song Guide

> English edition. Japanese: [`DEMO_SONG_GUIDE.ja.md`](DEMO_SONG_GUIDE.ja.md)

The `examples/demo-songs/` directory is the long-term **Golden Musical Demo Set**. Technical boundaries belong in `examples/feature-fixtures/`; the demo songs are intended to remain understandable as music.

## Demo set

| # | File | Title | Primary purpose |
|---:|---|---|---|
| 01 | `OSTF4_Demo_01_Neon_Crosswalk.ostf` | Neon Crosswalk | Pop; Bank Select, Program Change, CC, Channel Mode, drums |
| 02 | `OSTF4_Demo_02_Rust_and_Rain.ostf` | Rust and Rain | Alternative Rock; simultaneous multiChannel guitar, channel-specific program/CC/bend |
| 03 | `OSTF4_Demo_03_Last_Platform.ostf` | Last Platform | 3/4 ballad; complete lyric alignment, tempo relaxation, bend and pressure |
| 04 | `OSTF4_Demo_04_Blue_Lantern.ostf` | Blue Lantern | Jazz Swing; extended harmony/voicing, half-bar changes, walking bass and comping |
| 05 | `OSTF4_Demo_05_Above_the_Vector.ostf` | Above the Vector | Trance; multiple Logical MIDI Buses, dense CC automation and bend |
| 06 | `OSTF4_Demo_06_Velvet_Corners.ostf` | Velvet Corners | Neo Soul; multiple melody tracks, counter melody, 7th/9th harmony |
| 07 | `OSTF4_Demo_07_Glass_Garden_MPE_Lower.ostf` | Glass Garden | MPE Lower Zone; simultaneous per-note expression |
| 08 | `OSTF4_Demo_08_Orbiting_Light_MPE_Upper.ostf` | Orbiting Light | MPE Upper Zone; polyphonic key pressure and per-note expression |
| 09 | `OSTF4_Demo_09_Two_Sides_of_Morning.ostf` | Two Sides of Morning | Vocal Pop; lead + high + low harmony, shared lyric document, full voice alignment |
| 10 | `OSTF4_Demo_10_Answer_Back_Call_and_Response.ostf` | Answer Back | Call & Response; separate lyric documents plus counter melody |
| 11 | `OSTF4_Demo_11_Shifting_Ground_Mixed_Meter.ostf` | Shifting Ground | Mixed-meter Progressive Rock; 7/8→4/4→5/4, beatGroups, tempo/key changes, fixed trigger |
| 12 | `OSTF4_Demo_12_Signal_Bloom_Complete_Showcase.ostf` | Signal Bloom — Complete OSTF 4.0 Showcase | Complete Showcase; multi-bus, single/multi/MPE lower+upper, multi-voice lyrics, inherited 3.x layers |


## Golden quality rules

Every Golden Demo is independently composed, at least 24 bars long, multi-section, and carries coherent harmony/timeline data. Section and chord coverage is gap-free/non-overlapping in the audit. Vocal demos fully align every sung note for each declared voice. MPE demos use simultaneous notes on distinct member channels rather than merely rotating a monophonic line through channels.

## Compatibility Playback

A 4.0-aware but performance-incomplete player may ignore unsupported 4.0 MIDI/voice relationships while rendering inherited notes/chords/tracks/lyrics. The 12 demos all have statically validated 3.5 Compatibility Views after 4.0-only fields are removed.

## Demo vs fixture

Boundary values such as Program 0/127, Pitch Bend 0/8192/16383, Channel Mode 120–127, MPE member count 1/15, raw RPN/NRPN, and same-tick order are kept in Feature Fixtures rather than forced awkwardly into musical arrangements.
