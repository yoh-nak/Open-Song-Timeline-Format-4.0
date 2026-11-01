# OSTF 4.0.0 — Format Specification

> English edition. Japanese: [`FORMAT_SPECIFICATION.ja.md`](FORMAT_SPECIFICATION.ja.md)


Date: 2026-11-01  
Format name: **Open Song Timeline Format (OSTF)**  
File extension: **`.ostf`**

**Structural authority:** [`schema/ostf-4.0.0.schema.json`](../schema/ostf-4.0.0.schema.json)  
**Semantic authority:** this document. A file is fully conformant only when it satisfies both.

The words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are normative. Schema `default` values are interpretation defaults; they do not instruct a reader to materialize omitted properties when saving.

## 1. Purpose, scope, and design principles

OSTF exchanges structured song information: song metadata, timeline, form, harmony, concrete performed notes, lyrics, reusable editing relationships, and—beginning with 4.0—standard MIDI performance data and explicit multi-melody/multi-vocal relationships.

OSTF is not a DAW session format. It MUST NOT require a particular workstation, plug-in, operating-system MIDI device, audio interface, or rendering engine.

OSTF 4.0 inherits the 3.5 song/note model without redefining existing 3.x semantics. It adds typed Core fields for MIDI Channel, Logical MIDI Bus, Control Change, Program Change, Bank Select as CC 0/32 sequences, 14-bit Pitch Bend, Channel Pressure, Polyphonic Key Pressure, Channel Mode, MPE, multiple melody declarations, and lyric voices.

Standard 4.0 information MUST be stored in its named Core fields, not hidden only inside `extensions` or application-specific arbitrary JSON. Third-party/legacy extensions MAY be preserved verbatim.

The following are outside 4.0.0 Core: physical MIDI-port assignment, DAW track/output routing, plug-in state, audio routing, audio clips, SysEx, MIDI Clock, MTC, MMC, MIDI Show Control, MIDI 2.0 UMP, continuous tempo curves, score-layout state, and complete synthesis-engine restoration.

## 2. Serialization, versioning, and numeric rules

An OSTF file is one UTF-8 JSON object. Implementations MUST reject malformed UTF-8, duplicate object keys, isolated surrogates, NaN/Infinity, and trailing non-whitespace after the root JSON value.

Core integer fields MUST be JSON integer tokens. The maximum exact Core integer magnitude is `9007199254740991`.

The root required fields remain the same as 3.5:

`format`, `version`, `ppq`, `song`, `timeline`, `harmony`, `tracks`, `soundSlots`, `lyrics`, `resources`.

For this release, `format` MUST be `"OSTF"` and `version` MUST be `"4.0.0"`.

Optional root fields include `extensions`, `drumKits`, `contributors`, `document`, `relativePhrases`, `generation`, `presentation`, `phraseLibrary`, `clipChoices`, `localization`, `provenance`, and 4.0 `midi`.

A reader MUST use the specification matching the declared version. Reading a 3.x file MUST NOT silently rewrite it as 4.0. Unknown future versions MUST NOT be guessed to mean 4.0.

### 2.1 Compatibility Playback

A reader that explicitly recognizes `4.0.0` but has not implemented every 4.0 performance feature MAY ignore unsupported optional 4.0 behavior for playback/display while continuing to process inherited 3.x song data.

Typical temporarily ignored fields may include root `midi`, `tracks[].midi`, `notes[].channel`, `clips[].midiEvents`, `song.melodyTrackIds`, `lyrics.voices`, and `lyrics.alignments[].voiceId`.

This is graceful degradation for a **known** 4.0 file, not permission to treat unknown versions as 4.0.

A writer MUST NOT silently delete unsupported 4.0 data. If it cannot round-trip unknown/unimplemented Core fields without semantic loss, it MUST refuse destructive overwrite or offer an explicitly lossy downgrade/export path.

## 3. Time model, IDs, and references

`ppq` is integer ticks per quarter note, from 1 through 960000; 960 is recommended. Meter changes do not change tick size.

Global Core times are non-negative. Positive duration fields are at least 1 tick. Notes and MIDI events inside a clip use **clip-local** `start` and MAY be negative when the resulting absolute time remains non-negative.

Intervals are half-open: `[start, start + duration)`.

Absolute note/event time is `clip.start + local start`.

At one absolute tick, ordering is defined conceptually as:

