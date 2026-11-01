# OSTF 4.0.0 — Complete Parameter Reference

> English edition. Japanese: [`PARAMETER_REFERENCE.ja.md`](PARAMETER_REFERENCE.ja.md)


The JSON Schema is authoritative for types, required fields, ranges, and structural constraints. Semantic cross-field rules are authoritative in [`FORMAT_SPECIFICATION.md`](FORMAT_SPECIFICATION.md).

This English reference recursively enumerates the root object, all top-level `$defs`, and every nested `$defs` used for legacy/backward-compatibility structures.

## Root

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `format` | const `OSTF` | yes | const="OSTF" |  |
| `version` | const `4.0.0` | yes | const="4.0.0" |  |
| `ppq` | integer | yes | minimum=1; maximum=960000 |  |
| `song` | song | yes | — |  |
| `timeline` | timeline | yes | — |  |
| `harmony` | harmony | yes | — |  |
| `tracks` | array<track> | yes | — |  |
| `soundSlots` | array<soundSlot> | yes | — |  |
| `lyrics` | lyrics | yes | — |  |
| `resources` | array<resource> | yes | — |  |
| `extensions` | extensions | optional | — | Optional third-party or verbatim legacy compatibility payloads. Standard Core always remains authoritative. New first-party common information must not be stored only here. |
| `drumKits` | array<drumKit> | optional | — | Optional multiple percussion-kit definitions; absent = no declared kits (3.0 compatible). |
| `contributors` | array<contributor> | optional | — | Optional unordered identity records referenced by song.credits. No contributor limit is imposed by this format. |
| `document` | documentInfo | optional | — |  |
| `relativePhrases` | relativePhrases | optional | — |  |
| `generation` | generation | optional | — |  |
| `presentation` | presentation | optional | — |  |
| `phraseLibrary` | phraseLibrary | optional | — |  |
| `clipChoices` | clipChoices | optional | — |  |
| `localization` | localization | optional | — |  |
| `provenance` | provenance | optional | — |  |
| `midi` | midi | optional | — | Optional document-level logical MIDI bus declarations. No physical device or DAW port assignment is encoded. |

## `id`

```json
{
  "type": "string",
  "pattern": "^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$",
  "not": {
    "pattern": "[\\r\\n\\u2028\\u2029]"
  }
}
```

Structural condition blocks present: `not`. Consult the schema for exact machine-readable logic.


## `nonnegative`

```json
{
  "type": "integer",
  "minimum": 0,
  "maximum": 9007199254740991
}
```

## `positive`

```json
{
  "type": "integer",
  "minimum": 1,
  "maximum": 9007199254740991
}
```

## `signed`

```json
{
  "type": "integer",
  "minimum": -9007199254740991,
  "maximum": 9007199254740991
}
```

## `pitch`

```json
{
  "type": "integer",
  "minimum": 0,
  "maximum": 127
}
```

## `ref`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `type` | enum<section | track | clip | note | chord | scale | soundSlot | drumKit | lyricDocument | lyricPhrase | lyricUnit | lyricAlignment | resource | genre | contributor | credit | alternateTitle | mood | keyChange | phraseTemplate | phraseInstance | clipChoiceGroup | clipCandidate | roleDefinition | generationProcess | provenanceRecord | midiBus | midiEvent | lyricVoice> | yes | enum=["section", "track", "clip", "note", "chord", "scale", "soundSlot", "drumKit", "lyricDocument", "lyricPhrase", "lyricUnit", "lyricAlignment", "resource", "genre", "contributor", "credit", "alternateTitle", "mood", "keyChange", "phraseTemplate", "phraseInstance", "clipChoiceGroup", "clipCandidate", "roleDefinition", "generationProcess", "provenanceRecord", "midiBus", "midiEvent", "lyricVoice"] |  |
| `id` | id | yes | — |  |

## `range`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |

## `spelledPitchClass`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `step` | enum<A | B | C | D | E | F | G> | yes | enum=["A", "B", "C", "D", "E", "F", "G"] |  |
| `alter` | integer | yes | minimum=-2; maximum=2 |  |

## `song`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `title` | string | yes | — |  |
| `primaryMelodyTrackId` | id | optional | — |  |
| `localizedTitles` | localizedNames | optional | — | Optional localized song-title alternatives. song.title is the stable fallback. |
| `subtitle` | string | optional | — | Optional original-language subtitle. |
| `description` | string | optional | — | Optional original-language description, including Unicode and line breaks. |
| `alternateTitles` | array<alternateTitle> | optional | — | Additional stable-ID source-language titles. |
| `credits` | array<credit> | optional | — | Ordered credit assignments to contributors. Multiple roles and duplicate contributors permitted. |
| `genres` | array<genre> | optional | — | Optional open-ended ordered genre tags; zero or more allowed. Order is authored display preference, first item is primary if a primary label is desired, not a generator weight. Absent means unspecified. |
| `key` | key | optional | — |  |
| `scale` | scaleDefinition | optional | — |  |
| `languages` | array<string> | optional | uniqueItems=true |  |
| `primaryLyricDocumentId` | id | optional | — |  |
| `moods` | array<mood> | optional | — |  |
| `rights` | rightsStatement | optional | — |  |
| `melodyTrackIds` | array<id> | optional | uniqueItems=true | Optional ordered set of tracks explicitly authored as melody-bearing tracks. If present with primaryMelodyTrackId, the primary track must be a member. |

## `section`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | minLength=1 |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `role` | string | optional | — |  |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |

## `tempo`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `at` | nonnegative | yes | — |  |
| `bpm` | number | yes | maximum=1000; exclusiveMinimum=0 |  |

## `meter`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `at` | nonnegative | yes | — |  |
| `numerator` | integer | yes | minimum=1; maximum=64 |  |
| `denominator` | enum<1 | 2 | 4 | 8 | 16 | 32 | 64> | yes | enum=[1, 2, 4, 8, 16, 32, 64] |  |
| `beatGroups` | array<integer> | optional | minItems=1; maxItems=64 | Optional additive grouping in denominator units. Values must sum to numerator. Descriptive only; does not change note timestamps. |

## `timeline`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `sections` | array<section> | yes | — |  |
| `tempoChanges` | array<tempo> | yes | minItems=1 |  |
| `meterChanges` | array<meter> | yes | minItems=1 |  |
| `keyChanges` | array<keyChange> | optional | — |  |
| `duration` | nonnegative | optional | — |  |
| `barOrigin` | nonnegative | optional | — |  |

## `chord`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `label` | string | yes | minLength=1 |  |
| `root` | spelledPitchClass | optional | — |  |
| `intervals` | array<integer> | optional | minItems=1; maxItems=36; uniqueItems=true; contains=0 |  |
| `bass` | spelledPitchClass | optional | — |  |
| `voicing` | voicing | optional | — | Optional pitch snapshot (not an arrangement note sequence). No automatic playback. |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `scale`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `label` | string | optional | — |  |
| `tonic` | spelledPitchClass | yes | — |  |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true; contains=0 |  |
| `targets` | array<scaleTarget> | optional | minItems=1; uniqueItems=true | Optional explicit scope. Absence means general timed context, not a global key. Overlapping contexts are not resolved by array order. |

## `harmony`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `chords` | array<chord> | yes | — |  |
| `scales` | array<scale> | yes | — |  |

## `instrument`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `name` | string | yes | — |  |
| `family` | string | optional | — |  |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |

## `note`

Concrete Core DAW-note event. The event target is pitch or soundSlotId. Optional 3.5 note-local properties preserve mute, release velocity, probability and velocity variation. pitchRole=fixedTrigger marks a literal MIDI key trigger that must not be musically transposed or analyzed.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `duration` | positive | yes | — | Positive note gate in ticks, not the guaranteed audible duration of a one-shot percussion sample. |
| `pitch` | pitch | optional | — |  |
| `soundSlotId` | id | optional | — | Document-local Sound Slot ID. Distinct events and IDs stay distinct even if several slots share a MIDI key; a renderer decides the acoustic timbre. |
| `velocity` | integer | optional | minimum=1; maximum=127 | Optional authored note-on velocity for performed Core notes, 1–127; omit to mean unspecified. It is not a note-off velocity, controller stream, chord tone, or a sound-rendering guarantee. Never insert an inferred default when saving. |
| `enabled` | boolean | optional | default=true | Optional note-local enable state. Omitted means enabled. False preserves the note but suppresses scheduling/playback; it is distinct from probability=0. |
| `releaseVelocity` | integer | optional | minimum=0; maximum=127 | Optional MIDI Note Off/release velocity, 0-127. Omit when unspecified; readers must not invent and write back a default. |
| `probability` | number | optional | minimum=0; maximum=1; default=1 | Optional note-playback probability from 0.0 to 1.0. Omitted means 1.0. Random sequence identity is not standardized; materialize notes for deterministic interchange. |
| `velocityDeviation` | integer | optional | minimum=-126; maximum=126; default=0 | Optional note-local velocity variation endpoint relative to velocity. Requires velocity. Semantic rule: velocity + velocityDeviation must remain within 1..127. Omitted means 0. |
| `pitchRole` | enum<musical | fixedTrigger> | optional | default="musical"; enum=["musical", "fixedTrigger"] | Meaning of a pitch note. musical participates in pitch/harmony/transpose operations; fixedTrigger is an exact MIDI key trigger (for example a keyswitch) and is never transposed or used as musical-pitch evidence. Allowed only on pitch notes. |
| `channel` | midiChannel | optional | — | Optional concrete MIDI channel for this performed note. In singleChannel mode it may be omitted and inherited from the track; in multiChannel/MPE mode semantic rules require explicit channel where channel identity matters. |

Structural condition blocks present: `oneOf`, `allOf`, `dependentRequired`. Consult the schema for exact machine-readable logic.


## `clip`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | optional | — |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `enabled` | boolean | optional | default=true |  |
| `notes` | array<note> | yes | — |  |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |
| `midiEvents` | array<midiEvent> | optional | — | Optional clip-local MIDI performance/control events. start is local to the clip and may be negative if absolute time remains nonnegative. Notes remain authoritative in notes[]. |

## `track`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | — |  |
| `role` | string | optional | — |  |
| `instrument` | instrument | optional | — |  |
| `enabled` | boolean | optional | default=true |  |
| `clips` | array<clip> | yes | — |  |
| `trackType` | enum<melodic | drum | mixed> | optional | enum=["melodic", "drum", "mixed"] | Semantic event-target category; optional; missing/empty-note tracks may be unknown. It is not a DAW track class. |
| `preferredView` | enum<auto | pianoRoll | drumGrid> | optional | default="auto"; enum=["auto", "pianoRoll", "drumGrid"] | Non-binding presentation preference. Does not change note meaning. |
| `soundSource` | soundSource | optional | — | Optional sound-source selection hint, not a plug-in state, preset blob, or sound guarantee. |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |
| `drumKitId` | id | optional | — | Optional reference to a declared drumKit; only for a drum/mixed or empty inferred track. All stored slot events in the track must belong to the referenced kit. |
| `midi` | trackMidi | optional | — |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `soundSlot`

