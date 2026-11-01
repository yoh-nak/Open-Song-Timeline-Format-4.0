# OSTF 4.0.0 — Complete Manual

> English edition. Japanese: [`COMPLETE_MANUAL.ja.md`](COMPLETE_MANUAL.ja.md)


This manual is the practical companion to the normative [`FORMAT_SPECIFICATION.md`](FORMAT_SPECIFICATION.md) and machine-readable [`ostf-4.0.0.schema.json`](../schema/ostf-4.0.0.schema.json). It is written for reader/writer implementers, DAW-tool authors, editors, players, import/export utilities, and users who need to understand how the pieces fit together.

## 1. What OSTF 4.0 is

OSTF 4.0 is a DAW-independent structured-song interchange format. It keeps the 3.5 song/note foundation and adds a typed MIDI performance layer plus explicit multi-melody and multi-vocal relationships.

The central rule is separation of concerns:

- **Composition:** key, tempo, meter, form, harmony, lyrics.
- **Arrangement:** tracks, clips, concrete notes, instrument roles, sound slots.
- **Performance:** channels, CC, program changes, pitch bend, pressure, MPE.
- **Logical routing:** document-local MIDI buses.
- **Host routing:** physical MIDI ports, DAW outputs, plug-ins—outside Core.

Do not turn OSTF into a DAW project file by storing machine-specific routing as Core song truth.

## 2. File format and root object

An `.ostf` file is UTF-8 JSON. Required root fields are `format`, `version`, `ppq`, `song`, `timeline`, `harmony`, `tracks`, `soundSlots`, `lyrics`, and `resources`.

`format` is `OSTF`; this release uses `version: "4.0.0"`.

4.0-specific root `midi` is optional. A song that does not use MIDI-performance metadata does not need empty bus declarations or empty event arrays.

## 3. Reading order for implementers

A practical reader should:

1. parse UTF-8 JSON without duplicate-key ambiguity;
2. confirm `format` and version;
3. validate the root structure;
4. build ID registries;
5. resolve song/timeline/harmony/tracks/lyrics/resources;
6. resolve 4.0 logical buses and track MIDI modes;
7. resolve notes and MIDI events;
8. apply semantic validation (channels, MPE zones, targetNoteId, lyric voices);
9. render only features actually implemented;
10. preserve unsupported known 4.0 fields if writing back.

## 4. PPQ and time

All Core musical time is in ticks at document PPQ. Clip notes and MIDI events use clip-local starts; global time is clip start plus local start. A negative local start is valid only when the resulting absolute start is non-negative.

Meter affects musical display, not tick size. Tempo affects seconds, not tick identity.

## 5. IDs and references

Use stable IDs for stable objects. Moving an object should keep its ID; independent copying should create a new ID. Never resolve broken references by matching display names.

The `ref` structure provides typed references. Core object IDs are document-unique except explicitly local scopes such as phrase-template source-note IDs.

## 6. Song metadata

`song` contains the authored title and optional subtitle/description, genres, moods, languages, representative key/scale, credits/rights, primary melody, primary lyric document, and 4.0 melody-track set.

`primaryMelodyTrackId` chooses one primary melody. `melodyTrackIds` can declare the broader set of melody-bearing tracks. The primary should be a member when both are present.

Do not infer extra melody tracks merely from names such as “Lead” or “Harmony.”

## 7. Timeline

`timeline.sections` represents form. `tempoChanges`, `meterChanges`, optional `keyChanges`, duration, and `barOrigin` describe shared temporal context.

Golden demos intentionally use real multi-section structures rather than schema-only one-bar fragments.

## 8. Key and scale

`key` distinguishes tonal, atonal, and unspecified states. Preserve spelling and unknown authored mode strings. `timeline.keyChanges` is the timed modulation map. `song.scale` is an optional default scale, while `harmony.scales` contains timed contexts.

Do not silently reinterpret a local scale as a global key change.

## 9. Harmony

Chords may be represented by authored label plus optional structured root/interval/bass data. Chord voicing is descriptive harmonic voicing, not a second playback-note stream.

Concrete audible pitches come from track notes.

## 10. Tracks and clips

Tracks contain arrangement data. Clips position note/event groups on the song timeline. 4.0 adds optional `track.midi` and `clip.midiEvents` without replacing 3.x notes.

A player that has not implemented 4.0 performance may still render inherited notes after explicitly recognizing 4.0 Compatibility Playback rules.

## 11. Notes

A note targets either a MIDI-like pitch or a sound slot. Optional attributes include velocity, release velocity, enable state, probability, velocity deviation, pitch role, and 4.0 channel.

`pitchRole: fixedTrigger` is for literal trigger keys/keyswitches that must not transpose with musical material.

Probability is not deletion: the note remains authored data. Velocity deviation is not random generation metadata; it is note-local performance variation information with a valid endpoint.

## 12. Sound slots and drum kits

Sound slots let notes target semantic/percussive identities without requiring every sound to be represented as a musical pitch. Drum kits group/describe those mappings. A renderer chooses actual acoustic timbre.

## 13. Lyrics foundation

Lyrics remain document/phrase/unit/alignment based. Alignments associate lyric units with performed notes/times. Primary lyric selection does not delete alternate lyric documents.

4.0 lyric voices add explicit vocal-part relationships on top of this model.

## 14. Resources and extensions

Resources are explicit external/shared asset references. `extensions` remains for third-party or verbatim legacy data, but standard Core information must not be hidden only there.

## 15. Document, provenance, generation, presentation, phrase and choice data

These structures preserve shareable editing/history/display relationships. They are not license to recompute the song on load. Concrete notes and Core timeline remain authoritative for playback.

## 16. MIDI Performance Layer

The 4.0 MIDI layer adds performance intent without converting OSTF into a raw MIDI-file clone.

Notes stay in `notes[]`. Non-note performance messages go in `midiEvents[]`.

Supported event families are Control Change, Channel Mode, Program Change, Pitch Bend, Channel Pressure, and Polyphonic Key Pressure.

## 17. MIDI Channel

Stored channels are 1–16. A single-channel track can provide a channel inherited by child notes/events when omitted. Multi-channel and MPE tracks use concrete channel identity where it matters.

Do not store UI labels such as “Ch. 1” as the value; store the integer.

## 18. Logical MIDI Bus

A logical bus is a portable document-local destination namespace. Each bus has its own channels 1–16. Map it to local hardware/software at playback time.

