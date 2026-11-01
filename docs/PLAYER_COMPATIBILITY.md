# OSTF 4.0 — Player Compatibility and Safe-Save Rules

> English edition. Japanese: [`PLAYER_COMPATIBILITY.ja.md`](PLAYER_COMPATIBILITY.ja.md)


## 1. Compatibility goal

OSTF 4.0 intentionally preserves the 3.5 song/note model. A player may therefore support a transitional **Compatibility Playback** mode before implementing the complete 4.0 MIDI-performance layer.

## 2. What a transitional player must do

A transitional player MUST first recognize `version: "4.0.0"` as a known format. It may then render inherited 3.x structures such as sections, tempo, meter, key, harmony, tracks, clips, notes, sound slots, drum kits, and ordinary lyric structures.

It MAY ignore unsupported 4.0 playback semantics such as:

- root `midi` logical-bus routing;
- `track.midi` channel/mode/MPE configuration;
- note `channel`;
- `clip.midiEvents` (CC/program/bend/pressure/channel mode);
- `song.melodyTrackIds` as an additional relationship;
- `lyrics.voices` and `alignment.voiceId` as additional relationship metadata.

Ignoring those items for rendering MUST NOT be confused with deleting them from the file.

## 3. Safe save

If a player/editor cannot round-trip unsupported 4.0 Core fields unchanged, it MUST NOT destructively overwrite the original 4.0 file. Safe choices are:

1. read-only playback;
2. Save As to a format/version that explicitly warns about loss;
3. preserve unknown/unimplemented Core content losslessly;
4. refuse save until full preservation is possible.

## 4. Unknown future versions

Compatibility Playback applies only after positively recognizing `4.0.0`. A future `4.1`, `5.0`, or unknown version MUST NOT automatically be treated as 4.0.

## 5. Golden demos

The 12 Golden Demo Songs were statically transformed into 3.5 Compatibility Views by removing only 4.0-specific relationship/performance fields and changing the version to 3.5.0. All 12 views validate against the 3.5 schema. This confirms that their inherited song body remains structurally 3.5-compatible; it is not a runtime guarantee for an unmodified 3.x player.

## 6. Full 4.0 playback

A player claiming full 4.0 performance conformance should additionally implement the channel model, logical buses, MIDI-event ordering, program/bank/controller state, pitch bend, pressure, MPE zone/member semantics, and any multi-voice display/playback behavior it advertises.