Document-local stable logical percussion target. Equal roles, names or MIDI note mappings never merge distinct slot IDs. No sample/audio identity is encoded.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | — |  |
| `role` | string | optional | — | Optional free-text musical role; not a standardized GM instrument code or an acoustic sample identifier. |
| `midiNote` | pitch | optional | — | Legacy 3.0 MIDI note hint only; not a GM1 declaration or preview timbre identity. |
| `midiMapping` | midiMapping | optional | — | Explicit external MIDI1 output mapping; standard=gm1 is separate from custom even for an equal note number. Not a unique slot or audio identity. |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |

## `lyricDocument`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `title` | string | optional | — |  |
| `language` | string | optional | — |  |

## `lyricPhrase`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `documentId` | id | yes | — |  |
| `text` | string | yes | — |  |
| `pronunciation` | string | optional | — |  |
| `start` | nonnegative | optional | — |  |
| `duration` | positive | optional | — |  |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `lyricUnit`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `phraseId` | id | yes | — |  |
| `text` | string | yes | — |  |
| `pronunciation` | string | optional | — |  |
| `start` | nonnegative | optional | — |  |
| `duration` | positive | optional | — |  |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `lyricAlignment`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `unitIds` | array<id> | yes | minItems=1; uniqueItems=true |  |
| `noteIds` | array<id> | yes | minItems=1; uniqueItems=true |  |
| `voiceId` | id | optional | — | Optional lyricVoice association. When present, aligned units must belong to that voice document and aligned notes must belong to that voice track. |

## `lyrics`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `documents` | array<lyricDocument> | yes | — |  |
| `phrases` | array<lyricPhrase> | yes | — |  |
| `units` | array<lyricUnit> | yes | — |  |
| `alignments` | array<lyricAlignment> | yes | — |  |
| `voices` | array<lyricVoice> | optional | — | Optional explicit vocal-part relationships for multi-voice, harmony and chorus lyrics. |

## `sha256`

```json
{
  "type": "string",
  "pattern": "^[0-9a-f]{64}$",
  "not": {
    "pattern": "[\\r\\n\\u2028\\u2029]"
  }
}
```

Structural condition blocks present: `not`. Consult the schema for exact machine-readable logic.


## `resource`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `kind` | enum<audio | midi | text | other> | yes | enum=["audio", "midi", "text", "other"] |  |
| `uri` | string | yes | minLength=1 |  |
| `name` | string | optional | — |  |
| `sha256` | sha256 | optional | — |  |

## `dependencies`

```json
{
  "oneOf": [
    {
      "type": "object",
      "properties": {
        "scope": {
          "enum": [
            "document",
            "none"
          ]
        }
      },
      "required": [
        "scope"
      ],
      "additionalProperties": false
    },
    {
      "type": "object",
      "properties": {
        "scope": {
          "const": "objects"
        },
        "refs": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/ref"
          },
          "minItems": 1,
          "uniqueItems": true
        }
      },
      "required": [
        "scope",
        "refs"
      ],
      "additionalProperties": false
    }
  ]
}
```

Structural condition blocks present: `oneOf`. Consult the schema for exact machine-readable logic.


## `relativeAssignment`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `noteId` | id | yes | — |  |
| `basis` | enum<chordTone | scaleDegree> | yes | enum=["chordTone", "scaleDegree"] |  |
| `contextId` | id | yes | — |  |
| `anchorPitch` | pitch | yes | — |  |
| `index` | integer | yes | minimum=1; maximum=36 |  |
| `chromaticOffset` | integer | yes | minimum=-24; maximum=24 |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `generator`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | string | yes | minLength=1 |  |
| `version` | string | yes | minLength=1 |  |

## `generationInput`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `role` | string | yes | minLength=1 |  |
| `ref` | ref | yes | — |  |
| `readRange` | range | optional | — |  |

## `generationProcess`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `generator` | generator | yes | — |  |
| `inputs` | array<generationInput> | yes | — |  |
| `outputs` | array<ref> | yes | minItems=1 |  |
| `targetRange` | range | optional | — |  |
| `protectedNoteIds` | array<id> | yes | uniqueItems=true |  |
| `intent` | generationIntent | optional | — |  |

## `color`

```json
{
  "type": "string",
  "pattern": "^#[0-9A-Fa-f]{6}$",
  "not": {
    "pattern": "[\\r\\n\\u2028\\u2029]"
  }
}
```

Structural condition blocks present: `not`. Consult the schema for exact machine-readable logic.


## `roleDefinition`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | minLength=1 |  |
| `baseRole` | string | optional | — |  |

## `roleColorMap`

```json
{
  "type": "object",
  "additionalProperties": {
    "$ref": "#/$defs/color"
  }
}
```

## `customRoleColorMap`

```json
{
  "type": "object",
  "propertyNames": {
    "$ref": "#/$defs/id"
  },
  "additionalProperties": {
    "$ref": "#/$defs/color"
  }
}
```

## `objectColor`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `target` | ref | yes | — |  |
| `color` | color | yes | — |  |

## `phraseTemplate`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `duration` | positive | yes | — |  |
| `notes` | array<corePhraseSourceNote> | yes | — |  |
| `name` | string | optional | — |  |

## `phraseNoteMapping`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `templateNoteId` | id | yes | — |  |
| `repeatIndex` | nonnegative | yes | — |  |
| `noteId` | id | yes | — |  |

## `phraseInstance`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `templateId` | id | yes | — |  |
| `clipId` | id | yes | — |  |
| `repeatCount` | positive | yes | — |  |
| `transpose` | integer | yes | minimum=-127; maximum=127 |  |
| `noteMap` | array<phraseNoteMapping> | yes | — |  |
| `velocityMode` | enum<independent | linked> | optional | default="independent"; enum=["independent", "linked"] | independent preserves concrete velocities on refresh; linked requires exact presence/value equality with the template. This field does not trigger refresh on load. |
| `noteStateMode` | enum<independent | linked> | optional | default="independent"; enum=["independent", "linked"] | Controls enabled/releaseVelocity/probability/velocityDeviation after materialization. independent preserves concrete note-state values on refresh; linked requires exact presence/value equality with the template. pitchRole is structural and must always remain semantically equal between a source pitch note and its mapped concrete note. |

## `clipCandidate`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `clipIds` | array<id> | yes | uniqueItems=true |  |
| `name` | string | optional | — |  |

## `clipChoiceGroup`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `candidates` | array<clipCandidate> | yes | minItems=1 |  |
| `selectedCandidateId` | anyOf | yes | — |  |
| `name` | string | optional | — |  |

## `midiMapping`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `standard` | enum<gm1 | custom> | yes | enum=["gm1", "custom"] | gm1=GM Level 1 percussion key 35..81; custom=explicit arbitrary MIDI1 note number. |
| `note` | pitch | yes | — | MIDI1 note number in 0..127; for gm1 restrict to 35..81. |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `soundSource`

Optional informational rendering class or opaque destination hint. In OSTF 4.0, track.midi.busId is the Core logical MIDI routing reference; soundSource does not bind a physical device, plugin state or preset.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `kind` | enum<unassigned | gm1 | externalMidi | plugin | hostManaged> | yes | enum=["unassigned", "gm1", "externalMidi", "plugin", "hostManaged"] |  |
| `identifier` | string | optional | minLength=1 | Opaque informational target identifier (for example a URI or plug-in identifier), not a physical MIDI-port binding or loading command. |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `localizedNames`

Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically.

```json
{
  "type": "object",
  "minProperties": 1,
  "propertyNames": {
    "type": "string",
    "pattern": "^(?:[A-Za-z]{2,3}|[A-Za-z]{5,8})(?:-[A-Za-z0-9]{2,8})*$"
  },
  "additionalProperties": {
    "type": "string",
    "minLength": 1
  },
  "description": "Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically."
}
```

## `drumKit`

Optional named ordered membership of global Sound Slot IDs. Does not define plugin preset, sample loading, choke or a kit-local MIDI remapping.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | — |  |
| `localizedNames` | localizedNames | optional | — |  |
| `soundSlotIds` | array<id> | yes | uniqueItems=true | Ordered, unique references to global soundSlots; may be empty. |

## `voicing`

Optional informational chord voicing snapshot, not a played Core note collection. Repeated pitches and original array order have meaning for lossless preservation. Per-entry velocity is an optional voicing-preview hint.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `pitches` | array<pitch> | yes | minItems=1 | Ordered multiset of concrete absolute pitches, 0-127. Duplicates allowed. |
| `velocities` | array<anyOf> | optional | minItems=1 | Optional positional velocities aligned one-to-one to pitches. Null means unspecified at this index, not 0. Exact equal-length rule is semantic. |

## `contributor`

One song-local identity; same contributor can receive multiple credit roles. Unicode source name is authoritative.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | minLength=1 |  |
| `kind` | enum<person | organization | other> | optional | enum=["person", "organization", "other"] |  |
| `localizedNames` | localizedNames | optional | — |  |

## `credit`

Ordered credit assignment referencing one contributor; role is an open semantic token, not a translated label.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `contributorId` | id | yes | — |  |
| `role` | string | yes | pattern="^[a-z][a-z0-9.-]*$" |  |
| `label` | string | optional | — |  |

## `alternateTitle`

Stable-ID song title alternative; array position and title text do not identify it.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `title` | string | yes | minLength=1 |  |

## `languageMap32`

Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank.

```json
{
  "type": "object",
  "minProperties": 1,
  "propertyNames": {
    "type": "string",
    "pattern": "^[A-Za-z]{1,8}(?:-[A-Za-z0-9]{1,8})*$"
  },
  "additionalProperties": {
    "type": "string"
  },
  "description": "Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank."
}
```

## `localizationTarget`

Core target; song is ID-less, all others use global Core ID. No namespace/profile selector.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `kind` | enum<song | alternateTitle | contributor | credit | section | track | clip | soundSlot | drumKit | lyricDocument | resource | phraseTemplate | clipCandidate | clipChoiceGroup | roleDefinition | genre | mood | keyChange | scale | midiBus | lyricVoice> | yes | enum=["song", "alternateTitle", "contributor", "credit", "section", "track", "clip", "soundSlot", "drumKit", "lyricDocument", "resource", "phraseTemplate", "clipCandidate", "clipChoiceGroup", "roleDefinition", "genre", "mood", "keyChange", "scale", "midiBus", "lyricVoice"] |  |
| `id` | id | optional | — |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `localizationEntry`

Independent translations for one stable source field. Original source text remains authoritative as fallback.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `target` | localizationTarget | yes | — |  |
| `field` | string | yes | minLength=1 | Exact allowed source text field for this target kind (see normative matrix). No arbitrary path or musical value fields. |
| `values` | languageMap32 | yes | — |  |

## `genre`

One song-local open genre tag. The label is authored Unicode text and not a predefined controlled taxonomy; ID is stable for metadata merges and localization.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — | Stable song-local ID unique among all Core objects. |
| `name` | string | yes | minLength=1 | Original Unicode genre label, never normalized or rewritten during import. |
| `externalUri` | string | optional | minLength=1 | Optional external taxonomy URI hint; never authoritative for a local genre label, and never auto-dereferenced. |

## `legacy33`