Do not store names such as “MOTU Port 3” or an Ableton track output as portable Core identity unless that text is merely an authored logical label—not a physical binding.

## 19. Control Change and Channel Mode

Normal CC uses controller 0–119 and value 0–127. Channel Mode uses controllers 120–127 and is a distinct typed event family in the schema.

Common controls remain ordinary CC; the format does not require a closed semantic list for every controller number.

## 20. Bank Select and Program Change

Bank Select uses MIDI-standard CC 0/32 followed by Program Change. Program is stored 0–127. A UI may display 1–128, but the file value remains zero-based.

Avoid a second OSTF-specific bank-number source of truth.

## 21. Pitch Bend

Pitch Bend is preserved as 14-bit `value14`: 0 minimum, 8192 center, 16383 maximum. Do not reduce it to 7-bit.

Pitch-bend range/sensitivity is a separate concept and, where represented through MIDI 1.0 semantics, can be preserved through RPN CC sequences.

## 22. Pressure

Channel Pressure applies to the channel. Polyphonic Key Pressure includes a pitch and may use `targetNoteId` to bind meaning to a specific authored note.

## 23. RPN and NRPN

4.0 preserves RPN/NRPN as raw standard CC sequences. This maximizes round-trip fidelity and avoids introducing a second proprietary representation.

## 24. `targetNoteId`

`targetNoteId` is the bridge between channel-oriented MIDI messages and OSTF note identity. Use it only when the event semantically targets a concrete note, and keep channel/pitch relationships consistent.

Targeted expression must not leak to unrelated notes if the target note is not scheduled because of probability/enable logic.

## 25. Same-tick order

Default conceptual order is Note Off, MIDI Event, Note On. Explicit event `order` can move an event outside that default slot. Preserve array order for equal-time/equal-order MIDI events.

This matters for program/bank setup, controller reset, bend reset, and other stateful MIDI behavior.

## 26. MPE

MPE is not a new OSTF event family. It is a track mode plus channel allocation and ordinary pitch-bend/pressure/CC events.

Lower and upper zones use their normal master/member concepts. Simultaneous independently expressive notes occupy distinct member channels for overlapping time. `targetNoteId` makes per-note intent explicit in OSTF.

Reusable phrase sources should not accidentally freeze ephemeral MPE member-channel allocation unless that channel identity is intentionally authored as part of the concrete performance.

## 27. Multi-Melody

Use `song.melodyTrackIds` to declare multiple melody-bearing tracks: lead, counter melody, instrumental melody, vocal harmony, etc. Keep `primaryMelodyTrackId` as the single primary declaration.

This is metadata/relationship information, not extra playback scheduling.

## 28. Multi-Voice lyrics

Use `lyrics.voices` to relate vocal roles to track and lyric document. Multiple voices can share a document when singing the same text. Separate documents model call/response or genuinely different text.

Voice-specific alignments use `voiceId` to keep identical text/note structures unambiguous.

## 29. Reader behavior

Readers should be strict about structural corruption and conservative about authored unknown strings. Never silently normalize unknown genre/mode/role names into a familiar value.

A 4.0-aware but feature-incomplete player can use Compatibility Playback. A 3.x-only parser that rejects `version:4.0.0` is not required to accept it.

## 30. Writer behavior

Writers must emit schema-valid and semantically valid data. They must not invent missing optional information merely to populate UI defaults. They must not drop unsupported 4.0 data silently.

Prefer atomic writes and revalidation before replacing the original file.

## 31. Delete/move/merge safety

Deletion must account for incoming references. Track moves preserve IDs. Independent copies get new IDs. Merging track-owned performance data includes note channels, MIDI events, and track MIDI settings; shared logical buses remain shared document resources.

Do not auto-map two logical buses solely because they share a display name.

## 32. 3.5 → 4.0 migration

The minimal valid migration need not add any MIDI events. Add melody sets, lyric voices, channels, buses, or MPE only from known source information or explicit author action.

See the dedicated migration guide.

## 33. 4.0 → 3.5 downgrade

Potentially lost information includes channels, logical buses, MIDI events, MPE configuration, melody-set relationships, lyric voices, and voice-specific relationships. Downgrade must be explicitly lossy where applicable.

## 34. Validation layers

Use two layers:

- **Structural validation:** JSON + JSON Schema.
- **Semantic validation:** IDs/references, channel inheritance, bus existence, MPE zone rules, targetNoteId consistency, voice/document/track alignment, timeline sanity, and preservation rules.

Reference demos additionally use musical-quality checks such as section/chord coverage, MPE channel collision detection, target-event timing, and complete vocal alignment.

## 35. Reference example set

The release contains:

- 5 short Canonical Examples;
- 12 independently composed Golden Demo Songs;
- 41 Feature/Boundary Fixtures.

Demo songs show realistic integration. Fixtures show boundary values and unusual semantic cases without polluting musical examples with test-only data.

## 36. Common implementation errors

Do **not**:

- duplicate Note On/Off as proprietary MIDI events;
- store physical MIDI ports as logical-bus truth;
- write UI program 1–128 directly instead of file 0–127;
- quantize 14-bit Pitch Bend to 7-bit;
- create proprietary MPE event types as the only representation;
- create vocal harmony with a fixed chromatic offset without regard to scale/voice leading;
- drop unsupported 4.0 data during save;
- infer IDs from names;
- treat an unknown future version as 4.0.

## 37. Practical use cases

### Simple one-channel piano
One melodic track, one clip or several clips, ordinary notes, optional single-channel MIDI setting. No root `midi` is required unless a logical-bus declaration is useful.

### Multi-timbral track
Use `multiChannel`, explicit note/event channels, and optionally a logical bus.

### External synthesizers
Use meaningful logical-bus labels in OSTF and map them to actual hardware in the host environment.

### MPE synth
Use an MPE track, lower/upper zone configuration, member channels, and ordinary targeted bend/pressure/CC events.

### Lead + harmony vocals
Declare multiple melody tracks plus lyric voices. Shared text can use one lyric document with voice-specific alignments.

### Call & response
Use separate lyric documents when vocal parts sing different text.

### DAW import
Capture concrete notes and known performance messages. Do not invent unavailable routing/state. Preserve stable musical IDs where an importer has deterministic identity mapping.