1. Note Off (`order = 0`)
2. MIDI Event (`order = 1` when omitted)
3. Note On (`order = 2`)

A MIDI event MAY use a smaller or larger explicit numeric `order` to move before Note Off or after Note On. MIDI events with identical tick and identical order preserve array order. Notes in the same Note-Off/Note-On group are deterministically ordered by note ID.

Core IDs match `^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$`. Core object IDs are document-unique across Core object categories unless the specification explicitly defines a local ID scope (for example source-note IDs inside an individual phrase template).

A `{type,id}` reference MUST resolve to an object of the declared type. Readers MUST NOT repair references by name, array index, or “closest-looking” object.

## 4. Authoritative locations for song information

The authoritative location of a concept is important: OSTF avoids duplicate competing sources of truth.

| Concept | Authoritative Core location |
|---|---|
| title/subtitle/description | `song` |
| initial representative key | `song.key` |
| timed key changes | `timeline.keyChanges` |
| default scale | `song.scale` |
| timed scale contexts | `harmony.scales` |
| tempo/meter | `timeline.tempoChanges`, `timeline.meterChanges` |
| genres/moods/languages | `song.genres`, `song.moods`, `song.languages` |
| contributors/credits/rights | `contributors`, `song.credits`, `song.rights` |
| primary melody | `song.primaryMelodyTrackId` |
| explicit melody set | `song.melodyTrackIds` |
| primary lyric document | `song.primaryLyricDocumentId` |
| lyric voices | `lyrics.voices` |
| logical MIDI outputs | `midi.buses` + `tracks[].midi.busId` |
| concrete notes | `tracks[].clips[].notes` |
| MIDI performance/control | `tracks[].clips[].midiEvents` |
| chord voicing snapshot | `harmony.chords[].voicing` |

Missing optional information MUST remain unspecified; it MUST NOT be silently converted to C major, 120 BPM, Pop, English, channel 1, etc. Tempo and meter arrays are already required by the schema and therefore are not “optional defaults.”

## 5. Key, modulation, and scale

A key state may be `tonal`, `atonal`, or `unspecified`. A tonal key requires tonic and mode. Atonal/unspecified states do not carry a tonal tonic/mode.

Pitch spelling is preserved as `{step, alter}`. Enharmonic spellings such as C-sharp and D-flat are semantically distinct written spellings even when they resolve to the same pitch class.

Common mode identifiers include `major`, `minor`, `dorian`, `phrygian`, `lydian`, `mixolydian`, and `locrian`; unknown authored mode strings MUST be preserved rather than coerced to major/minor.

`timeline.keyChanges` contains changes after time zero. The initial representative key belongs in `song.key`; a redundant `at:0` key change is not used to create a second source of truth.

A key change persists until the next key change. Borrowed chords or local scale contexts do not automatically imply a key change.

`song.scale` is an optional default scale definition. `harmony.scales` contains timed scale contexts. Neither is automatically regenerated from key metadata during ordinary load/save.

## 6. Tempo, meter, form, duration, and pickup

`timeline.tempoChanges` and `timeline.meterChanges` are required non-empty arrays. Their initial entries establish the initial tempo and meter.

Meter denominator values are limited by the schema. Optional `beatGroups` describe additive grouping in denominator units; when present they MUST sum to the meter numerator and do not alter timestamps.

`timeline.sections` describes musical form. Section ranges SHOULD form a coherent authored structure; the Golden reference demos use complete gap-free, non-overlapping coverage from song start to end.

`timeline.duration` is an optional authored total duration. `barOrigin` describes bar-number origin/pickup presentation and does not shift the absolute event timeline.

## 7. Harmony, voicing, and performed notes

`harmony.chords` stores harmonic events. A chord label may be present alone, or a structured root/interval representation may be supplied. If one of root/intervals is present, the other is required by schema semantics.

A chord `voicing` is a pitch snapshot describing voicing, not an additional sequence of automatically sounding notes. Concrete playback notes live only in track clips.

`harmony.scales` is contextual analysis/harmonic information; it MUST NOT cause a renderer to invent notes.

## 8. Tracks, clips, notes, percussion, and MIDI performance

Tracks contain concrete arrangement data. Clips are positioned in the global timeline and contain notes plus optional 4.0 `midiEvents`.

A note targets either `pitch` or `soundSlotId`, never both. Sound slots and drum kits provide document-level names/roles for fixed percussive or trigger semantics without forcing a particular synthesizer.