```json
{
  "$defs": {
    "id": {
      "type": "string",
      "pattern": "^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$",
      "not": {
        "pattern": "[\\r\\n\\u2028\\u2029]"
      }
    },
    "nonnegative": {
      "type": "integer",
      "minimum": 0,
      "maximum": 9007199254740991
    },
    "positive": {
      "type": "integer",
      "minimum": 1,
      "maximum": 9007199254740991
    },
    "signed": {
      "type": "integer",
      "minimum": -9007199254740991,
      "maximum": 9007199254740991
    },
    "pitch": {
      "type": "integer",
      "minimum": 0,
      "maximum": 127
    },
    "ref": {
      "type": "object",
      "properties": {
        "type": {
          "enum": [
            "section",
            "track",
            "clip",
            "note",
            "chord",
            "scale",
            "soundSlot",
            "drumKit",
            "lyricDocument",
            "lyricPhrase",
            "lyricUnit",
            "lyricAlignment",
            "resource",
            "genre"
          ]
        },
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        }
      },
      "required": [
        "type",
        "id"
      ],
      "additionalProperties": false
    },
    "range": {
      "type": "object",
      "properties": {
        "start": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        }
      },
      "required": [
        "start",
        "duration"
      ],
      "additionalProperties": false
    },
    "spelledPitchClass": {
      "type": "object",
      "properties": {
        "step": {
          "enum": [
            "A",
            "B",
            "C",
            "D",
            "E",
            "F",
            "G"
          ]
        },
        "alter": {
          "type": "integer",
          "minimum": -2,
          "maximum": 2
        }
      },
      "required": [
        "step",
        "alter"
      ],
      "additionalProperties": false
    },
    "song": {
      "type": "object",
      "properties": {
        "title": {
          "type": "string"
        },
        "primaryMelodyTrackId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "localizedTitles": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames",
          "description": "Optional localized song-title alternatives. song.title is the stable fallback."
        },
        "subtitle": {
          "type": "string",
          "description": "Optional original-language subtitle."
        },
        "description": {
          "type": "string",
          "description": "Optional original-language description, including Unicode and line breaks."
        },
        "alternateTitles": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/alternateTitle"
          },
          "description": "Additional stable-ID source-language titles."
        },
        "credits": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/credit"
          },
          "description": "Ordered credit assignments to contributors. Multiple roles and duplicate contributors permitted."
        },
        "genres": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/genre"
          },
          "description": "Optional open-ended ordered genre tags; zero or more allowed. Order is authored display preference, first item is primary if a primary label is desired, not a generator weight. Absent means unspecified."
        }
      },
      "required": [
        "title"
      ],
      "additionalProperties": false
    },
    "section": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "name": {
          "type": "string",
          "minLength": 1
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        },
        "role": {
          "type": "string"
        },
        "localizedNames": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames",
          "description": "Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity."
        }
      },
      "required": [
        "id",
        "name",
        "start",
        "duration"
      ],
      "additionalProperties": false
    },
    "tempo": {
      "type": "object",
      "properties": {
        "at": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "bpm": {
          "type": "number",
          "exclusiveMinimum": 0,
          "maximum": 1000
        }
      },
      "required": [
        "at",
        "bpm"
      ],
      "additionalProperties": false
    },
    "meter": {
      "type": "object",
      "properties": {
        "at": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "numerator": {
          "type": "integer",
          "minimum": 1,
          "maximum": 64
        },
        "denominator": {
          "enum": [
            1,
            2,
            4,
            8,
            16,
            32,
            64
          ],
          "type": "integer"
        }
      },
      "required": [
        "at",
        "numerator",
        "denominator"
      ],
      "additionalProperties": false
    },
    "timeline": {
      "type": "object",
      "properties": {
        "sections": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/section"
          }
        },
        "tempoChanges": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/tempo"
          },
          "minItems": 1
        },
        "meterChanges": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/meter"
          },
          "minItems": 1
        }
      },
      "required": [
        "sections",
        "tempoChanges",
        "meterChanges"
      ],
      "additionalProperties": false
    },
    "chord": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        },
        "label": {
          "type": "string",
          "minLength": 1
        },
        "root": {
          "$ref": "#/$defs/legacy33/$defs/spelledPitchClass"
        },
        "intervals": {
          "type": "array",
          "items": {
            "type": "integer",
            "minimum": 0,
            "maximum": 35
          },
          "minItems": 1,
          "maxItems": 36,
          "uniqueItems": true,
          "contains": {
            "const": 0
          }
        },
        "bass": {
          "$ref": "#/$defs/legacy33/$defs/spelledPitchClass"
        },
        "voicing": {
          "$ref": "#/$defs/legacy33/$defs/voicing",
          "description": "Optional pitch snapshot (not an arrangement note sequence). No automatic playback."
        }
      },
      "required": [
        "id",
        "start",
        "duration",
        "label"
      ],
      "additionalProperties": false,
      "dependentRequired": {
        "root": [
          "intervals"
        ],
        "intervals": [
          "root"
        ]
      }
    },
    "scale": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        },
        "label": {
          "type": "string"
        },
        "tonic": {
          "$ref": "#/$defs/legacy33/$defs/spelledPitchClass"
        },
        "intervals": {
          "type": "array",
          "items": {
            "type": "integer",
            "minimum": 0,
            "maximum": 11
          },
          "minItems": 1,
          "maxItems": 12,
          "uniqueItems": true,
          "contains": {
            "const": 0
          }
        }
      },
      "required": [
        "id",
        "start",
        "duration",
        "tonic",
        "intervals"
      ],
      "additionalProperties": false
    },
    "harmony": {
      "type": "object",
      "properties": {
        "chords": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/chord"
          }
        },
        "scales": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/scale"
          }
        }
      },
      "required": [
        "chords",
        "scales"
      ],
      "additionalProperties": false
    },
    "instrument": {
      "type": "object",
      "properties": {
        "name": {
          "type": "string"
        },
        "family": {
          "type": "string"
        },
        "localizedNames": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames",
          "description": "Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity."
        }
      },
      "required": [
        "name"
      ],
      "additionalProperties": false
    },
    "note": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/signed"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive",
          "description": "Positive note gate in ticks, not the guaranteed audible duration of a one-shot percussion sample."
        },
        "pitch": {
          "$ref": "#/$defs/legacy33/$defs/pitch"
        },
        "soundSlotId": {
          "$ref": "#/$defs/legacy33/$defs/id",
          "description": "Document-local Sound Slot ID. Distinct events and IDs stay distinct even if several slots share a MIDI key; a renderer decides the acoustic timbre."
        },
        "velocity": {
          "type": "integer",
          "minimum": 1,
          "maximum": 127,
          "description": "Optional authored note-on velocity for performed Core notes, 1–127; omit to mean unspecified. It is not a note-off velocity, controller stream, chord tone, or a sound-rendering guarantee. Never insert an inferred default when saving."
        }
      },
      "required": [
        "id",
        "start",
        "duration"
      ],
      "additionalProperties": false,
      "oneOf": [
        {
          "required": [
            "pitch"
          ],
          "not": {
            "required": [
              "soundSlotId"
            ]
          }
        },
        {
          "required": [
            "soundSlotId"
          ],
          "not": {
            "required": [
              "pitch"
            ]
          }
        }
      ],
      "description": "Concrete Core note; optional velocity is a note-on attribute. Phrase templates use a separate version-frozen sourceNote shape."
    },
    "clip": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "name": {
          "type": "string"
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        },
        "enabled": {
          "type": "boolean",
          "default": true
        },
        "notes": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/note"
          }
        },
        "localizedNames": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames",
          "description": "Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity."
        }
      },
      "required": [
        "id",
        "start",
        "duration",
        "notes"
      ],
      "additionalProperties": false
    },
    "track": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "name": {
          "type": "string"
        },
        "role": {
          "type": "string"
        },
        "instrument": {
          "$ref": "#/$defs/legacy33/$defs/instrument"
        },
        "enabled": {
          "type": "boolean",
          "default": true
        },
        "clips": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/clip"
          }
        },
        "trackType": {
          "enum": [
            "melodic",
            "drum",
            "mixed"
          ],
          "description": "Semantic event-target category; optional; missing/empty-note tracks may be unknown. It is not a DAW track class."
        },
        "preferredView": {
          "enum": [
            "auto",
            "pianoRoll",
            "drumGrid"
          ],
          "default": "auto",
          "description": "Non-binding presentation preference. Does not change note meaning."
        },
        "soundSource": {
          "$ref": "#/$defs/legacy33/$defs/soundSource",
          "description": "Optional sound-source selection hint, not a plug-in state, preset blob, or sound guarantee."
        },
        "localizedNames": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames",
          "description": "Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity."
        },
        "drumKitId": {
          "$ref": "#/$defs/legacy33/$defs/id",
          "description": "Optional reference to a declared drumKit; only for a drum/mixed or empty inferred track. All stored slot events in the track must belong to the referenced kit."
        }
      },
      "required": [
        "id",
        "name",
        "clips"
      ],
      "additionalProperties": false,
      "allOf": [
        {
          "if": {
            "properties": {
              "trackType": {
                "const": "melodic"
              }
            },
            "required": [
              "trackType"
            ]
          },
          "then": {
            "not": {
              "required": [
                "drumKitId"
              ]
            }
          }
        }
      ]
    },
    "soundSlot": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "name": {
          "type": "string"
        },
        "role": {
          "type": "string",
          "description": "Optional free-text musical role; not a standardized GM instrument code or an acoustic sample identifier."
        },
        "midiNote": {
          "$ref": "#/$defs/legacy33/$defs/pitch",
          "description": "Legacy 3.0 MIDI note hint only; not a GM1 declaration or preview timbre identity."
        },
        "midiMapping": {
          "$ref": "#/$defs/legacy33/$defs/midiMapping",
          "description": "Explicit external MIDI1 output mapping; standard=gm1 is separate from custom even for an equal note number. Not a unique slot or audio identity."
        },
        "localizedNames": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames",
          "description": "Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity."
        }
      },
      "required": [
        "id",
        "name"
      ],
      "additionalProperties": false,
      "description": "Document-local stable logical percussion target. Equal roles, names or MIDI note mappings never merge distinct slot IDs. No sample/audio identity is encoded."
    },
    "lyricDocument": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "title": {
          "type": "string"
        },
        "language": {
          "type": "string"
        }
      },
      "required": [
        "id"
      ],
      "additionalProperties": false
    },
    "lyricPhrase": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "documentId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "text": {
          "type": "string"
        },
        "pronunciation": {
          "type": "string"
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        }
      },
      "required": [
        "id",
        "documentId",
        "text"
      ],
      "additionalProperties": false,
      "dependentRequired": {
        "start": [
          "duration"
        ],
        "duration": [
          "start"
        ]
      }
    },
    "lyricUnit": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "phraseId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "text": {
          "type": "string"
        },
        "pronunciation": {
          "type": "string"
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        }
      },
      "required": [
        "id",
        "phraseId",
        "text"
      ],
      "additionalProperties": false,
      "dependentRequired": {
        "start": [
          "duration"
        ],
        "duration": [
          "start"
        ]
      }
    },
    "lyricAlignment": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "unitIds": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/id"
          },
          "uniqueItems": true,
          "minItems": 1
        },
        "noteIds": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/id"
          },
          "uniqueItems": true,
          "minItems": 1
        }
      },
      "required": [
        "id",
        "unitIds",
        "noteIds"
      ],
      "additionalProperties": false
    },
    "lyrics": {
      "type": "object",
      "properties": {
        "documents": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/lyricDocument"
          }
        },
        "phrases": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/lyricPhrase"
          }
        },
        "units": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/lyricUnit"
          }
        },
        "alignments": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/lyricAlignment"
          }
        }
      },
      "required": [
        "documents",
        "phrases",
        "units",
        "alignments"
      ],
      "additionalProperties": false
    },
    "sha256": {
      "type": "string",
      "pattern": "^[0-9a-f]{64}$",
      "not": {
        "pattern": "[\\r\\n\\u2028\\u2029]"
      }
    },
    "resource": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "kind": {
          "enum": [
            "audio",
            "midi",
            "text",
            "other"
          ]
        },
        "uri": {
          "type": "string",
          "minLength": 1
        },
        "name": {
          "type": "string"
        },
        "sha256": {
          "$ref": "#/$defs/legacy33/$defs/sha256"
        }
      },
      "required": [
        "id",
        "kind",
        "uri"
      ],
      "additionalProperties": false
    },
    "dependencies": {
      "oneOf": [
        {
          "type": "object",
          "properties": {
            "scope": {
              "enum": [
                "document",
                "none"
              ]
            }
          },
          "required": [
            "scope"
          ],
          "additionalProperties": false
        },
        {
          "type": "object",
          "properties": {
            "scope": {
              "const": "objects"
            },
            "refs": {
              "type": "array",
              "items": {
                "$ref": "#/$defs/legacy33/$defs/ref"
              },
              "minItems": 1,
              "uniqueItems": true
            }
          },
          "required": [
            "scope",
            "refs"
          ],
          "additionalProperties": false
        }
      ]
    },
    "extension": {
      "type": "object",
      "properties": {
        "version": {
          "type": "string",
          "minLength": 1
        },
        "status": {
          "enum": [
            "current",
            "stale",
            "unverified"
          ]
        },
        "dependencies": {
          "$ref": "#/$defs/legacy33/$defs/dependencies"
        },
        "data": {}
      },
      "required": [
        "version",
        "status",
        "dependencies",
        "data"
      ],
      "additionalProperties": false
    },
    "relativeAssignment": {
      "type": "object",
      "properties": {
        "noteId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "basis": {
          "enum": [
            "chordTone",
            "scaleDegree"
          ]
        },
        "contextId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "anchorPitch": {
          "$ref": "#/$defs/legacy33/$defs/pitch"
        },
        "index": {
          "type": "integer",
          "minimum": 1,
          "maximum": 36
        },
        "chromaticOffset": {
          "type": "integer",
          "minimum": -24,
          "maximum": 24
        }
      },
      "required": [
        "noteId",
        "basis",
        "contextId",
        "anchorPitch",
        "index",
        "chromaticOffset"
      ],
      "additionalProperties": false,
      "allOf": [
        {
          "if": {
            "properties": {
              "basis": {
                "const": "scaleDegree"
              }
            },
            "required": [
              "basis"
            ]
          },
          "then": {
            "properties": {
              "index": {
                "maximum": 12
              }
            }
          }
        }
      ]
    },
    "relativeData": {
      "type": "object",
      "properties": {
        "assignments": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/relativeAssignment"
          }
        }
      },
      "required": [
        "assignments"
      ],
      "additionalProperties": false
    },
    "generator": {
      "type": "object",
      "properties": {
        "id": {
          "type": "string",
          "minLength": 1
        },
        "version": {
          "type": "string",
          "minLength": 1
        }
      },
      "required": [
        "id",
        "version"
      ],
      "additionalProperties": false
    },
    "generationInput": {
      "type": "object",
      "properties": {
        "role": {
          "type": "string",
          "minLength": 1
        },
        "ref": {
          "$ref": "#/$defs/legacy33/$defs/ref"
        },
        "readRange": {
          "$ref": "#/$defs/legacy33/$defs/range"
        }
      },
      "required": [
        "role",
        "ref"
      ],
      "additionalProperties": false
    },
    "generationProcess": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "generator": {
          "$ref": "#/$defs/legacy33/$defs/generator"
        },
        "inputs": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/generationInput"
          }
        },
        "outputs": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/ref"
          },
          "minItems": 1
        },
        "targetRange": {
          "$ref": "#/$defs/legacy33/$defs/range"
        },
        "protectedNoteIds": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/id"
          },
          "uniqueItems": true
        },
        "vendorExtension": {
          "type": "string",
          "minLength": 1
        }
      },
      "required": [
        "id",
        "generator",
        "inputs",
        "outputs",
        "protectedNoteIds"
      ],
      "additionalProperties": false
    },
    "generationData": {
      "type": "object",
      "properties": {
        "processes": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/generationProcess"
          }
        }
      },
      "required": [
        "processes"
      ],
      "additionalProperties": false
    },
    "color": {
      "type": "string",
      "pattern": "^#[0-9A-Fa-f]{6}$",
      "not": {
        "pattern": "[\\r\\n\\u2028\\u2029]"
      }
    },
    "roleDefinition": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "name": {
          "type": "string",
          "minLength": 1
        },
        "baseRole": {
          "type": "string"
        }
      },
      "required": [
        "id",
        "name"
      ],
      "additionalProperties": false
    },
    "roleColorMap": {
      "type": "object",
      "additionalProperties": {
        "$ref": "#/$defs/legacy33/$defs/color"
      }
    },
    "customRoleColorMap": {
      "type": "object",
      "propertyNames": {
        "$ref": "#/$defs/legacy33/$defs/id"
      },
      "additionalProperties": {
        "$ref": "#/$defs/legacy33/$defs/color"
      }
    },
    "objectColor": {
      "type": "object",
      "properties": {
        "target": {
          "$ref": "#/$defs/legacy33/$defs/ref"
        },
        "color": {
          "$ref": "#/$defs/legacy33/$defs/color"
        }
      },
      "required": [
        "target",
        "color"
      ],
      "additionalProperties": false
    },
    "appearanceData": {
      "type": "object",
      "properties": {
        "roleDefinitions": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/roleDefinition"
          }
        },
        "sectionRoleAssignments": {
          "type": "object",
          "propertyNames": {
            "$ref": "#/$defs/legacy33/$defs/id"
          },
          "additionalProperties": {
            "$ref": "#/$defs/legacy33/$defs/id"
          }
        },
        "appearance": {
          "type": "object",
          "properties": {
            "roleColors": {
              "$ref": "#/$defs/legacy33/$defs/roleColorMap"
            },
            "customRoleColors": {
              "$ref": "#/$defs/legacy33/$defs/customRoleColorMap"
            },
            "objectColors": {
              "type": "array",
              "items": {
                "$ref": "#/$defs/legacy33/$defs/objectColor"
              }
            }
          },
          "required": [
            "roleColors",
            "customRoleColors",
            "objectColors"
          ],
          "additionalProperties": false
        }
      },
      "required": [
        "roleDefinitions",
        "sectionRoleAssignments",
        "appearance"
      ],
      "additionalProperties": false
    },
    "legacyData": {
      "type": "object",
      "properties": {
        "sourceVersion": {
          "const": "2.3.0"
        },
        "sourceSha256": {
          "$ref": "#/$defs/legacy33/$defs/sha256"
        },
        "sourceText": {
          "type": "string",
          "minLength": 1
        },
        "conversionReport": {
          "type": "object"
        }
      },
      "required": [
        "sourceVersion",
        "sourceSha256",
        "sourceText",
        "conversionReport"
      ],
      "additionalProperties": false
    },
    "extensions": {
      "type": "object",
      "propertyNames": {
        "pattern": "^[a-z][a-z0-9-]*(?:\\.[a-z][a-z0-9-]*){2,}$",
        "not": {
          "pattern": "[\\r\\n\\u2028\\u2029]"
        }
      },
      "properties": {
        "org.ostf.relative-phrase.v1": {
          "allOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/extension"
            },
            {
              "if": {
                "properties": {
                  "version": {
                    "const": "1.0.0-draft.1"
                  }
                },
                "required": [
                  "version"
                ]
              },
              "then": {
                "properties": {
                  "data": {
                    "$ref": "#/$defs/legacy33/$defs/relativeData"
                  }
                }
              }
            }
          ]
        },
        "org.ostf.generation-context.v1": {
          "allOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/extension"
            },
            {
              "if": {
                "properties": {
                  "version": {
                    "const": "1.0.0-draft.1"
                  }
                },
                "required": [
                  "version"
                ]
              },
              "then": {
                "properties": {
                  "data": {
                    "$ref": "#/$defs/legacy33/$defs/generationData"
                  }
                }
              }
            }
          ]
        },
        "org.ostf.appearance.v1": {
          "allOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/extension"
            },
            {
              "if": {
                "properties": {
                  "version": {
                    "const": "1.0.0-draft.1"
                  }
                },
                "required": [
                  "version"
                ]
              },
              "then": {
                "properties": {
                  "data": {
                    "$ref": "#/$defs/legacy33/$defs/appearanceData"
                  }
                }
              }
            }
          ]
        },
        "org.ostf.legacy-import.v1": {
          "allOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/extension"
            },
            {
              "if": {
                "properties": {
                  "version": {
                    "const": "1.0.0-draft.1"
                  }
                },
                "required": [
                  "version"
                ]
              },
              "then": {
                "properties": {
                  "data": {
                    "$ref": "#/$defs/legacy33/$defs/legacyData"
                  }
                }
              }
            }
          ]
        },
        "org.ostf.phrase-library.v1": {
          "allOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/extension"
            },
            {
              "if": {
                "properties": {
                  "version": {
                    "const": "1.0.0-draft.1"
                  }
                },
                "required": [
                  "version"
                ]
              },
              "then": {
                "properties": {
                  "data": {
                    "$ref": "#/$defs/legacy33/$defs/phraseLibraryData"
                  }
                }
              }
            }
          ]
        },
        "org.ostf.clip-choices.v1": {
          "allOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/extension"
            },
            {
              "if": {
                "properties": {
                  "version": {
                    "const": "1.0.0-draft.1"
                  }
                },
                "required": [
                  "version"
                ]
              },
              "then": {
                "properties": {
                  "data": {
                    "$ref": "#/$defs/legacy33/$defs/clipChoicesData"
                  }
                }
              }
            }
          ]
        },
        "org.ostf.localization.v1": {
          "allOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/extension"
            },
            {
              "if": {
                "properties": {
                  "version": {
                    "const": "1.0.0"
                  }
                },
                "required": [
                  "version"
                ]
              },
              "then": {
                "properties": {
                  "data": {
                    "$ref": "#/$defs/legacy33/$defs/localizationData"
                  }
                }
              }
            }
          ]
        }
      },
      "additionalProperties": {
        "$ref": "#/$defs/legacy33/$defs/extension"
      }
    },
    "phraseTemplate": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        },
        "notes": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/phraseSourceNote"
          }
        },
        "name": {
          "type": "string"
        }
      },
      "required": [
        "id",
        "duration",
        "notes"
      ],
      "additionalProperties": false
    },
    "phraseNoteMapping": {
      "type": "object",
      "properties": {
        "templateNoteId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "repeatIndex": {
          "$ref": "#/$defs/legacy33/$defs/nonnegative"
        },
        "noteId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        }
      },
      "required": [
        "templateNoteId",
        "repeatIndex",
        "noteId"
      ],
      "additionalProperties": false
    },
    "phraseInstance": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "templateId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "clipId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "repeatCount": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        },
        "transpose": {
          "type": "integer",
          "minimum": -127,
          "maximum": 127
        },
        "noteMap": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/phraseNoteMapping"
          }
        }
      },
      "required": [
        "id",
        "templateId",
        "clipId",
        "repeatCount",
        "transpose",
        "noteMap"
      ],
      "additionalProperties": false
    },
    "phraseLibraryData": {
      "type": "object",
      "properties": {
        "templates": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/phraseTemplate"
          }
        },
        "instances": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/phraseInstance"
          }
        }
      },
      "required": [
        "templates",
        "instances"
      ],
      "additionalProperties": false
    },
    "clipCandidate": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "clipIds": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/id"
          },
          "uniqueItems": true
        },
        "name": {
          "type": "string"
        }
      },
      "required": [
        "id",
        "clipIds"
      ],
      "additionalProperties": false
    },
    "clipChoiceGroup": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "candidates": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/clipCandidate"
          },
          "minItems": 1
        },
        "selectedCandidateId": {
          "anyOf": [
            {
              "$ref": "#/$defs/legacy33/$defs/id"
            },
            {
              "type": "null"
            }
          ]
        },
        "name": {
          "type": "string"
        }
      },
      "required": [
        "id",
        "candidates",
        "selectedCandidateId"
      ],
      "additionalProperties": false
    },
    "clipChoicesData": {
      "type": "object",
      "properties": {
        "groups": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/clipChoiceGroup"
          }
        }
      },
      "required": [
        "groups"
      ],
      "additionalProperties": false
    },
    "midiMapping": {
      "type": "object",
      "properties": {
        "standard": {
          "enum": [
            "gm1",
            "custom"
          ],
          "description": "gm1=GM Level 1 percussion key 35..81; custom=explicit arbitrary MIDI1 note number."
        },
        "note": {
          "$ref": "#/$defs/legacy33/$defs/pitch",
          "description": "MIDI1 note number in 0..127; for gm1 restrict to 35..81."
        }
      },
      "required": [
        "standard",
        "note"
      ],
      "additionalProperties": false,
      "allOf": [
        {
          "if": {
            "properties": {
              "standard": {
                "const": "gm1"
              }
            },
            "required": [
              "standard"
            ]
          },
          "then": {
            "properties": {
              "note": {
                "type": "integer",
                "minimum": 35,
                "maximum": 81
              }
            }
          }
        }
      ]
    },
    "soundSource": {
      "type": "object",
      "description": "Optional informational rendering class or opaque destination hint. Program Change, Bank Select and plugin presets are not standardized by Core.",
      "properties": {
        "kind": {
          "enum": [
            "unassigned",
            "gm1",
            "externalMidi",
            "plugin",
            "hostManaged"
          ]
        },
        "identifier": {
          "type": "string",
          "minLength": 1,
          "description": "Opaque target identifier, e.g. URI, plug-in identifier or port alias, not a loading command."
        }
      },
      "required": [
        "kind"
      ],
      "additionalProperties": false,
      "allOf": [
        {
          "if": {
            "properties": {
              "kind": {
                "enum": [
                  "gm1",
                  "unassigned"
                ]
              }
            },
            "required": [
              "kind"
            ]
          },
          "then": {
            "not": {
              "required": [
                "identifier"
              ]
            }
          }
        }
      ]
    },
    "localizedNames": {
      "type": "object",
      "minProperties": 1,
      "propertyNames": {
        "type": "string",
        "pattern": "^(?:[A-Za-z]{2,3}|[A-Za-z]{5,8})(?:-[A-Za-z0-9]{2,8})*$"
      },
      "additionalProperties": {
        "type": "string",
        "minLength": 1
      },
      "description": "Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically."
    },
    "drumKit": {
      "type": "object",
      "description": "Optional named ordered membership of global Sound Slot IDs. Does not define plugin preset, sample loading, choke or a kit-local MIDI remapping.",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "name": {
          "type": "string"
        },
        "localizedNames": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames"
        },
        "soundSlotIds": {
          "type": "array",
          "uniqueItems": true,
          "items": {
            "$ref": "#/$defs/legacy33/$defs/id"
          },
          "description": "Ordered, unique references to global soundSlots; may be empty."
        }
      },
      "required": [
        "id",
        "name",
        "soundSlotIds"
      ],
      "additionalProperties": false
    },
    "phraseSourceNote": {
      "type": "object",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "start": {
          "$ref": "#/$defs/legacy33/$defs/signed"
        },
        "duration": {
          "$ref": "#/$defs/legacy33/$defs/positive"
        },
        "pitch": {
          "$ref": "#/$defs/legacy33/$defs/pitch"
        },
        "soundSlotId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        }
      },
      "required": [
        "id",
        "start",
        "duration"
      ],
      "additionalProperties": false,
      "oneOf": [
        {
          "required": [
            "pitch"
          ],
          "not": {
            "required": [
              "soundSlotId"
            ]
          }
        },
        {
          "required": [
            "soundSlotId"
          ],
          "not": {
            "required": [
              "pitch"
            ]
          }
        }
      ],
      "description": "Version-frozen 1.0.0-draft.1 phrase source note. Does NOT admit velocity; a linked Core instance may independently set velocity."
    },
    "voicing": {
      "type": "object",
      "additionalProperties": false,
      "description": "Optional informational chord voicing snapshot, not a played Core note collection. Repeated pitches and original array order have meaning for lossless preservation. Per-entry velocity is an optional voicing-preview hint.",
      "properties": {
        "pitches": {
          "type": "array",
          "minItems": 1,
          "items": {
            "$ref": "#/$defs/legacy33/$defs/pitch"
          },
          "description": "Ordered multiset of concrete absolute pitches, 0-127. Duplicates allowed."
        },
        "velocities": {
          "type": "array",
          "minItems": 1,
          "items": {
            "anyOf": [
              {
                "type": "integer",
                "minimum": 1,
                "maximum": 127
              },
              {
                "type": "null"
              }
            ]
          },
          "description": "Optional positional velocities aligned one-to-one to pitches. Null means unspecified at this index, not 0. Exact equal-length rule is semantic."
        }
      },
      "required": [
        "pitches"
      ]
    },
    "contributor": {
      "type": "object",
      "additionalProperties": false,
      "description": "One song-local identity; same contributor can receive multiple credit roles. Unicode source name is authoritative.",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "name": {
          "type": "string",
          "minLength": 1
        },
        "kind": {
          "enum": [
            "person",
            "organization",
            "other"
          ]
        },
        "localizedNames": {
          "$ref": "#/$defs/legacy33/$defs/localizedNames"
        }
      },
      "required": [
        "id",
        "name"
      ]
    },
    "credit": {
      "type": "object",
      "additionalProperties": false,
      "description": "Ordered credit assignment referencing one contributor; role is an open semantic token, not a translated label.",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "contributorId": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "role": {
          "type": "string",
          "pattern": "^[a-z][a-z0-9.-]*$"
        },
        "label": {
          "type": "string"
        }
      },
      "required": [
        "id",
        "contributorId",
        "role"
      ]
    },
    "alternateTitle": {
      "type": "object",
      "additionalProperties": false,
      "description": "Stable-ID song title alternative; array position and title text do not identify it.",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "title": {
          "type": "string",
          "minLength": 1
        }
      },
      "required": [
        "id",
        "title"
      ]
    },
    "languageMap32": {
      "type": "object",
      "minProperties": 1,
      "propertyNames": {
        "type": "string",
        "pattern": "^[A-Za-z]{1,8}(?:-[A-Za-z0-9]{1,8})*$"
      },
      "additionalProperties": {
        "type": "string"
      },
      "description": "Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank."
    },
    "localizationTarget": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "kind": {
          "enum": [
            "song",
            "alternateTitle",
            "contributor",
            "credit",
            "section",
            "track",
            "clip",
            "soundSlot",
            "drumKit",
            "lyricDocument",
            "resource",
            "phraseTemplate",
            "clipCandidate",
            "clipChoiceGroup",
            "roleDefinition",
            "genre"
          ]
        },
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id"
        },
        "profile": {
          "type": "string",
          "enum": [
            "org.ostf.phrase-library.v1",
            "org.ostf.clip-choices.v1",
            "org.ostf.appearance.v1"
          ]
        }
      },
      "required": [
        "kind"
      ],
      "allOf": [
        {
          "if": {
            "properties": {
              "kind": {
                "const": "song"
              }
            },
            "required": [
              "kind"
            ]
          },
          "then": {
            "not": {
              "anyOf": [
                {
                  "required": [
                    "id"
                  ]
                },
                {
                  "required": [
                    "profile"
                  ]
                }
              ]
            }
          },
          "else": {
            "required": [
              "id"
            ]
          }
        },
        {
          "if": {
            "properties": {
              "kind": {
                "enum": [
                  "phraseTemplate",
                  "clipCandidate",
                  "clipChoiceGroup",
                  "roleDefinition"
                ]
              }
            },
            "required": [
              "kind"
            ]
          },
          "then": {
            "required": [
              "profile"
            ]
          },
          "else": {
            "not": {
              "required": [
                "profile"
              ]
            }
          }
        }
      ],
      "description": "Stable target by Core ID or profile-local scoped ID; song is the only ID-less target. No array indexes or mutable names as locators."
    },
    "localizationEntry": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "target": {
          "$ref": "#/$defs/legacy33/$defs/localizationTarget"
        },
        "field": {
          "type": "string",
          "minLength": 1,
          "description": "Exact allowed source text field for this target kind (see normative matrix). No arbitrary path or musical value fields."
        },
        "values": {
          "$ref": "#/$defs/legacy33/$defs/languageMap32"
        }
      },
      "required": [
        "target",
        "field",
        "values"
      ],
      "description": "Independent translations for one stable source field. Original source text remains authoritative as fallback."
    },
    "localizationData": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "entries": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/localizationEntry"
          }
        }
      },
      "required": [
        "entries"
      ],
      "description": "Language-map translations indexed by stable kind/id/field, never by names or array positions."
    },
    "genre": {
      "type": "object",
      "description": "One song-local open genre tag. The label is authored Unicode text and not a predefined controlled taxonomy; ID is stable for metadata merges and localization.",
      "properties": {
        "id": {
          "$ref": "#/$defs/legacy33/$defs/id",
          "description": "Stable song-local ID unique among all Core objects."
        },
        "name": {
          "type": "string",
          "minLength": 1,
          "description": "Original Unicode genre label, never normalized or rewritten during import."
        },
        "externalUri": {
          "type": "string",
          "minLength": 1,
          "format": "uri",
          "description": "Optional external taxonomy URI hint; never authoritative for a local genre label, and never auto-dereferenced."
        }
      },
      "required": [
        "id",
        "name"
      ],
      "additionalProperties": false
    }
  }
}
```