### SMF export
Resolve clip-local times to absolute ticks, resolve channel inheritance, expand notes to Note On/Off, serialize MIDI events in defined order, and map logical buses to output files/ports according to export policy.

## 38. GitHub release checklist

Before publishing a specification release:

- schema parses as JSON Schema 2020-12;
- all public `.ostf` files validate structurally;
- semantic reference checks pass;
- parameter reference covers every schema definition/property;
- demo coverage matrix covers every 4.0-added Core property;
- Golden demos are structurally musical rather than repeated test sequences;
- compatibility and downgrade policy are documented;
- English and Japanese documentation links are valid;
- release package remains within repository upload constraints.

## 39. Authority order

When documentation appears to conflict:

1. JSON Schema is authoritative for machine-readable structural constraints.
2. Format Specification is authoritative for normative semantics not expressible in schema.
3. Parameter Reference explains every field/definition.
4. Complete Manual explains implementation/use patterns.
5. Examples demonstrate patterns but do not override the schema/specification.

## 40. Complete schema object catalog

The following catalog recursively represents every schema definition, including nested legacy-compatibility `$defs`. Exact conditional logic remains authoritative in the JSON Schema.

### `id`

Type/constraint: **string**. pattern="^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$"

### `nonnegative`

Type/constraint: **integer**. minimum=0; maximum=9007199254740991

### `positive`

Type/constraint: **integer**. minimum=1; maximum=9007199254740991

### `signed`

Type/constraint: **integer**. minimum=-9007199254740991; maximum=9007199254740991

### `pitch`

Type/constraint: **integer**. minimum=0; maximum=127

### `ref`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `type` | enum<section | track | clip | note | chord | scale | soundSlot | drumKit | lyricDocument | lyricPhrase | lyricUnit | lyricAlignment | resource | genre | contributor | credit | alternateTitle | mood | keyChange | phraseTemplate | phraseInstance | clipChoiceGroup | clipCandidate | roleDefinition | generationProcess | provenanceRecord | midiBus | midiEvent | lyricVoice> | yes | enum=["section", "track", "clip", "note", "chord", "scale", "soundSlot", "drumKit", "lyricDocument", "lyricPhrase", "lyricUnit", "lyricAlignment", "resource", "genre", "contributor", "credit", "alternateTitle", "mood", "keyChange", "phraseTemplate", "phraseInstance", "clipChoiceGroup", "clipCandidate", "roleDefinition", "generationProcess", "provenanceRecord", "midiBus", "midiEvent", "lyricVoice"] |
| `id` | id | yes | — |

### `range`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |

### `spelledPitchClass`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `step` | enum<A | B | C | D | E | F | G> | yes | enum=["A", "B", "C", "D", "E", "F", "G"] |
| `alter` | integer | yes | minimum=-2; maximum=2 |

### `song`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `title` | string | yes | — |
| `primaryMelodyTrackId` | id | optional | — |
| `localizedTitles` | localizedNames | optional | — |
| `subtitle` | string | optional | — |
| `description` | string | optional | — |
| `alternateTitles` | array<alternateTitle> | optional | — |
| `credits` | array<credit> | optional | — |
| `genres` | array<genre> | optional | — |
| `key` | key | optional | — |
| `scale` | scaleDefinition | optional | — |
| `languages` | array<string> | optional | uniqueItems=true |
| `primaryLyricDocumentId` | id | optional | — |
| `moods` | array<mood> | optional | — |
| `rights` | rightsStatement | optional | — |
| `melodyTrackIds` | array<id> | optional | uniqueItems=true |

### `section`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `role` | string | optional | — |
| `localizedNames` | localizedNames | optional | — |

### `tempo`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `at` | nonnegative | yes | — |
| `bpm` | number | yes | maximum=1000; exclusiveMinimum=0 |

### `meter`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `at` | nonnegative | yes | — |
| `numerator` | integer | yes | minimum=1; maximum=64 |
| `denominator` | enum<1 | 2 | 4 | 8 | 16 | 32 | 64> | yes | enum=[1, 2, 4, 8, 16, 32, 64] |
| `beatGroups` | array<integer> | optional | minItems=1; maxItems=64 |

### `timeline`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `sections` | array<section> | yes | — |
| `tempoChanges` | array<tempo> | yes | minItems=1 |
| `meterChanges` | array<meter> | yes | minItems=1 |
| `keyChanges` | array<keyChange> | optional | — |
| `duration` | nonnegative | optional | — |
| `barOrigin` | nonnegative | optional | — |

### `chord`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `label` | string | yes | minLength=1 |
| `root` | spelledPitchClass | optional | — |
| `intervals` | array<integer> | optional | minItems=1; maxItems=36; uniqueItems=true; contains=0 |
| `bass` | spelledPitchClass | optional | — |
| `voicing` | voicing | optional | — |

### `scale`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `label` | string | optional | — |
| `tonic` | spelledPitchClass | yes | — |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true; contains=0 |
| `targets` | array<scaleTarget> | optional | minItems=1; uniqueItems=true |

### `harmony`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `chords` | array<chord> | yes | — |
| `scales` | array<scale> | yes | — |

### `instrument`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `name` | string | yes | — |
| `family` | string | optional | — |
| `localizedNames` | localizedNames | optional | — |

### `note`

Concrete Core DAW-note event. The event target is pitch or soundSlotId. Optional 3.5 note-local properties preserve mute, release velocity, probability and velocity variation. pitchRole=fixedTrigger marks a literal MIDI key trigger that must not be musically transposed or analyzed.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `duration` | positive | yes | — |
| `pitch` | pitch | optional | — |
| `soundSlotId` | id | optional | — |
| `velocity` | integer | optional | minimum=1; maximum=127 |
| `enabled` | boolean | optional | default=true |
| `releaseVelocity` | integer | optional | minimum=0; maximum=127 |
| `probability` | number | optional | minimum=0; maximum=1; default=1 |
| `velocityDeviation` | integer | optional | minimum=-126; maximum=126; default=0 |
| `pitchRole` | enum<musical | fixedTrigger> | optional | default="musical"; enum=["musical", "fixedTrigger"] |
| `channel` | midiChannel | optional | — |

### `clip`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | optional | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `enabled` | boolean | optional | default=true |
| `notes` | array<note> | yes | — |
| `localizedNames` | localizedNames | optional | — |
| `midiEvents` | array<midiEvent> | optional | — |