### 8.1 Note completion and expressive attributes

`velocity` is authored Note-On velocity 1–127 when present. `releaseVelocity` is Note-Off velocity 0–127. `enabled:false` preserves a note but suppresses scheduling. `probability` is 0.0–1.0. `velocityDeviation` requires velocity and the resulting endpoint MUST remain in 1–127.

`pitchRole:"fixedTrigger"` means the exact MIDI key is a non-musical trigger (for example a keyswitch); it MUST NOT be transposed or used as musical-pitch evidence.

### 8.2 Track MIDI mode and MIDI Channel

OSTF channel numbers are stored as human-readable **1–16**.

Track `midi.mode` values:

- `singleChannel`: a track-level channel may be inherited by notes/events that omit channel.
- `multiChannel`: channel identity varies within the track; concrete notes/events use explicit channels where identity matters.
- `mpe`: the track uses an MPE zone and member-channel allocation rules.

Channel is part of the performance representation in 4.0 and is required for correct MPE/per-channel expression semantics.

### 8.3 Logical MIDI Bus

Root `midi.buses` declares document-local logical MIDI buses. Each bus defines an independent 16-channel namespace.

Logical buses are not physical OS/USB MIDI ports and MUST NOT encode workstation-specific device IDs, Ableton “MIDI To” routes, plug-in instance handles, or audio routing. A host MAY map a logical bus to a local destination at playback time.

Two different buses MAY both use channel 1; they remain distinct address spaces.

### 8.4 MIDI events

`clips[].midiEvents` holds 4.0 performance/control events. Notes remain in `notes[]`; Note On/Off MUST NOT be duplicated as custom MIDI-event types.

Supported event families are:

- Control Change (`controller` 0–119, `value` 0–127)
- Channel Mode (`controller` 120–127, `value` 0–127)
- Program Change (`program` 0–127)
- Pitch Bend (`value14` 0–16383, center 8192)
- Channel Pressure (`value` 0–127)
- Polyphonic Key Pressure (`pitch` 0–127, `value` 0–127)

Bank Select is represented as standard CC 0 (MSB), optional/appropriate CC 32 (LSB), followed by Program Change. A duplicate OSTF-specific bank field is intentionally not introduced.

RPN and NRPN are represented losslessly as their underlying Control Change sequences in 4.0.0. Implementations MUST NOT rewrite them into a proprietary event type as the only source of truth.

### 8.5 `targetNoteId` and per-note expression

Supported expression events MAY include `targetNoteId` to bind the semantic intention to a concrete note. The reference MUST resolve to a note in the same track context and the event/channel/pitch relationship MUST be semantically consistent with that note.

For probabilistic notes, a targeted expression event associated only with a note that is not scheduled on a given realization MUST NOT leak into unrelated sounding notes.

### 8.6 MPE

MPE is represented using ordinary MIDI channels and ordinary event types; OSTF does not invent separate `mpePitch`, `mpePressure`, or `mpeTimbre` event types.

An MPE track declares lower or upper zone configuration and member-channel count. Lower and upper zones sharing a logical bus MUST NOT occupy overlapping channels.

Per-note Pitch Bend, pressure, and CC (commonly CC74) use the member channel of the target note. Simultaneous notes that require independent expression MUST occupy distinct member channels for the overlapping duration.

The Golden demos include both lower-zone and upper-zone polyphonic MPE and a combined showcase with non-overlapping zones on one logical bus.

### 8.7 Same-tick global ordering

The ordering model in section 3 applies globally across note boundaries and MIDI events. Bank/program/controller sequences that depend on order SHOULD use explicit `order` where ambiguity would otherwise remain.

## 9. Lyrics, language, primary lyrics, and lyric voices

Lyrics are structured into documents, phrases, units, and alignments. A lyric document holds authored text identity; alignment binds lyric units to concrete performed notes/times.

`song.primaryLyricDocumentId` selects the primary lyric document but does not delete or deactivate other lyric documents.

### 9.1 Multiple vocals and chorus lyrics

OSTF 4.0 adds `lyrics.voices`. A voice can link a named/role-bearing vocal part to a melody track and lyric document.

Typical role strings include `lead`, `harmony`, `backing`, `unison`, `double`, `counter`, `adlib`, and `response`, but role is intentionally extensible. Unknown authored role strings MUST be preserved.