## `legacy33/$defs/id`

```json
{
  "type": "string",
  "pattern": "^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$",
  "not": {
    "pattern": "[\\r\\n\\u2028\\u2029]"
  }
}
```

Structural condition blocks present: `not`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/nonnegative`

```json
{
  "type": "integer",
  "minimum": 0,
  "maximum": 9007199254740991
}
```

## `legacy33/$defs/positive`

```json
{
  "type": "integer",
  "minimum": 1,
  "maximum": 9007199254740991
}
```

## `legacy33/$defs/signed`

```json
{
  "type": "integer",
  "minimum": -9007199254740991,
  "maximum": 9007199254740991
}
```

## `legacy33/$defs/pitch`

```json
{
  "type": "integer",
  "minimum": 0,
  "maximum": 127
}
```

## `legacy33/$defs/ref`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `type` | enum<section | track | clip | note | chord | scale | soundSlot | drumKit | lyricDocument | lyricPhrase | lyricUnit | lyricAlignment | resource | genre> | yes | enum=["section", "track", "clip", "note", "chord", "scale", "soundSlot", "drumKit", "lyricDocument", "lyricPhrase", "lyricUnit", "lyricAlignment", "resource", "genre"] |  |
| `id` | id | yes | — |  |

## `legacy33/$defs/range`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |

## `legacy33/$defs/spelledPitchClass`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `step` | enum<A | B | C | D | E | F | G> | yes | enum=["A", "B", "C", "D", "E", "F", "G"] |  |
| `alter` | integer | yes | minimum=-2; maximum=2 |  |

## `legacy33/$defs/song`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `title` | string | yes | — |  |
| `primaryMelodyTrackId` | id | optional | — |  |
| `localizedTitles` | localizedNames | optional | — | Optional localized song-title alternatives. song.title is the stable fallback. |
| `subtitle` | string | optional | — | Optional original-language subtitle. |
| `description` | string | optional | — | Optional original-language description, including Unicode and line breaks. |
| `alternateTitles` | array<alternateTitle> | optional | — | Additional stable-ID source-language titles. |
| `credits` | array<credit> | optional | — | Ordered credit assignments to contributors. Multiple roles and duplicate contributors permitted. |
| `genres` | array<genre> | optional | — | Optional open-ended ordered genre tags; zero or more allowed. Order is authored display preference, first item is primary if a primary label is desired, not a generator weight. Absent means unspecified. |

## `legacy33/$defs/section`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | minLength=1 |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `role` | string | optional | — |  |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |

## `legacy33/$defs/tempo`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `at` | nonnegative | yes | — |  |
| `bpm` | number | yes | maximum=1000; exclusiveMinimum=0 |  |

## `legacy33/$defs/meter`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `at` | nonnegative | yes | — |  |
| `numerator` | integer | yes | minimum=1; maximum=64 |  |
| `denominator` | enum<1 | 2 | 4 | 8 | 16 | 32 | 64> | yes | enum=[1, 2, 4, 8, 16, 32, 64] |  |

## `legacy33/$defs/timeline`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `sections` | array<section> | yes | — |  |
| `tempoChanges` | array<tempo> | yes | minItems=1 |  |
| `meterChanges` | array<meter> | yes | minItems=1 |  |

## `legacy33/$defs/chord`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `label` | string | yes | minLength=1 |  |
| `root` | spelledPitchClass | optional | — |  |
| `intervals` | array<integer> | optional | minItems=1; maxItems=36; uniqueItems=true; contains=0 |  |
| `bass` | spelledPitchClass | optional | — |  |
| `voicing` | voicing | optional | — | Optional pitch snapshot (not an arrangement note sequence). No automatic playback. |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/scale`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `label` | string | optional | — |  |
| `tonic` | spelledPitchClass | yes | — |  |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true; contains=0 |  |