### `track`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `role` | string | optional | — |
| `instrument` | instrument | optional | — |
| `enabled` | boolean | optional | default=true |
| `clips` | array<clip> | yes | — |
| `trackType` | enum<melodic | drum | mixed> | optional | enum=["melodic", "drum", "mixed"] |
| `preferredView` | enum<auto | pianoRoll | drumGrid> | optional | default="auto"; enum=["auto", "pianoRoll", "drumGrid"] |
| `soundSource` | soundSource | optional | — |
| `localizedNames` | localizedNames | optional | — |
| `drumKitId` | id | optional | — |
| `midi` | trackMidi | optional | — |

### `soundSlot`

Document-local stable logical percussion target. Equal roles, names or MIDI note mappings never merge distinct slot IDs. No sample/audio identity is encoded.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `role` | string | optional | — |
| `midiNote` | pitch | optional | — |
| `midiMapping` | midiMapping | optional | — |
| `localizedNames` | localizedNames | optional | — |

### `lyricDocument`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `title` | string | optional | — |
| `language` | string | optional | — |

### `lyricPhrase`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `documentId` | id | yes | — |
| `text` | string | yes | — |
| `pronunciation` | string | optional | — |
| `start` | nonnegative | optional | — |
| `duration` | positive | optional | — |

### `lyricUnit`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `phraseId` | id | yes | — |
| `text` | string | yes | — |
| `pronunciation` | string | optional | — |
| `start` | nonnegative | optional | — |
| `duration` | positive | optional | — |

### `lyricAlignment`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `unitIds` | array<id> | yes | minItems=1; uniqueItems=true |
| `noteIds` | array<id> | yes | minItems=1; uniqueItems=true |
| `voiceId` | id | optional | — |

### `lyrics`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `documents` | array<lyricDocument> | yes | — |
| `phrases` | array<lyricPhrase> | yes | — |
| `units` | array<lyricUnit> | yes | — |
| `alignments` | array<lyricAlignment> | yes | — |
| `voices` | array<lyricVoice> | optional | — |

### `sha256`

Type/constraint: **string**. pattern="^[0-9a-f]{64}$"

### `resource`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `kind` | enum<audio | midi | text | other> | yes | enum=["audio", "midi", "text", "other"] |
| `uri` | string | yes | minLength=1 |
| `name` | string | optional | — |
| `sha256` | sha256 | optional | — |

### `dependencies`

Type/constraint: **oneOf**. —

### `relativeAssignment`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `noteId` | id | yes | — |
| `basis` | enum<chordTone | scaleDegree> | yes | enum=["chordTone", "scaleDegree"] |
| `contextId` | id | yes | — |
| `anchorPitch` | pitch | yes | — |
| `index` | integer | yes | minimum=1; maximum=36 |
| `chromaticOffset` | integer | yes | minimum=-24; maximum=24 |

### `generator`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | string | yes | minLength=1 |
| `version` | string | yes | minLength=1 |

### `generationInput`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `role` | string | yes | minLength=1 |
| `ref` | ref | yes | — |
| `readRange` | range | optional | — |

### `generationProcess`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `generator` | generator | yes | — |
| `inputs` | array<generationInput> | yes | — |
| `outputs` | array<ref> | yes | minItems=1 |
| `targetRange` | range | optional | — |
| `protectedNoteIds` | array<id> | yes | uniqueItems=true |
| `intent` | generationIntent | optional | — |

### `color`

Type/constraint: **string**. pattern="^#[0-9A-Fa-f]{6}$"

### `roleDefinition`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `baseRole` | string | optional | — |

### `roleColorMap`

Type/constraint: **object**. —

### `customRoleColorMap`

Type/constraint: **object**. —

### `objectColor`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `target` | ref | yes | — |
| `color` | color | yes | — |

### `phraseTemplate`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `duration` | positive | yes | — |
| `notes` | array<corePhraseSourceNote> | yes | — |
| `name` | string | optional | — |

### `phraseNoteMapping`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `templateNoteId` | id | yes | — |
| `repeatIndex` | nonnegative | yes | — |
| `noteId` | id | yes | — |

### `phraseInstance`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `templateId` | id | yes | — |
| `clipId` | id | yes | — |
| `repeatCount` | positive | yes | — |
| `transpose` | integer | yes | minimum=-127; maximum=127 |
| `noteMap` | array<phraseNoteMapping> | yes | — |
| `velocityMode` | enum<independent | linked> | optional | default="independent"; enum=["independent", "linked"] |
| `noteStateMode` | enum<independent | linked> | optional | default="independent"; enum=["independent", "linked"] |

### `clipCandidate`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `clipIds` | array<id> | yes | uniqueItems=true |
| `name` | string | optional | — |

### `clipChoiceGroup`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `candidates` | array<clipCandidate> | yes | minItems=1 |
| `selectedCandidateId` | anyOf | yes | — |
| `name` | string | optional | — |

### `midiMapping`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `standard` | enum<gm1 | custom> | yes | enum=["gm1", "custom"] |
| `note` | pitch | yes | — |

### `soundSource`

Optional informational rendering class or opaque destination hint. In OSTF 4.0, track.midi.busId is the Core logical MIDI routing reference; soundSource does not bind a physical device, plugin state or preset.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `kind` | enum<unassigned | gm1 | externalMidi | plugin | hostManaged> | yes | enum=["unassigned", "gm1", "externalMidi", "plugin", "hostManaged"] |
| `identifier` | string | optional | minLength=1 |

### `localizedNames`

Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically.

Type/constraint: **object**. —

### `drumKit`

Optional named ordered membership of global Sound Slot IDs. Does not define plugin preset, sample loading, choke or a kit-local MIDI remapping.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `localizedNames` | localizedNames | optional | — |
| `soundSlotIds` | array<id> | yes | uniqueItems=true |

### `voicing`

Optional informational chord voicing snapshot, not a played Core note collection. Repeated pitches and original array order have meaning for lossless preservation. Per-entry velocity is an optional voicing-preview hint.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `pitches` | array<pitch> | yes | minItems=1 |
| `velocities` | array<anyOf> | optional | minItems=1 |

### `contributor`

One song-local identity; same contributor can receive multiple credit roles. Unicode source name is authoritative.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `kind` | enum<person | organization | other> | optional | enum=["person", "organization", "other"] |
| `localizedNames` | localizedNames | optional | — |

### `credit`