Multiple voices MAY share the same lyric document when they sing the same text. Separate documents SHOULD be used when different parts sing different text. `alignments[].voiceId` disambiguates which vocal part an alignment belongs to.

## 10. Genre, mood, credits, rights, resources, and localization

Genres, moods, and role/name strings are authored metadata. Readers MUST preserve unknown values.

Contributor identities and ordered song credits are separate so the same person/entity can be reused across several roles without duplicating identity data.

Resources describe external or embedded references according to the schema; OSTF does not assume network availability.

Localization adds display alternatives without overwriting original authored names/text. Original source-language values remain stable fallbacks.

## 11. Shared editing information

OSTF Core includes structured shared-editing relationships such as relative phrases, generation metadata, presentation roles, phrase library entries, clip choices, localization, document identity, and provenance.

These fields describe authored/shared state. They are not instructions to rerun an AI model or regenerate a song automatically on load.

### 11.1 Relative phrases

Relative phrase information represents reusable/transposable relationships while concrete performed notes remain authoritative for actual playback.

### 11.2 Generation

Generation metadata records process/intention/history. It MUST NOT replace concrete Core notes or become a required rendering engine state.

### 11.3 Presentation

Presentation describes shared display/role hints that are meaningful across implementations. Ephemeral window geometry or application-local UI state stays outside Core.

### 11.4 Phrase library

Phrase templates/source notes are reusable source material. Source-note IDs are local to their template. Concrete instantiated track notes receive document-level IDs and are authoritative for playback.

MIDI channel allocation—especially MPE member channel selection—is a concrete performance placement concern and SHOULD NOT be baked into reusable source-note identity unless the schema explicitly calls for it.

### 11.5 Clip choices

Clip-choice groups/candidates describe authored alternatives. The chosen state does not imply deletion of alternatives.

## 12. Document identity, history, merge, and safe save

`document` and `provenance` identify document/revision lineage independently from song authorship.

Editors SHOULD preserve stable IDs when moving the same object and issue new IDs for independent copies. Merge logic MUST compare by stable identity and semantic ownership, not by display name alone.

When merging a track, its notes, clip MIDI events, and track MIDI settings belong to the track/performance content. Root logical MIDI buses are shared document resources and MUST NOT be silently remapped merely because another bus has the same display name.

Writers SHOULD use atomic-save behavior where practical: write a complete new representation, validate it, then replace the destination rather than partially mutating the only copy.

## 13. Migration boundary: 3.5 to 4.0

A valid 3.5 song can migrate to 4.0 without inventing MIDI performance data. The minimum migration is version change plus any intentional new 4.0 fields.

If `primaryMelodyTrackId` exists, a migration tool MAY create `melodyTrackIds` containing that track, but MUST NOT invent additional melody tracks from names/roles without an explicit migration rule/user action.

MIDI channels, buses, controls, MPE zones, and lyric voices MUST be created only when the source actually contains or the user explicitly supplies that information.

Downgrading from 4.0 to 3.5 may lose MIDI events, channels, logical buses, MPE configuration, multiple-melody declarations, and lyric-voice relationships. A downgrade writer MUST disclose the loss and MUST NOT claim lossless 4.0 round-trip.

See [`MIGRATION_3_5_TO_4_0.md`](MIGRATION_3_5_TO_4_0.md).

## 14. Responsibility boundary and future versions

OSTF 4.0.0 stops at structured song + arrangement + performed notes + MIDI 1.0-compatible performance expression + logical routing identity + multi-melody/multi-voice relationships.

Physical device routing and DAW/session restoration stay host-specific. MIDI 2.0, SysEx, synchronized machine-control protocols, audio-project state, and other broader domains require future work rather than undocumented 4.0 extensions pretending to be Core.

## 15. Conformance and release categories

A **Reader** conforms when it validates/understands the fields it claims, preserves required semantics, and does not silently reinterpret unknown information.

A **Writer** conforms only when emitted files satisfy both schema and semantic requirements and it does not silently discard unsupported Core information during round-trip.

A **Compatibility Player** MAY ignore unsupported 4.0 performance behavior after recognizing 4.0, but must preserve the distinction between “not rendered” and “not present.”

A **Full Performance Player** additionally implements the 4.0 MIDI channel/event/MPE behavior it claims.

Format-release readiness is separate from implementation-release readiness. The reference package validates the format and examples statically; DAW, hardware, MPE-device, and specific-player runtime conformance tests belong to individual implementations.