## `legacy33/$defs/harmony`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `chords` | array<chord> | yes | — |  |
| `scales` | array<scale> | yes | — |  |

## `legacy33/$defs/instrument`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `name` | string | yes | — |  |
| `family` | string | optional | — |  |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |

## `legacy33/$defs/note`

Concrete Core note; optional velocity is a note-on attribute. Phrase templates use a separate version-frozen sourceNote shape.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `duration` | positive | yes | — | Positive note gate in ticks, not the guaranteed audible duration of a one-shot percussion sample. |
| `pitch` | pitch | optional | — |  |
| `soundSlotId` | id | optional | — | Document-local Sound Slot ID. Distinct events and IDs stay distinct even if several slots share a MIDI key; a renderer decides the acoustic timbre. |
| `velocity` | integer | optional | minimum=1; maximum=127 | Optional authored note-on velocity for performed Core notes, 1–127; omit to mean unspecified. It is not a note-off velocity, controller stream, chord tone, or a sound-rendering guarantee. Never insert an inferred default when saving. |

Structural condition blocks present: `oneOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/clip`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | optional | — |  |
| `start` | nonnegative | yes | — |  |
| `duration` | positive | yes | — |  |
| `enabled` | boolean | optional | default=true |  |
| `notes` | array<note> | yes | — |  |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |

## `legacy33/$defs/track`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | — |  |
| `role` | string | optional | — |  |
| `instrument` | instrument | optional | — |  |
| `enabled` | boolean | optional | default=true |  |
| `clips` | array<clip> | yes | — |  |
| `trackType` | enum<melodic | drum | mixed> | optional | enum=["melodic", "drum", "mixed"] | Semantic event-target category; optional; missing/empty-note tracks may be unknown. It is not a DAW track class. |
| `preferredView` | enum<auto | pianoRoll | drumGrid> | optional | default="auto"; enum=["auto", "pianoRoll", "drumGrid"] | Non-binding presentation preference. Does not change note meaning. |
| `soundSource` | soundSource | optional | — | Optional sound-source selection hint, not a plug-in state, preset blob, or sound guarantee. |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |
| `drumKitId` | id | optional | — | Optional reference to a declared drumKit; only for a drum/mixed or empty inferred track. All stored slot events in the track must belong to the referenced kit. |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/soundSlot`

Document-local stable logical percussion target. Equal roles, names or MIDI note mappings never merge distinct slot IDs. No sample/audio identity is encoded.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | — |  |
| `role` | string | optional | — | Optional free-text musical role; not a standardized GM instrument code or an acoustic sample identifier. |
| `midiNote` | pitch | optional | — | Legacy 3.0 MIDI note hint only; not a GM1 declaration or preview timbre identity. |
| `midiMapping` | midiMapping | optional | — | Explicit external MIDI1 output mapping; standard=gm1 is separate from custom even for an equal note number. Not a unique slot or audio identity. |
| `localizedNames` | localizedNames | optional | — | Optional localized display variants; retain the original name and stable ID. Names never determine track type, routing or identity. |

## `legacy33/$defs/lyricDocument`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `title` | string | optional | — |  |
| `language` | string | optional | — |  |

## `legacy33/$defs/lyricPhrase`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `documentId` | id | yes | — |  |
| `text` | string | yes | — |  |
| `pronunciation` | string | optional | — |  |
| `start` | nonnegative | optional | — |  |
| `duration` | positive | optional | — |  |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/lyricUnit`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `phraseId` | id | yes | — |  |
| `text` | string | yes | — |  |
| `pronunciation` | string | optional | — |  |
| `start` | nonnegative | optional | — |  |
| `duration` | positive | optional | — |  |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/lyricAlignment`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `unitIds` | array<id> | yes | minItems=1; uniqueItems=true |  |
| `noteIds` | array<id> | yes | minItems=1; uniqueItems=true |  |

## `legacy33/$defs/lyrics`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `documents` | array<lyricDocument> | yes | — |  |
| `phrases` | array<lyricPhrase> | yes | — |  |
| `units` | array<lyricUnit> | yes | — |  |
| `alignments` | array<lyricAlignment> | yes | — |  |

## `legacy33/$defs/sha256`

```json
{
  "type": "string",
  "pattern": "^[0-9a-f]{64}$",
  "not": {
    "pattern": "[\\r\\n\\u2028\\u2029]"
  }
}
```

Structural condition blocks present: `not`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/resource`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `kind` | enum<audio | midi | text | other> | yes | enum=["audio", "midi", "text", "other"] |  |
| `uri` | string | yes | minLength=1 |  |
| `name` | string | optional | — |  |
| `sha256` | sha256 | optional | — |  |

## `legacy33/$defs/dependencies`

```json
{
  "oneOf": [
    {
      "type": "object",
      "properties": {
        "scope": {
          "enum": [
            "document",
            "none"
          ]
        }
      },
      "required": [
        "scope"
      ],
      "additionalProperties": false
    },
    {
      "type": "object",
      "properties": {
        "scope": {
          "const": "objects"
        },
        "refs": {
          "type": "array",
          "items": {
            "$ref": "#/$defs/legacy33/$defs/ref"
          },
          "minItems": 1,
          "uniqueItems": true
        }
      },
      "required": [
        "scope",
        "refs"
      ],
      "additionalProperties": false
    }
  ]
}
```

Structural condition blocks present: `oneOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/extension`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `version` | string | yes | minLength=1 |  |
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |  |
| `dependencies` | dependencies | yes | — |  |
| `data` | object/constraint | yes | — |  |

## `legacy33/$defs/relativeAssignment`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `noteId` | id | yes | — |  |
| `basis` | enum<chordTone | scaleDegree> | yes | enum=["chordTone", "scaleDegree"] |  |
| `contextId` | id | yes | — |  |
| `anchorPitch` | pitch | yes | — |  |
| `index` | integer | yes | minimum=1; maximum=36 |  |
| `chromaticOffset` | integer | yes | minimum=-24; maximum=24 |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/relativeData`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `assignments` | array<relativeAssignment> | yes | — |  |

## `legacy33/$defs/generator`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | string | yes | minLength=1 |  |
| `version` | string | yes | minLength=1 |  |

## `legacy33/$defs/generationInput`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `role` | string | yes | minLength=1 |  |
| `ref` | ref | yes | — |  |
| `readRange` | range | optional | — |  |

## `legacy33/$defs/generationProcess`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `generator` | generator | yes | — |  |
| `inputs` | array<generationInput> | yes | — |  |
| `outputs` | array<ref> | yes | minItems=1 |  |
| `targetRange` | range | optional | — |  |
| `protectedNoteIds` | array<id> | yes | uniqueItems=true |  |
| `vendorExtension` | string | optional | minLength=1 |  |

## `legacy33/$defs/generationData`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `processes` | array<generationProcess> | yes | — |  |

## `legacy33/$defs/color`

```json
{
  "type": "string",
  "pattern": "^#[0-9A-Fa-f]{6}$",
  "not": {
    "pattern": "[\\r\\n\\u2028\\u2029]"
  }
}
```

Structural condition blocks present: `not`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/roleDefinition`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | minLength=1 |  |
| `baseRole` | string | optional | — |  |

## `legacy33/$defs/roleColorMap`

```json
{
  "type": "object",
  "additionalProperties": {
    "$ref": "#/$defs/legacy33/$defs/color"
  }
}
```

## `legacy33/$defs/customRoleColorMap`

```json
{
  "type": "object",
  "propertyNames": {
    "$ref": "#/$defs/legacy33/$defs/id"
  },
  "additionalProperties": {
    "$ref": "#/$defs/legacy33/$defs/color"
  }
}
```

## `legacy33/$defs/objectColor`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `target` | ref | yes | — |  |
| `color` | color | yes | — |  |

## `legacy33/$defs/appearanceData`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `roleDefinitions` | array<roleDefinition> | yes | — |  |
| `sectionRoleAssignments` | object | yes | — |  |
| `appearance` | object | yes | — |  |

## `legacy33/$defs/legacyData`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `sourceVersion` | const `2.3.0` | yes | const="2.3.0" |  |
| `sourceSha256` | sha256 | yes | — |  |
| `sourceText` | string | yes | minLength=1 |  |
| `conversionReport` | object | yes | — |  |