Ordered credit assignment referencing one contributor; role is an open semantic token, not a translated label.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `contributorId` | id | yes | — |
| `role` | string | yes | pattern="^[a-z][a-z0-9.-]*$" |
| `label` | string | optional | — |

### `alternateTitle`

Stable-ID song title alternative; array position and title text do not identify it.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `title` | string | yes | minLength=1 |

### `languageMap32`

Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank.

Type/constraint: **object**. —

### `localizationTarget`

Core target; song is ID-less, all others use global Core ID. No namespace/profile selector.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `kind` | enum<song | alternateTitle | contributor | credit | section | track | clip | soundSlot | drumKit | lyricDocument | resource | phraseTemplate | clipCandidate | clipChoiceGroup | roleDefinition | genre | mood | keyChange | scale | midiBus | lyricVoice> | yes | enum=["song", "alternateTitle", "contributor", "credit", "section", "track", "clip", "soundSlot", "drumKit", "lyricDocument", "resource", "phraseTemplate", "clipCandidate", "clipChoiceGroup", "roleDefinition", "genre", "mood", "keyChange", "scale", "midiBus", "lyricVoice"] |
| `id` | id | optional | — |

### `localizationEntry`

Independent translations for one stable source field. Original source text remains authoritative as fallback.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `target` | localizationTarget | yes | — |
| `field` | string | yes | minLength=1 |
| `values` | languageMap32 | yes | — |

### `genre`

One song-local open genre tag. The label is authored Unicode text and not a predefined controlled taxonomy; ID is stable for metadata merges and localization.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `externalUri` | string | optional | minLength=1 |

### `legacy33`

Type/constraint: **object/constraint**. —

### `legacy33/$defs/id`

Type/constraint: **string**. pattern="^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$"

### `legacy33/$defs/nonnegative`

Type/constraint: **integer**. minimum=0; maximum=9007199254740991

### `legacy33/$defs/positive`

Type/constraint: **integer**. minimum=1; maximum=9007199254740991

### `legacy33/$defs/signed`

Type/constraint: **integer**. minimum=-9007199254740991; maximum=9007199254740991

### `legacy33/$defs/pitch`

Type/constraint: **integer**. minimum=0; maximum=127

### `legacy33/$defs/ref`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `type` | enum<section | track | clip | note | chord | scale | soundSlot | drumKit | lyricDocument | lyricPhrase | lyricUnit | lyricAlignment | resource | genre> | yes | enum=["section", "track", "clip", "note", "chord", "scale", "soundSlot", "drumKit", "lyricDocument", "lyricPhrase", "lyricUnit", "lyricAlignment", "resource", "genre"] |
| `id` | id | yes | — |

### `legacy33/$defs/range`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |

### `legacy33/$defs/spelledPitchClass`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `step` | enum<A | B | C | D | E | F | G> | yes | enum=["A", "B", "C", "D", "E", "F", "G"] |
| `alter` | integer | yes | minimum=-2; maximum=2 |

### `legacy33/$defs/song`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `title` | string | yes | — |
| `primaryMelodyTrackId` | id | optional | — |
| `localizedTitles` | localizedNames | optional | — |
| `subtitle` | string | optional | — |
| `description` | string | optional | — |
| `alternateTitles` | array<alternateTitle> | optional | — |
| `credits` | array<credit> | optional | — |
| `genres` | array<genre> | optional | — |

### `legacy33/$defs/section`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `role` | string | optional | — |
| `localizedNames` | localizedNames | optional | — |

### `legacy33/$defs/tempo`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `at` | nonnegative | yes | — |
| `bpm` | number | yes | maximum=1000; exclusiveMinimum=0 |

### `legacy33/$defs/meter`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `at` | nonnegative | yes | — |
| `numerator` | integer | yes | minimum=1; maximum=64 |
| `denominator` | enum<1 | 2 | 4 | 8 | 16 | 32 | 64> | yes | enum=[1, 2, 4, 8, 16, 32, 64] |

### `legacy33/$defs/timeline`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `sections` | array<section> | yes | — |
| `tempoChanges` | array<tempo> | yes | minItems=1 |
| `meterChanges` | array<meter> | yes | minItems=1 |

### `legacy33/$defs/chord`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `label` | string | yes | minLength=1 |
| `root` | spelledPitchClass | optional | — |
| `intervals` | array<integer> | optional | minItems=1; maxItems=36; uniqueItems=true; contains=0 |
| `bass` | spelledPitchClass | optional | — |
| `voicing` | voicing | optional | — |

### `legacy33/$defs/scale`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `label` | string | optional | — |
| `tonic` | spelledPitchClass | yes | — |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true; contains=0 |

### `legacy33/$defs/harmony`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `chords` | array<chord> | yes | — |
| `scales` | array<scale> | yes | — |

### `legacy33/$defs/instrument`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `name` | string | yes | — |
| `family` | string | optional | — |
| `localizedNames` | localizedNames | optional | — |

### `legacy33/$defs/note`

Concrete Core note; optional velocity is a note-on attribute. Phrase templates use a separate version-frozen sourceNote shape.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `duration` | positive | yes | — |
| `pitch` | pitch | optional | — |
| `soundSlotId` | id | optional | — |
| `velocity` | integer | optional | minimum=1; maximum=127 |

### `legacy33/$defs/clip`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | optional | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `enabled` | boolean | optional | default=true |
| `notes` | array<note> | yes | — |
| `localizedNames` | localizedNames | optional | — |

### `legacy33/$defs/track`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `role` | string | optional | — |
| `instrument` | instrument | optional | — |
| `enabled` | boolean | optional | default=true |
| `clips` | array<clip> | yes | — |
| `trackType` | enum<melodic | drum | mixed> | optional | enum=["melodic", "drum", "mixed"] |
| `preferredView` | enum<auto | pianoRoll | drumGrid> | optional | default="auto"; enum=["auto", "pianoRoll", "drumGrid"] |
| `soundSource` | soundSource | optional | — |
| `localizedNames` | localizedNames | optional | — |
| `drumKitId` | id | optional | — |

### `legacy33/$defs/soundSlot`

Document-local stable logical percussion target. Equal roles, names or MIDI note mappings never merge distinct slot IDs. No sample/audio identity is encoded.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `role` | string | optional | — |
| `midiNote` | pitch | optional | — |
| `midiMapping` | midiMapping | optional | — |
| `localizedNames` | localizedNames | optional | — |