## `legacy33/$defs/extensions`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `org.ostf.relative-phrase.v1` | object/constraint | optional | — |  |
| `org.ostf.generation-context.v1` | object/constraint | optional | — |  |
| `org.ostf.appearance.v1` | object/constraint | optional | — |  |
| `org.ostf.legacy-import.v1` | object/constraint | optional | — |  |
| `org.ostf.phrase-library.v1` | object/constraint | optional | — |  |
| `org.ostf.clip-choices.v1` | object/constraint | optional | — |  |
| `org.ostf.localization.v1` | object/constraint | optional | — |  |

## `legacy33/$defs/phraseTemplate`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `duration` | positive | yes | — |  |
| `notes` | array<phraseSourceNote> | yes | — |  |
| `name` | string | optional | — |  |

## `legacy33/$defs/phraseNoteMapping`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `templateNoteId` | id | yes | — |  |
| `repeatIndex` | nonnegative | yes | — |  |
| `noteId` | id | yes | — |  |

## `legacy33/$defs/phraseInstance`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `templateId` | id | yes | — |  |
| `clipId` | id | yes | — |  |
| `repeatCount` | positive | yes | — |  |
| `transpose` | integer | yes | minimum=-127; maximum=127 |  |
| `noteMap` | array<phraseNoteMapping> | yes | — |  |

## `legacy33/$defs/phraseLibraryData`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `templates` | array<phraseTemplate> | yes | — |  |
| `instances` | array<phraseInstance> | yes | — |  |

## `legacy33/$defs/clipCandidate`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `clipIds` | array<id> | yes | uniqueItems=true |  |
| `name` | string | optional | — |  |

## `legacy33/$defs/clipChoiceGroup`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `candidates` | array<clipCandidate> | yes | minItems=1 |  |
| `selectedCandidateId` | anyOf | yes | — |  |
| `name` | string | optional | — |  |

## `legacy33/$defs/clipChoicesData`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `groups` | array<clipChoiceGroup> | yes | — |  |

## `legacy33/$defs/midiMapping`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `standard` | enum<gm1 | custom> | yes | enum=["gm1", "custom"] | gm1=GM Level 1 percussion key 35..81; custom=explicit arbitrary MIDI1 note number. |
| `note` | pitch | yes | — | MIDI1 note number in 0..127; for gm1 restrict to 35..81. |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/soundSource`

Optional informational rendering class or opaque destination hint. Program Change, Bank Select and plugin presets are not standardized by Core.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `kind` | enum<unassigned | gm1 | externalMidi | plugin | hostManaged> | yes | enum=["unassigned", "gm1", "externalMidi", "plugin", "hostManaged"] |  |
| `identifier` | string | optional | minLength=1 | Opaque target identifier, e.g. URI, plug-in identifier or port alias, not a loading command. |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/localizedNames`

Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically.

```json
{
  "type": "object",
  "minProperties": 1,
  "propertyNames": {
    "type": "string",
    "pattern": "^(?:[A-Za-z]{2,3}|[A-Za-z]{5,8})(?:-[A-Za-z0-9]{2,8})*$"
  },
  "additionalProperties": {
    "type": "string",
    "minLength": 1
  },
  "description": "Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically."
}
```

## `legacy33/$defs/drumKit`

Optional named ordered membership of global Sound Slot IDs. Does not define plugin preset, sample loading, choke or a kit-local MIDI remapping.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | — |  |
| `localizedNames` | localizedNames | optional | — |  |
| `soundSlotIds` | array<id> | yes | uniqueItems=true | Ordered, unique references to global soundSlots; may be empty. |

## `legacy33/$defs/phraseSourceNote`

Version-frozen 1.0.0-draft.1 phrase source note. Does NOT admit velocity; a linked Core instance may independently set velocity.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `duration` | positive | yes | — |  |
| `pitch` | pitch | optional | — |  |
| `soundSlotId` | id | optional | — |  |

Structural condition blocks present: `oneOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/voicing`

Optional informational chord voicing snapshot, not a played Core note collection. Repeated pitches and original array order have meaning for lossless preservation. Per-entry velocity is an optional voicing-preview hint.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `pitches` | array<pitch> | yes | minItems=1 | Ordered multiset of concrete absolute pitches, 0-127. Duplicates allowed. |
| `velocities` | array<anyOf> | optional | minItems=1 | Optional positional velocities aligned one-to-one to pitches. Null means unspecified at this index, not 0. Exact equal-length rule is semantic. |

## `legacy33/$defs/contributor`

One song-local identity; same contributor can receive multiple credit roles. Unicode source name is authoritative.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | minLength=1 |  |
| `kind` | enum<person | organization | other> | optional | enum=["person", "organization", "other"] |  |
| `localizedNames` | localizedNames | optional | — |  |

## `legacy33/$defs/credit`

Ordered credit assignment referencing one contributor; role is an open semantic token, not a translated label.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `contributorId` | id | yes | — |  |
| `role` | string | yes | pattern="^[a-z][a-z0-9.-]*$" |  |
| `label` | string | optional | — |  |

## `legacy33/$defs/alternateTitle`

Stable-ID song title alternative; array position and title text do not identify it.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `title` | string | yes | minLength=1 |  |

## `legacy33/$defs/languageMap32`

Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank.

```json
{
  "type": "object",
  "minProperties": 1,
  "propertyNames": {
    "type": "string",
    "pattern": "^[A-Za-z]{1,8}(?:-[A-Za-z0-9]{1,8})*$"
  },
  "additionalProperties": {
    "type": "string"
  },
  "description": "Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank."
}
```

## `legacy33/$defs/localizationTarget`

Stable target by Core ID or profile-local scoped ID; song is the only ID-less target. No array indexes or mutable names as locators.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `kind` | enum<song | alternateTitle | contributor | credit | section | track | clip | soundSlot | drumKit | lyricDocument | resource | phraseTemplate | clipCandidate | clipChoiceGroup | roleDefinition | genre> | yes | enum=["song", "alternateTitle", "contributor", "credit", "section", "track", "clip", "soundSlot", "drumKit", "lyricDocument", "resource", "phraseTemplate", "clipCandidate", "clipChoiceGroup", "roleDefinition", "genre"] |  |
| `id` | id | optional | — |  |
| `profile` | enum<org.ostf.phrase-library.v1 | org.ostf.clip-choices.v1 | org.ostf.appearance.v1> | optional | enum=["org.ostf.phrase-library.v1", "org.ostf.clip-choices.v1", "org.ostf.appearance.v1"] |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `legacy33/$defs/localizationEntry`

Independent translations for one stable source field. Original source text remains authoritative as fallback.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `target` | localizationTarget | yes | — |  |
| `field` | string | yes | minLength=1 | Exact allowed source text field for this target kind (see normative matrix). No arbitrary path or musical value fields. |
| `values` | languageMap32 | yes | — |  |

## `legacy33/$defs/localizationData`

Language-map translations indexed by stable kind/id/field, never by names or array positions.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `entries` | array<localizationEntry> | yes | — |  |

## `legacy33/$defs/genre`

One song-local open genre tag. The label is authored Unicode text and not a predefined controlled taxonomy; ID is stable for metadata merges and localization.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — | Stable song-local ID unique among all Core objects. |
| `name` | string | yes | minLength=1 | Original Unicode genre label, never normalized or rewritten during import. |
| `externalUri` | string | optional | minLength=1 | Optional external taxonomy URI hint; never authoritative for a local genre label, and never auto-dereferenced. |

## `opaqueDocumentId`

Stable URI or UUID-style identifier for a document lineage. Compared as exact Unicode text; never fetched.

```json
{
  "type": "string",
  "minLength": 1,
  "description": "Stable URI or UUID-style identifier for a document lineage. Compared as exact Unicode text; never fetched."
}
```

## `revisionRef`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `documentId` | opaqueDocumentId | yes | — |  |
| `revisionId` | opaqueDocumentId | yes | — |  |
| `sha256` | sha256 | optional | — |  |

## `software`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `name` | string | yes | minLength=1 |  |
| `version` | string | optional | minLength=1 |  |

## `documentInfo`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | opaqueDocumentId | yes | — |  |
| `revisionId` | opaqueDocumentId | yes | — |  |
| `parents` | array<revisionRef> | optional | uniqueItems=true |  |
| `createdBy` | software | optional | — |  |
| `modifiedBy` | software | optional | — |  |

## `key`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `state` | enum<tonal | atonal | unspecified> | yes | enum=["tonal", "atonal", "unspecified"] |  |
| `tonic` | spelledPitchClass | optional | — |  |
| `mode` | string | optional | minLength=1 | Authored mode identifier. Canonical recognized values include major, minor, dorian, phrygian, lydian, mixolydian, locrian. Unknown values are preserved, never converted to major/minor. |
| `label` | string | optional | — |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `keyChange`

A change after tick zero; initial key is stored only at song.key. Change at tick 0 is forbidden to prevent dual authority.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `at` | positive | yes | — |  |
| `key` | key | yes | — |  |
| `label` | string | optional | — |  |

## `scaleDefinition`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `tonic` | spelledPitchClass | yes | — |  |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true; contains=0 |  |
| `label` | string | optional | — |  |

## `scaleTarget`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `type` | enum<section | track | clip | chord | note> | yes | enum=["section", "track", "clip", "chord", "note"] |  |
| `id` | id | yes | — |  |

## `mood`

Authored descriptive mood tag; not a generator preset or a probability.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — | Stable song-local ID unique among all Core objects. |
| `name` | string | yes | minLength=1 | Original Unicode genre label, never normalized or rewritten during import. |
| `externalUri` | string | optional | minLength=1 | Optional external vocabulary URI hint; never authoritative for the authored mood name and never auto-dereferenced. |

## `rightsStatement`

Authored rights statement only; no rights or permissions are inferred or granted by the format.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `notice` | string | optional | — |  |
| `licenseUri` | string | optional | minLength=1 |  |
| `rightsHolderIds` | array<id> | optional | uniqueItems=true |  |

## `grooveIntent`

Descriptive generation intent only. Never re-apply to already stored notes. unitTicks is one unswung subdivision; two subdivisions form a pair.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `name` | string | optional | — |  |
| `unitTicks` | positive | optional | — |  |
| `longWeight` | integer | optional | minimum=1; maximum=1000000 |  |
| `shortWeight` | integer | optional | minimum=1; maximum=1000000 |  |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `generationIntent`

Portable description of intent, not a promise to reproduce an algorithm. Does not change song.genres or song.moods.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `genreNames` | array<string> | optional | — |  |
| `moodNames` | array<string> | optional | — |  |
| `groove` | grooveIntent | optional | — |  |
| `prompt` | string | optional | — |  |
| `seed` | string | optional | — |  |

## `relativePhrases`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |  |
| `dependencies` | dependencies | yes | — |  |
| `assignments` | array<relativeAssignment> | yes | — |  |

## `generation`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |  |
| `dependencies` | dependencies | yes | — |  |
| `processes` | array<generationProcess> | yes | — |  |

## `presentation`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |  |
| `dependencies` | dependencies | yes | — |  |
| `roleDefinitions` | array<roleDefinition> | yes | — |  |
| `sectionRoleAssignments` | object | yes | — |  |
| `appearance` | object | yes | — |  |

## `phraseLibrary`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |  |
| `dependencies` | dependencies | yes | — |  |
| `templates` | array<phraseTemplate> | yes | — |  |
| `instances` | array<phraseInstance> | yes | — |  |

## `clipChoices`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |  |
| `dependencies` | dependencies | yes | — |  |
| `groups` | array<clipChoiceGroup> | yes | — |  |

## `localization`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `status` | enum<current | stale | unverified> | yes | enum=["current", "stale", "unverified"] |  |
| `dependencies` | dependencies | yes | — |  |
| `entries` | array<localizationEntry> | yes | — |  |

## `corePhraseSourceNote`

Template-local source note for phrase materialization. It carries the same note-local 3.5 properties as concrete notes. It is not performed by itself; concrete track notes remain the saved playback events.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `duration` | positive | yes | — | Positive note gate in ticks, not the guaranteed audible duration of a one-shot percussion sample. |
| `pitch` | pitch | optional | — |  |
| `soundSlotId` | id | optional | — | Document-local Sound Slot ID. Distinct events and IDs stay distinct even if several slots share a MIDI key; a renderer decides the acoustic timbre. |
| `velocity` | integer | optional | minimum=1; maximum=127 | Optional template note-on velocity, 1–127. Applied only by an explicit materialization/update according to instance.velocityMode; never a performed event by itself. |
| `enabled` | boolean | optional | default=true | Optional note-local enable state. Omitted means enabled. False preserves the note but suppresses scheduling/playback; it is distinct from probability=0. |
| `releaseVelocity` | integer | optional | minimum=0; maximum=127 | Optional MIDI Note Off/release velocity, 0-127. Omit when unspecified; readers must not invent and write back a default. |
| `probability` | number | optional | minimum=0; maximum=1; default=1 | Optional note-playback probability from 0.0 to 1.0. Omitted means 1.0. Random sequence identity is not standardized; materialize notes for deterministic interchange. |
| `velocityDeviation` | integer | optional | minimum=-126; maximum=126; default=0 | Optional note-local velocity variation endpoint relative to velocity. Requires velocity. Semantic rule: velocity + velocityDeviation must remain within 1..127. Omitted means 0. |
| `pitchRole` | enum<musical | fixedTrigger> | optional | default="musical"; enum=["musical", "fixedTrigger"] | Meaning of a pitch note. musical participates in pitch/harmony/transpose operations; fixedTrigger is an exact MIDI key trigger (for example a keyswitch) and is never transposed or used as musical-pitch evidence. Allowed only on pitch notes. |

Structural condition blocks present: `oneOf`, `allOf`, `dependentRequired`. Consult the schema for exact machine-readable logic.


## `provenanceSource`

Historical source; hashes refer to exact source bytes, not the current document. No automatic retrieval.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `format` | string | optional | minLength=1 |  |
| `version` | string | optional | minLength=1 |  |
| `sha256` | sha256 | optional | — |  |
| `uri` | string | optional | minLength=1 |  |
| `documentId` | opaqueDocumentId | optional | — |  |
| `revisionId` | opaqueDocumentId | optional | — |  |
| `resourceId` | id | optional | — |  |

Structural condition blocks present: `dependentRequired`. Consult the schema for exact machine-readable logic.


## `provenanceRecord`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `action` | enum<create | import | conversion | edit | transpose | merge | generation | other> | yes | enum=["create", "import", "conversion", "edit", "transpose", "merge", "generation", "other"] |  |
| `description` | string | optional | — |  |
| `tool` | software | optional | — |  |
| `sources` | array<provenanceSource> | optional | — |  |
| `semitones` | integer | optional | minimum=-127; maximum=127 |  |
| `beforeKey` | key | optional | — |  |
| `afterKey` | key | optional | — |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `provenance`

Non-executable historical assertions; not composer credits and not a substitute for current metadata. Does not prove authorship or operation correctness.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `records` | array<provenanceRecord> | yes | — |  |

## `midiChannel`

Human-readable MIDI 1.0 channel number 1..16. Wire status-nibble channel = value - 1.

```json
{
  "type": "integer",
  "minimum": 1,
  "maximum": 16,
  "description": "Human-readable MIDI 1.0 channel number 1..16. Wire status-nibble channel = value - 1."
}
```

## `midiBus`

Document-local logical MIDI destination. Each bus has its own MIDI channel namespace. It is not a physical OS/USB/DAW port binding.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | yes | — |  |

## `midi`

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `buses` | array<midiBus> | yes | — |  |

## `mpeSettings`

Semantic MPE zone declaration. Lower-zone master is channel 1 and upper-zone master is channel 16; member channels are derived contiguously from memberChannelCount.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `zone` | enum<lower | upper> | yes | enum=["lower", "upper"] |  |
| `memberChannelCount` | integer | yes | minimum=1; maximum=15 |  |

## `trackMidi`

Optional MIDI channel/routing interpretation for a track. busId is a logical OSTF bus, never a physical MIDI port.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `busId` | id | optional | — |  |
| `mode` | enum<singleChannel | multiChannel | mpe> | yes | enum=["singleChannel", "multiChannel", "mpe"] |  |
| `channel` | midiChannel | optional | — |  |
| `mpe` | mpeSettings | optional | — |  |

Structural condition blocks present: `allOf`. Consult the schema for exact machine-readable logic.


## `controlChangeEvent`

MIDI 1.0 Control Change. Controllers 120..127 use channelMode instead.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `order` | signed | optional | default=1 | Same-tick ordering key. Omitted means 1. Note Off has implicit order 0 and Note On implicit order 2; ties are resolved by the normative ordering rules. |
| `channel` | midiChannel | optional | — |  |
| `type` | const `controlChange` | yes | const="controlChange" |  |
| `targetNoteId` | id | optional | — | Optional semantic association with an existing concrete note. It does not replace channel/pitch wire semantics. |
| `controller` | integer | yes | minimum=0; maximum=119 |  |
| `value` | integer | yes | minimum=0; maximum=127 |  |

## `channelModeEvent`

MIDI 1.0 Channel Mode message represented separately from ordinary Control Change.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `order` | signed | optional | default=1 | Same-tick ordering key. Omitted means 1. Note Off has implicit order 0 and Note On implicit order 2; ties are resolved by the normative ordering rules. |
| `channel` | midiChannel | optional | — |  |
| `type` | const `channelMode` | yes | const="channelMode" |  |
| `controller` | integer | yes | minimum=120; maximum=127 |  |
| `value` | integer | yes | minimum=0; maximum=127 |  |

## `programChangeEvent`

MIDI 1.0 Program Change using the wire data value 0..127. UI may display 1..128.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `order` | signed | optional | default=1 | Same-tick ordering key. Omitted means 1. Note Off has implicit order 0 and Note On implicit order 2; ties are resolved by the normative ordering rules. |
| `channel` | midiChannel | optional | — |  |
| `type` | const `programChange` | yes | const="programChange" |  |
| `program` | integer | yes | minimum=0; maximum=127 |  |

## `pitchBendEvent`

MIDI 1.0 14-bit Pitch Bend Change. Bend range is receiver state/RPN data, not encoded in value14.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `order` | signed | optional | default=1 | Same-tick ordering key. Omitted means 1. Note Off has implicit order 0 and Note On implicit order 2; ties are resolved by the normative ordering rules. |
| `channel` | midiChannel | optional | — |  |
| `type` | const `pitchBend` | yes | const="pitchBend" |  |
| `targetNoteId` | id | optional | — | Optional semantic association with an existing concrete note. It does not replace channel/pitch wire semantics. |
| `value14` | integer | yes | minimum=0; maximum=16383 | Raw MIDI 1.0 14-bit pitch-bend value; center is 8192. |

## `channelPressureEvent`

MIDI 1.0 Channel Pressure (channel aftertouch).

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `order` | signed | optional | default=1 | Same-tick ordering key. Omitted means 1. Note Off has implicit order 0 and Note On implicit order 2; ties are resolved by the normative ordering rules. |
| `channel` | midiChannel | optional | — |  |
| `type` | const `channelPressure` | yes | const="channelPressure" |  |
| `targetNoteId` | id | optional | — | Optional semantic association with an existing concrete note. It does not replace channel/pitch wire semantics. |
| `value` | integer | yes | minimum=0; maximum=127 |  |

## `polyPressureEvent`

MIDI 1.0 Polyphonic Key Pressure (poly aftertouch).

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `start` | signed | yes | — |  |
| `order` | signed | optional | default=1 | Same-tick ordering key. Omitted means 1. Note Off has implicit order 0 and Note On implicit order 2; ties are resolved by the normative ordering rules. |
| `channel` | midiChannel | optional | — |  |
| `type` | const `polyPressure` | yes | const="polyPressure" |  |
| `targetNoteId` | id | optional | — | Optional semantic association with an existing concrete note. It does not replace channel/pitch wire semantics. |
| `pitch` | pitch | yes | — |  |
| `value` | integer | yes | minimum=0; maximum=127 |  |

## `midiEvent`

```json
{
  "oneOf": [
    {
      "$ref": "#/$defs/controlChangeEvent"
    },
    {
      "$ref": "#/$defs/channelModeEvent"
    },
    {
      "$ref": "#/$defs/programChangeEvent"
    },
    {
      "$ref": "#/$defs/pitchBendEvent"
    },
    {
      "$ref": "#/$defs/channelPressureEvent"
    },
    {
      "$ref": "#/$defs/polyPressureEvent"
    }
  ]
}
```

Structural condition blocks present: `oneOf`. Consult the schema for exact machine-readable logic.


## `lyricVoice`

Explicit relationship between a vocal/melody track and a lyric document. Several voices may reference the same lyric document without duplicating text.

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `id` | id | yes | — |  |
| `name` | string | optional | — |  |
| `role` | string | optional | minLength=1 | Open authored role. Recommended values include lead, harmony, backing, unison, double, counter, adlib, response. |
| `trackId` | id | yes | — |  |
| `documentId` | id | yes | — |  |

## Properties defined inside conditional/nested inline objects

Some properties are declared inside `oneOf` branches or inline nested objects rather than directly in a named `$defs` property table. They are listed here so the reference covers every property name in the recursive schema.

| Field | Location(s) | Type |
|---|---|---|
| `customRoleColors` | `root/$defs/legacy33/$defs/appearanceData/properties/appearance/properties/customRoleColors`<br>`root/$defs/presentation/properties/appearance/properties/customRoleColors` | customRoleColorMap |
| `objectColors` | `root/$defs/legacy33/$defs/appearanceData/properties/appearance/properties/objectColors`<br>`root/$defs/presentation/properties/appearance/properties/objectColors` | array |
| `refs` | `root/$defs/dependencies/oneOf/1/properties/refs`<br>`root/$defs/legacy33/$defs/dependencies/oneOf/1/properties/refs` | array |
| `roleColors` | `root/$defs/legacy33/$defs/appearanceData/properties/appearance/properties/roleColors`<br>`root/$defs/presentation/properties/appearance/properties/roleColors` | roleColorMap |
| `scope` | `root/$defs/dependencies/oneOf/0/properties/scope`<br>`root/$defs/dependencies/oneOf/1/properties/scope`<br>`root/$defs/legacy33/$defs/dependencies/oneOf/0/properties/scope`<br>`root/$defs/legacy33/$defs/dependencies/oneOf/1/properties/scope` | const `objects` / enum<document | none> |

## Coverage statement

Recursive definitions enumerated: **156 / 156** (top-level 91, nested 65). Unique property names covered across the full recursive schema: **207 / 207**.