### `legacy33/$defs/lyricDocument`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `title` | string | optional | — |
| `language` | string | optional | — |

### `legacy33/$defs/lyricPhrase`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `documentId` | id | yes | — |
| `text` | string | yes | — |
| `pronunciation` | string | optional | — |
| `start` | nonnegative | optional | — |
| `duration` | positive | optional | — |

### `legacy33/$defs/lyricUnit`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `phraseId` | id | yes | — |
| `text` | string | yes | — |
| `pronunciation` | string | optional | — |
| `start` | nonnegative | optional | — |
| `duration` | positive | optional | — |

### `legacy33/$defs/lyricAlignment`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `unitIds` | array<id> | yes | minItems=1; uniqueItems=true |
| `noteIds` | array<id> | yes | minItems=1; uniqueItems=true |

### `legacy33/$defs/lyrics`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `documents` | array<lyricDocument> | yes | — |
| `phrases` | array<lyricPhrase> | yes | — |
| `units` | array<lyricUnit> | yes | — |
| `alignments` | array<lyricAlignment> | yes | — |

### `legacy33/$defs/sha256`

Type/constraint: **string**. pattern="^[0-9a-f]{64}$"

### `legacy33/$defs/resource`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `kind` | enum<audio | midi | text | other> | yes | enum=["audio", "midi", "text", "other"] |
| `uri` | string | yes | minLength=1 |
| `name` | string | optional | — |
| `sha256` | sha256 | optional | — |

### `legacy33/$defs/dependencies`

Type/constraint: **oneOf**. —

### `legacy33/$defs/extension`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `version` | string | yes | minLength=1 |
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |
| `dependencies` | dependencies | yes | — |
| `data` | object/constraint | yes | — |

### `legacy33/$defs/relativeAssignment`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `noteId` | id | yes | — |
| `basis` | enum<chordTone | scaleDegree> | yes | enum=["chordTone", "scaleDegree"] |
| `contextId` | id | yes | — |
| `anchorPitch` | pitch | yes | — |
| `index` | integer | yes | minimum=1; maximum=36 |
| `chromaticOffset` | integer | yes | minimum=-24; maximum=24 |

### `legacy33/$defs/relativeData`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `assignments` | array<relativeAssignment> | yes | — |

### `legacy33/$defs/generator`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | string | yes | minLength=1 |
| `version` | string | yes | minLength=1 |

### `legacy33/$defs/generationInput`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `role` | string | yes | minLength=1 |
| `ref` | ref | yes | — |
| `readRange` | range | optional | — |

### `legacy33/$defs/generationProcess`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `generator` | generator | yes | — |
| `inputs` | array<generationInput> | yes | — |
| `outputs` | array<ref> | yes | minItems=1 |
| `targetRange` | range | optional | — |
| `protectedNoteIds` | array<id> | yes | uniqueItems=true |
| `vendorExtension` | string | optional | minLength=1 |

### `legacy33/$defs/generationData`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `processes` | array<generationProcess> | yes | — |

### `legacy33/$defs/color`

Type/constraint: **string**. pattern="^#[0-9A-Fa-f]{6}$"

### `legacy33/$defs/roleDefinition`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `baseRole` | string | optional | — |

### `legacy33/$defs/roleColorMap`

Type/constraint: **object**. —

### `legacy33/$defs/customRoleColorMap`

Type/constraint: **object**. —

### `legacy33/$defs/objectColor`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `target` | ref | yes | — |
| `color` | color | yes | — |

### `legacy33/$defs/appearanceData`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `roleDefinitions` | array<roleDefinition> | yes | — |
| `sectionRoleAssignments` | object | yes | — |
| `appearance` | object | yes | — |

### `legacy33/$defs/legacyData`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `sourceVersion` | const `2.3.0` | yes | const="2.3.0" |
| `sourceSha256` | sha256 | yes | — |
| `sourceText` | string | yes | minLength=1 |
| `conversionReport` | object | yes | — |

### `legacy33/$defs/extensions`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `org.ostf.relative-phrase.v1` | object/constraint | optional | — |
| `org.ostf.generation-context.v1` | object/constraint | optional | — |
| `org.ostf.appearance.v1` | object/constraint | optional | — |
| `org.ostf.legacy-import.v1` | object/constraint | optional | — |
| `org.ostf.phrase-library.v1` | object/constraint | optional | — |
| `org.ostf.clip-choices.v1` | object/constraint | optional | — |
| `org.ostf.localization.v1` | object/constraint | optional | — |

### `legacy33/$defs/phraseTemplate`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `duration` | positive | yes | — |
| `notes` | array<phraseSourceNote> | yes | — |
| `name` | string | optional | — |

### `legacy33/$defs/phraseNoteMapping`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `templateNoteId` | id | yes | — |
| `repeatIndex` | nonnegative | yes | — |
| `noteId` | id | yes | — |

### `legacy33/$defs/phraseInstance`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `templateId` | id | yes | — |
| `clipId` | id | yes | — |
| `repeatCount` | positive | yes | — |
| `transpose` | integer | yes | minimum=-127; maximum=127 |
| `noteMap` | array<phraseNoteMapping> | yes | — |

### `legacy33/$defs/phraseLibraryData`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `templates` | array<phraseTemplate> | yes | — |
| `instances` | array<phraseInstance> | yes | — |

### `legacy33/$defs/clipCandidate`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `clipIds` | array<id> | yes | uniqueItems=true |
| `name` | string | optional | — |

### `legacy33/$defs/clipChoiceGroup`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `candidates` | array<clipCandidate> | yes | minItems=1 |
| `selectedCandidateId` | anyOf | yes | — |
| `name` | string | optional | — |

### `legacy33/$defs/clipChoicesData`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `groups` | array<clipChoiceGroup> | yes | — |

### `legacy33/$defs/midiMapping`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `standard` | enum<gm1 | custom> | yes | enum=["gm1", "custom"] |
| `note` | pitch | yes | — |

### `legacy33/$defs/soundSource`

Optional informational rendering class or opaque destination hint. Program Change, Bank Select and plugin presets are not standardized by Core.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `kind` | enum<unassigned | gm1 | externalMidi | plugin | hostManaged> | yes | enum=["unassigned", "gm1", "externalMidi", "plugin", "hostManaged"] |
| `identifier` | string | optional | minLength=1 |

### `legacy33/$defs/localizedNames`

Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically.

Type/constraint: **object**. —

### `legacy33/$defs/drumKit`

Optional named ordered membership of global Sound Slot IDs. Does not define plugin preset, sample loading, choke or a kit-local MIDI remapping.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `localizedNames` | localizedNames | optional | — |
| `soundSlotIds` | array<id> | yes | uniqueItems=true |

### `legacy33/$defs/phraseSourceNote`

Version-frozen 1.0.0-draft.1 phrase source note. Does NOT admit velocity; a linked Core instance may independently set velocity.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `duration` | positive | yes | — |
| `pitch` | pitch | optional | — |
| `soundSlotId` | id | optional | — |

### `legacy33/$defs/voicing`

Optional informational chord voicing snapshot, not a played Core note collection. Repeated pitches and original array order have meaning for lossless preservation. Per-entry velocity is an optional voicing-preview hint.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `pitches` | array<pitch> | yes | minItems=1 |
| `velocities` | array<anyOf> | optional | minItems=1 |

### `legacy33/$defs/contributor`

One song-local identity; same contributor can receive multiple credit roles. Unicode source name is authoritative.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `kind` | enum<person | organization | other> | optional | enum=["person", "organization", "other"] |
| `localizedNames` | localizedNames | optional | — |

### `legacy33/$defs/credit`

Ordered credit assignment referencing one contributor; role is an open semantic token, not a translated label.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `contributorId` | id | yes | — |
| `role` | string | yes | pattern="^[a-z][a-z0-9.-]*$" |
| `label` | string | optional | — |

### `legacy33/$defs/alternateTitle`

Stable-ID song title alternative; array position and title text do not identify it.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `title` | string | yes | minLength=1 |

### `legacy33/$defs/languageMap32`

Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank.

Type/constraint: **object**. —

### `legacy33/$defs/localizationTarget`

Stable target by Core ID or profile-local scoped ID; song is the only ID-less target. No array indexes or mutable names as locators.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `kind` | enum<song | alternateTitle | contributor | credit | section | track | clip | soundSlot | drumKit | lyricDocument | resource | phraseTemplate | clipCandidate | clipChoiceGroup | roleDefinition | genre> | yes | enum=["song", "alternateTitle", "contributor", "credit", "section", "track", "clip", "soundSlot", "drumKit", "lyricDocument", "resource", "phraseTemplate", "clipCandidate", "clipChoiceGroup", "roleDefinition", "genre"] |
| `id` | id | optional | — |
| `profile` | enum<org.ostf.phrase-library.v1 | org.ostf.clip-choices.v1 | org.ostf.appearance.v1> | optional | enum=["org.ostf.phrase-library.v1", "org.ostf.clip-choices.v1", "org.ostf.appearance.v1"] |

### `legacy33/$defs/localizationEntry`

Independent translations for one stable source field. Original source text remains authoritative as fallback.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `target` | localizationTarget | yes | — |
| `field` | string | yes | minLength=1 |
| `values` | languageMap32 | yes | — |

### `legacy33/$defs/localizationData`

Language-map translations indexed by stable kind/id/field, never by names or array positions.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `entries` | array<localizationEntry> | yes | — |

### `legacy33/$defs/genre`

One song-local open genre tag. The label is authored Unicode text and not a predefined controlled taxonomy; ID is stable for metadata merges and localization.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `externalUri` | string | optional | minLength=1 |

### `opaqueDocumentId`

Stable URI or UUID-style identifier for a document lineage. Compared as exact Unicode text; never fetched.

Type/constraint: **string**. minLength=1

### `revisionRef`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `documentId` | opaqueDocumentId | yes | — |
| `revisionId` | opaqueDocumentId | yes | — |
| `sha256` | sha256 | optional | — |

### `software`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `name` | string | yes | minLength=1 |
| `version` | string | optional | minLength=1 |

### `documentInfo`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | opaqueDocumentId | yes | — |
| `revisionId` | opaqueDocumentId | yes | — |
| `parents` | array<revisionRef> | optional | uniqueItems=true |
| `createdBy` | software | optional | — |
| `modifiedBy` | software | optional | — |

### `key`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `state` | enum<tonal | atonal | unspecified> | yes | enum=["tonal", "atonal", "unspecified"] |
| `tonic` | spelledPitchClass | optional | — |
| `mode` | string | optional | minLength=1 |
| `label` | string | optional | — |

### `keyChange`

A change after tick zero; initial key is stored only at song.key. Change at tick 0 is forbidden to prevent dual authority.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `at` | positive | yes | — |
| `key` | key | yes | — |
| `label` | string | optional | — |

### `scaleDefinition`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `tonic` | spelledPitchClass | yes | — |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true; contains=0 |
| `label` | string | optional | — |

### `scaleTarget`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `type` | enum<section | track | clip | chord | note> | yes | enum=["section", "track", "clip", "chord", "note"] |
| `id` | id | yes | — |

### `mood`

Authored descriptive mood tag; not a generator preset or a probability.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `externalUri` | string | optional | minLength=1 |

### `rightsStatement`

Authored rights statement only; no rights or permissions are inferred or granted by the format.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `notice` | string | optional | — |
| `licenseUri` | string | optional | minLength=1 |
| `rightsHolderIds` | array<id> | optional | uniqueItems=true |

### `grooveIntent`

Descriptive generation intent only. Never re-apply to already stored notes. unitTicks is one unswung subdivision; two subdivisions form a pair.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `name` | string | optional | — |
| `unitTicks` | positive | optional | — |
| `longWeight` | integer | optional | minimum=1; maximum=1000000 |
| `shortWeight` | integer | optional | minimum=1; maximum=1000000 |

### `generationIntent`

Portable description of intent, not a promise to reproduce an algorithm. Does not change song.genres or song.moods.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `genreNames` | array<string> | optional | — |
| `moodNames` | array<string> | optional | — |
| `groove` | grooveIntent | optional | — |
| `prompt` | string | optional | — |
| `seed` | string | optional | — |

### `relativePhrases`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |
| `dependencies` | dependencies | yes | — |
| `assignments` | array<relativeAssignment> | yes | — |

### `generation`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |
| `dependencies` | dependencies | yes | — |
| `processes` | array<generationProcess> | yes | — |

### `presentation`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |
| `dependencies` | dependencies | yes | — |
| `roleDefinitions` | array<roleDefinition> | yes | — |
| `sectionRoleAssignments` | object | yes | — |
| `appearance` | object | yes | — |

### `phraseLibrary`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |
| `dependencies` | dependencies | yes | — |
| `templates` | array<phraseTemplate> | yes | — |
| `instances` | array<phraseInstance> | yes | — |

### `clipChoices`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |
| `dependencies` | dependencies | yes | — |
| `groups` | array<clipChoiceGroup> | yes | — |

### `localization`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |
| `dependencies` | dependencies | yes | — |
| `entries` | array<localizationEntry> | yes | — |

### `corePhraseSourceNote`

Template-local source note for phrase materialization. It carries the same note-local 3.5 properties as concrete notes. It is not performed by itself; concrete track notes remain the saved playback events.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `duration` | positive | yes | — |
| `pitch` | pitch | optional | — |
| `soundSlotId` | id | optional | — |
| `velocity` | integer | optional | minimum=1; maximum=127 |
| `enabled` | boolean | optional | default=true |
| `releaseVelocity` | integer | optional | minimum=0; maximum=127 |
| `probability` | number | optional | minimum=0; maximum=1; default=1 |
| `velocityDeviation` | integer | optional | minimum=-126; maximum=126; default=0 |
| `pitchRole` | enum<musical | fixedTrigger> | optional | default="musical"; enum=["musical", "fixedTrigger"] |

### `provenanceSource`

Historical source; hashes refer to exact source bytes, not the current document. No automatic retrieval.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `format` | string | optional | minLength=1 |
| `version` | string | optional | minLength=1 |
| `sha256` | sha256 | optional | — |
| `uri` | string | optional | minLength=1 |
| `documentId` | opaqueDocumentId | optional | — |
| `revisionId` | opaqueDocumentId | optional | — |
| `resourceId` | id | optional | — |

### `provenanceRecord`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `action` | enum<create | import | conversion | edit | transpose | merge | generation | other> | yes | enum=["create", "import", "conversion", "edit", "transpose", "merge", "generation", "other"] |
| `description` | string | optional | — |
| `tool` | software | optional | — |
| `sources` | array<provenanceSource> | optional | — |
| `semitones` | integer | optional | minimum=-127; maximum=127 |
| `beforeKey` | key | optional | — |
| `afterKey` | key | optional | — |

### `provenance`

Non-executable historical assertions; not composer credits and not a substitute for current metadata. Does not prove authorship or operation correctness.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `records` | array<provenanceRecord> | yes | — |

### `midiChannel`

Human-readable MIDI 1.0 channel number 1..16. Wire status-nibble channel = value - 1.

Type/constraint: **integer**. minimum=1; maximum=16

### `midiBus`

Document-local logical MIDI destination. Each bus has its own MIDI channel namespace. It is not a physical OS/USB/DAW port binding.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |

### `midi`

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `buses` | array<midiBus> | yes | — |

### `mpeSettings`

Semantic MPE zone declaration. Lower-zone master is channel 1 and upper-zone master is channel 16; member channels are derived contiguously from memberChannelCount.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `zone` | enum<lower | upper> | yes | enum=["lower", "upper"] |
| `memberChannelCount` | integer | yes | minimum=1; maximum=15 |

### `trackMidi`

Optional MIDI channel/routing interpretation for a track. busId is a logical OSTF bus, never a physical MIDI port.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `busId` | id | optional | — |
| `mode` | enum<singleChannel | multiChannel | mpe> | yes | enum=["singleChannel", "multiChannel", "mpe"] |
| `channel` | midiChannel | optional | — |
| `mpe` | mpeSettings | optional | — |

### `controlChangeEvent`

MIDI 1.0 Control Change. Controllers 120..127 use channelMode instead.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional | default=1 |
| `channel` | midiChannel | optional | — |
| `type` | const `controlChange` | yes | const="controlChange" |
| `targetNoteId` | id | optional | — |
| `controller` | integer | yes | minimum=0; maximum=119 |
| `value` | integer | yes | minimum=0; maximum=127 |

### `channelModeEvent`

MIDI 1.0 Channel Mode message represented separately from ordinary Control Change.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional | default=1 |
| `channel` | midiChannel | optional | — |
| `type` | const `channelMode` | yes | const="channelMode" |
| `controller` | integer | yes | minimum=120; maximum=127 |
| `value` | integer | yes | minimum=0; maximum=127 |

### `programChangeEvent`

MIDI 1.0 Program Change using the wire data value 0..127. UI may display 1..128.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional | default=1 |
| `channel` | midiChannel | optional | — |
| `type` | const `programChange` | yes | const="programChange" |
| `program` | integer | yes | minimum=0; maximum=127 |

### `pitchBendEvent`

MIDI 1.0 14-bit Pitch Bend Change. Bend range is receiver state/RPN data, not encoded in value14.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional | default=1 |
| `channel` | midiChannel | optional | — |
| `type` | const `pitchBend` | yes | const="pitchBend" |
| `targetNoteId` | id | optional | — |
| `value14` | integer | yes | minimum=0; maximum=16383 |

### `channelPressureEvent`

MIDI 1.0 Channel Pressure (channel aftertouch).

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional | default=1 |
| `channel` | midiChannel | optional | — |
| `type` | const `channelPressure` | yes | const="channelPressure" |
| `targetNoteId` | id | optional | — |
| `value` | integer | yes | minimum=0; maximum=127 |

### `polyPressureEvent`

MIDI 1.0 Polyphonic Key Pressure (poly aftertouch).

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional | default=1 |
| `channel` | midiChannel | optional | — |
| `type` | const `polyPressure` | yes | const="polyPressure" |
| `targetNoteId` | id | optional | — |
| `pitch` | pitch | yes | — |
| `value` | integer | yes | minimum=0; maximum=127 |

### `midiEvent`

Type/constraint: **oneOf**. —

### `lyricVoice`

Explicit relationship between a vocal/melody track and a lyric document. Several voices may reference the same lyric document without duplicating text.

| Field | Type | Required | Main constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | optional | — |
| `role` | string | optional | minLength=1 |
| `trackId` | id | yes | — |
| `documentId` | id | yes | — |

Catalog coverage: **156 recursive definitions**, **207 unique property names**.

## 41. Final principle

OSTF should preserve authored musical meaning without pretending to be the DAW, synthesizer, or hardware environment that renders it. 4.0 extends expressive interchange while keeping that boundary explicit.
