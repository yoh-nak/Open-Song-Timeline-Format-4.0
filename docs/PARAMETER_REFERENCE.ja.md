# OSTF 4.0.0 — パラメーターリファレンス

> English: [`PARAMETER_REFERENCE.md`](PARAMETER_REFERENCE.md)

構造の正本は[`ostf-4.0.0.schema.json`](../schema/ostf-4.0.0.schema.json)、意味の正本は[`FORMAT_SPECIFICATION.ja.md`](FORMAT_SPECIFICATION.ja.md)です。
## リファレンスの読み方

本書は4.0 Schemaの`$defs`を全件列挙しており、Rootを含む全構造パラメーターを対象とする。型・required・数値範囲はSchemaを正本とし、相互参照、継承、同tick順序、MPE Zone、Compatibility Playback等のSchemaだけでは表せない制約は`FORMAT_SPECIFICATION.ja.md`を正本とする。

OSTF Playerが4.0の一部機能を未実装でも、このリファレンス上のパラメーターがOptionalであることと、Writerが削除してよいことは同義ではない。詳細は`PLAYER_COMPATIBILITY.ja.md`を参照する。


## Root

| Field | Type | Required | Constraints |
|---|---|---|---|
| `format` | const `OSTF` | yes | — |
| `version` | const `4.0.0` | yes | — |
| `ppq` | integer | yes | minimum=1; maximum=960000 |
| `song` | song | yes | — |
| `timeline` | timeline | yes | — |
| `harmony` | harmony | yes | — |
| `tracks` | array<track> | yes | — |
| `soundSlots` | array<soundSlot> | yes | — |
| `lyrics` | lyrics | yes | — |
| `resources` | array<resource> | yes | — |
| `extensions` | extensions | optional | — |
| `drumKits` | array<drumKit> | optional | — |
| `contributors` | array<contributor> | optional | — |
| `document` | documentInfo | optional | — |
| `relativePhrases` | relativePhrases | optional | — |
| `generation` | generation | optional | — |
| `presentation` | presentation | optional | — |
| `phraseLibrary` | phraseLibrary | optional | — |
| `clipChoices` | clipChoices | optional | — |
| `localization` | localization | optional | — |
| `provenance` | provenance | optional | — |
| `midi` | midi | optional | — |

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

| Field | Type | Required | Constraints |
|---|---|---|---|
| `type` | enum `section`, `track`, `clip`, `note`, `chord`, `scale`, `soundSlot`, `drumKit`, `lyricDocument`, `lyricPhrase`, `lyricUnit`, `lyricAlignment`, `resource`, `genre`, `contributor`, `credit`, `alternateTitle`, `mood`, `keyChange`, `phraseTemplate`, `phraseInstance`, `clipChoiceGroup`, `clipCandidate`, `roleDefinition`, `generationProcess`, `provenanceRecord`, `midiBus`, `midiEvent`, `lyricVoice` | yes | — |
| `id` | id | yes | — |

## `range`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |

## `spelledPitchClass`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `step` | enum `A`, `B`, `C`, `D`, `E`, `F`, `G` | yes | — |
| `alter` | integer | yes | minimum=-2; maximum=2 |

## `song`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `title` | string | yes | — |
| `primaryMelodyTrackId` | id | optional/conditional | — |
| `localizedTitles` | localizedNames | optional/conditional | — |
| `subtitle` | string | optional/conditional | — |
| `description` | string | optional/conditional | — |
| `alternateTitles` | array<alternateTitle> | optional/conditional | — |
| `credits` | array<credit> | optional/conditional | — |
| `genres` | array<genre> | optional/conditional | — |
| `key` | key | optional/conditional | — |
| `scale` | scaleDefinition | optional/conditional | — |
| `languages` | array<string> | optional/conditional | uniqueItems=true |
| `primaryLyricDocumentId` | id | optional/conditional | — |
| `moods` | array<mood> | optional/conditional | — |
| `rights` | rightsStatement | optional/conditional | — |
| `melodyTrackIds` | array<id> | optional/conditional | uniqueItems=true |

## `section`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `role` | string | optional/conditional | — |
| `localizedNames` | localizedNames | optional/conditional | — |

## `tempo`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `at` | nonnegative | yes | — |
| `bpm` | number | yes | maximum=1000; exclusiveMinimum=0 |

## `meter`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `at` | nonnegative | yes | — |
| `numerator` | integer | yes | minimum=1; maximum=64 |
| `denominator` | enum `1`, `2`, `4`, `8`, `16`, `32`, `64` | yes | — |
| `beatGroups` | array<integer> | optional/conditional | minItems=1; maxItems=64 |

## `timeline`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `sections` | array<section> | yes | — |
| `tempoChanges` | array<tempo> | yes | minItems=1 |
| `meterChanges` | array<meter> | yes | minItems=1 |
| `keyChanges` | array<keyChange> | optional/conditional | — |
| `duration` | nonnegative | optional/conditional | — |
| `barOrigin` | nonnegative | optional/conditional | — |

## `chord`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `label` | string | yes | minLength=1 |
| `root` | spelledPitchClass | optional/conditional | — |
| `intervals` | array<integer> | optional/conditional | minItems=1; maxItems=36; uniqueItems=true |
| `bass` | spelledPitchClass | optional/conditional | — |
| `voicing` | voicing | optional/conditional | — |

## `scale`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `label` | string | optional/conditional | — |
| `tonic` | spelledPitchClass | yes | — |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true |
| `targets` | array<scaleTarget> | optional/conditional | minItems=1; uniqueItems=true |

## `harmony`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `chords` | array<chord> | yes | — |
| `scales` | array<scale> | yes | — |

## `instrument`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `name` | string | yes | — |
| `family` | string | optional/conditional | — |
| `localizedNames` | localizedNames | optional/conditional | — |

## `note`

Concrete Core DAW-note event. The event target is pitch or soundSlotId. Optional 3.5 note-local properties preserve mute, release velocity, probability and velocity variation. pitchRole=fixedTrigger marks a literal MIDI key trigger that must not be musically transposed or analyzed.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `duration` | positive | yes | — |
| `pitch` | pitch | optional/conditional | — |
| `soundSlotId` | id | optional/conditional | — |
| `velocity` | integer | optional/conditional | minimum=1; maximum=127 |
| `enabled` | boolean | optional/conditional | default=True |
| `releaseVelocity` | integer | optional/conditional | minimum=0; maximum=127 |
| `probability` | number | optional/conditional | minimum=0; maximum=1; default=1 |
| `velocityDeviation` | integer | optional/conditional | minimum=-126; maximum=126; default=0 |
| `pitchRole` | enum `musical`, `fixedTrigger` | optional/conditional | default=musical |
| `channel` | midiChannel | optional/conditional | — |

## `clip`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | optional/conditional | — |
| `start` | nonnegative | yes | — |
| `duration` | positive | yes | — |
| `enabled` | boolean | optional/conditional | default=True |
| `notes` | array<note> | yes | — |
| `localizedNames` | localizedNames | optional/conditional | — |
| `midiEvents` | array<midiEvent> | optional/conditional | — |

## `track`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `role` | string | optional/conditional | — |
| `instrument` | instrument | optional/conditional | — |
| `enabled` | boolean | optional/conditional | default=True |
| `clips` | array<clip> | yes | — |
| `trackType` | enum `melodic`, `drum`, `mixed` | optional/conditional | — |
| `preferredView` | enum `auto`, `pianoRoll`, `drumGrid` | optional/conditional | default=auto |
| `soundSource` | soundSource | optional/conditional | — |
| `localizedNames` | localizedNames | optional/conditional | — |
| `drumKitId` | id | optional/conditional | — |
| `midi` | trackMidi | optional/conditional | — |

## `soundSlot`

Document-local stable logical percussion target. Equal roles, names or MIDI note mappings never merge distinct slot IDs. No sample/audio identity is encoded.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `role` | string | optional/conditional | — |
| `midiNote` | pitch | optional/conditional | — |
| `midiMapping` | midiMapping | optional/conditional | — |
| `localizedNames` | localizedNames | optional/conditional | — |

## `lyricDocument`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `title` | string | optional/conditional | — |
| `language` | string | optional/conditional | — |

## `lyricPhrase`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `documentId` | id | yes | — |
| `text` | string | yes | — |
| `pronunciation` | string | optional/conditional | — |
| `start` | nonnegative | optional/conditional | — |
| `duration` | positive | optional/conditional | — |

## `lyricUnit`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `phraseId` | id | yes | — |
| `text` | string | yes | — |
| `pronunciation` | string | optional/conditional | — |
| `start` | nonnegative | optional/conditional | — |
| `duration` | positive | optional/conditional | — |

## `lyricAlignment`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `unitIds` | array<id> | yes | minItems=1; uniqueItems=true |
| `noteIds` | array<id> | yes | minItems=1; uniqueItems=true |
| `voiceId` | id | optional/conditional | — |

## `lyrics`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `documents` | array<lyricDocument> | yes | — |
| `phrases` | array<lyricPhrase> | yes | — |
| `units` | array<lyricUnit> | yes | — |
| `alignments` | array<lyricAlignment> | yes | — |
| `voices` | array<lyricVoice> | optional/conditional | — |

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

## `resource`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `kind` | enum `audio`, `midi`, `text`, `other` | yes | — |
| `uri` | string | yes | minLength=1 |
| `name` | string | optional/conditional | — |
| `sha256` | sha256 | optional/conditional | — |

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

## `relativeAssignment`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `noteId` | id | yes | — |
| `basis` | enum `chordTone`, `scaleDegree` | yes | — |
| `contextId` | id | yes | — |
| `anchorPitch` | pitch | yes | — |
| `index` | integer | yes | minimum=1; maximum=36 |
| `chromaticOffset` | integer | yes | minimum=-24; maximum=24 |

## `generator`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | string | yes | minLength=1 |
| `version` | string | yes | minLength=1 |

## `generationInput`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `role` | string | yes | minLength=1 |
| `ref` | ref | yes | — |
| `readRange` | range | optional/conditional | — |

## `generationProcess`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `generator` | generator | yes | — |
| `inputs` | array<generationInput> | yes | — |
| `outputs` | array<ref> | yes | minItems=1 |
| `targetRange` | range | optional/conditional | — |
| `protectedNoteIds` | array<id> | yes | uniqueItems=true |
| `intent` | generationIntent | optional/conditional | — |

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

## `roleDefinition`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `baseRole` | string | optional/conditional | — |

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

| Field | Type | Required | Constraints |
|---|---|---|---|
| `target` | ref | yes | — |
| `color` | color | yes | — |

## `phraseTemplate`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `duration` | positive | yes | — |
| `notes` | array<corePhraseSourceNote> | yes | — |
| `name` | string | optional/conditional | — |

## `phraseNoteMapping`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `templateNoteId` | id | yes | — |
| `repeatIndex` | nonnegative | yes | — |
| `noteId` | id | yes | — |

## `phraseInstance`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `templateId` | id | yes | — |
| `clipId` | id | yes | — |
| `repeatCount` | positive | yes | — |
| `transpose` | integer | yes | minimum=-127; maximum=127 |
| `noteMap` | array<phraseNoteMapping> | yes | — |
| `velocityMode` | enum `independent`, `linked` | optional/conditional | default=independent |
| `noteStateMode` | enum `independent`, `linked` | optional/conditional | default=independent |

## `clipCandidate`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `clipIds` | array<id> | yes | uniqueItems=true |
| `name` | string | optional/conditional | — |

## `clipChoiceGroup`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `candidates` | array<clipCandidate> | yes | minItems=1 |
| `selectedCandidateId` | schema | yes | — |
| `name` | string | optional/conditional | — |

## `midiMapping`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `standard` | enum `gm1`, `custom` | yes | — |
| `note` | pitch | yes | — |

## `soundSource`

Optional informational rendering class or opaque destination hint. In OSTF 4.0, track.midi.busId is the Core logical MIDI routing reference; soundSource does not bind a physical device, plugin state or preset.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `kind` | enum `unassigned`, `gm1`, `externalMidi`, `plugin`, `hostManaged` | yes | — |
| `identifier` | string | optional/conditional | minLength=1 |

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

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |
| `localizedNames` | localizedNames | optional/conditional | — |
| `soundSlotIds` | array<id> | yes | uniqueItems=true |

## `voicing`

Optional informational chord voicing snapshot, not a played Core note collection. Repeated pitches and original array order have meaning for lossless preservation. Per-entry velocity is an optional voicing-preview hint.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `pitches` | array<pitch> | yes | minItems=1 |
| `velocities` | array<schema> | optional/conditional | minItems=1 |

## `contributor`

One song-local identity; same contributor can receive multiple credit roles. Unicode source name is authoritative.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `kind` | enum `person`, `organization`, `other` | optional/conditional | — |
| `localizedNames` | localizedNames | optional/conditional | — |

## `credit`

Ordered credit assignment referencing one contributor; role is an open semantic token, not a translated label.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `contributorId` | id | yes | — |
| `role` | string | yes | pattern=^[a-z][a-z0-9.-]*$ |
| `label` | string | optional/conditional | — |

## `alternateTitle`

Stable-ID song title alternative; array position and title text do not identify it.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `title` | string | yes | minLength=1 |

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

| Field | Type | Required | Constraints |
|---|---|---|---|
| `kind` | enum `song`, `alternateTitle`, `contributor`, `credit`, `section`, `track`, `clip`, `soundSlot`, `drumKit`, `lyricDocument`, `resource`, `phraseTemplate`, `clipCandidate`, `clipChoiceGroup`, `roleDefinition`, `genre`, `mood`, `keyChange`, `scale`, `midiBus`, `lyricVoice` | yes | — |
| `id` | id | optional/conditional | — |

## `localizationEntry`

Independent translations for one stable source field. Original source text remains authoritative as fallback.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `target` | localizationTarget | yes | — |
| `field` | string | yes | minLength=1 |
| `values` | languageMap32 | yes | — |

## `genre`

One song-local open genre tag. The label is authored Unicode text and not a predefined controlled taxonomy; ID is stable for metadata merges and localization.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `externalUri` | string | optional/conditional | minLength=1 |

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

| Field | Type | Required | Constraints |
|---|---|---|---|
| `documentId` | opaqueDocumentId | yes | — |
| `revisionId` | opaqueDocumentId | yes | — |
| `sha256` | sha256 | optional/conditional | — |

## `software`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `name` | string | yes | minLength=1 |
| `version` | string | optional/conditional | minLength=1 |

## `documentInfo`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | opaqueDocumentId | yes | — |
| `revisionId` | opaqueDocumentId | yes | — |
| `parents` | array<revisionRef> | optional/conditional | uniqueItems=true |
| `createdBy` | software | optional/conditional | — |
| `modifiedBy` | software | optional/conditional | — |

## `key`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `state` | enum `tonal`, `atonal`, `unspecified` | yes | — |
| `tonic` | spelledPitchClass | optional/conditional | — |
| `mode` | string | optional/conditional | minLength=1 |
| `label` | string | optional/conditional | — |

## `keyChange`

A change after tick zero; initial key is stored only at song.key. Change at tick 0 is forbidden to prevent dual authority.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `at` | positive | yes | — |
| `key` | key | yes | — |
| `label` | string | optional/conditional | — |

## `scaleDefinition`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `tonic` | spelledPitchClass | yes | — |
| `intervals` | array<integer> | yes | minItems=1; maxItems=12; uniqueItems=true |
| `label` | string | optional/conditional | — |

## `scaleTarget`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `type` | enum `section`, `track`, `clip`, `chord`, `note` | yes | — |
| `id` | id | yes | — |

## `mood`

Authored descriptive mood tag; not a generator preset or a probability.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | minLength=1 |
| `externalUri` | string | optional/conditional | minLength=1 |

## `rightsStatement`

Authored rights statement only; no rights or permissions are inferred or granted by the format.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `notice` | string | optional/conditional | — |
| `licenseUri` | string | optional/conditional | minLength=1 |
| `rightsHolderIds` | array<id> | optional/conditional | uniqueItems=true |

## `grooveIntent`

Descriptive generation intent only. Never re-apply to already stored notes. unitTicks is one unswung subdivision; two subdivisions form a pair.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `name` | string | optional/conditional | — |
| `unitTicks` | positive | optional/conditional | — |
| `longWeight` | integer | optional/conditional | minimum=1; maximum=1000000 |
| `shortWeight` | integer | optional/conditional | minimum=1; maximum=1000000 |

## `generationIntent`

Portable description of intent, not a promise to reproduce an algorithm. Does not change song.genres or song.moods.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `genreNames` | array<string> | optional/conditional | — |
| `moodNames` | array<string> | optional/conditional | — |
| `groove` | grooveIntent | optional/conditional | — |
| `prompt` | string | optional/conditional | — |
| `seed` | string | optional/conditional | — |

## `relativePhrases`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `status` | enum `current`, `stale`, `unverified` | yes | — |
| `dependencies` | dependencies | yes | — |
| `assignments` | array<relativeAssignment> | yes | — |

## `generation`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `status` | enum `current`, `stale`, `unverified` | yes | — |
| `dependencies` | dependencies | yes | — |
| `processes` | array<generationProcess> | yes | — |

## `presentation`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `status` | enum `current`, `stale`, `unverified` | yes | — |
| `dependencies` | dependencies | yes | — |
| `roleDefinitions` | array<roleDefinition> | yes | — |
| `sectionRoleAssignments` | object | yes | — |
| `appearance` | object | yes | — |

## `phraseLibrary`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `status` | enum `current`, `stale`, `unverified` | yes | — |
| `dependencies` | dependencies | yes | — |
| `templates` | array<phraseTemplate> | yes | — |
| `instances` | array<phraseInstance> | yes | — |

## `clipChoices`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `status` | enum `current`, `stale`, `unverified` | yes | — |
| `dependencies` | dependencies | yes | — |
| `groups` | array<clipChoiceGroup> | yes | — |

## `localization`

Standard optional Core object under root version 4.0.0, not an extension. Current relationships must be consistent with saved Core; loading never performs editing or generation.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `status` | enum `current`, `stale`, `unverified` | yes | — |
| `dependencies` | dependencies | yes | — |
| `entries` | array<localizationEntry> | yes | — |

## `corePhraseSourceNote`

Template-local source note for phrase materialization. It carries the same note-local 3.5 properties as concrete notes. It is not performed by itself; concrete track notes remain the saved playback events.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `duration` | positive | yes | — |
| `pitch` | pitch | optional/conditional | — |
| `soundSlotId` | id | optional/conditional | — |
| `velocity` | integer | optional/conditional | minimum=1; maximum=127 |
| `enabled` | boolean | optional/conditional | default=True |
| `releaseVelocity` | integer | optional/conditional | minimum=0; maximum=127 |
| `probability` | number | optional/conditional | minimum=0; maximum=1; default=1 |
| `velocityDeviation` | integer | optional/conditional | minimum=-126; maximum=126; default=0 |
| `pitchRole` | enum `musical`, `fixedTrigger` | optional/conditional | default=musical |

## `provenanceSource`

Historical source; hashes refer to exact source bytes, not the current document. No automatic retrieval.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `format` | string | optional/conditional | minLength=1 |
| `version` | string | optional/conditional | minLength=1 |
| `sha256` | sha256 | optional/conditional | — |
| `uri` | string | optional/conditional | minLength=1 |
| `documentId` | opaqueDocumentId | optional/conditional | — |
| `revisionId` | opaqueDocumentId | optional/conditional | — |
| `resourceId` | id | optional/conditional | — |

## `provenanceRecord`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `action` | enum `create`, `import`, `conversion`, `edit`, `transpose`, `merge`, `generation`, `other` | yes | — |
| `description` | string | optional/conditional | — |
| `tool` | software | optional/conditional | — |
| `sources` | array<provenanceSource> | optional/conditional | — |
| `semitones` | integer | optional/conditional | minimum=-127; maximum=127 |
| `beforeKey` | key | optional/conditional | — |
| `afterKey` | key | optional/conditional | — |

## `provenance`

Non-executable historical assertions; not composer credits and not a substitute for current metadata. Does not prove authorship or operation correctness.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `records` | array<provenanceRecord> | yes | — |

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

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | yes | — |

## `midi`

| Field | Type | Required | Constraints |
|---|---|---|---|
| `buses` | array<midiBus> | yes | — |

## `mpeSettings`

Semantic MPE zone declaration. Lower-zone master is channel 1 and upper-zone master is channel 16; member channels are derived contiguously from memberChannelCount.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `zone` | enum `lower`, `upper` | yes | — |
| `memberChannelCount` | integer | yes | minimum=1; maximum=15 |

## `trackMidi`

Optional MIDI channel/routing interpretation for a track. busId is a logical OSTF bus, never a physical MIDI port.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `busId` | id | optional/conditional | — |
| `mode` | enum `singleChannel`, `multiChannel`, `mpe` | yes | — |
| `channel` | midiChannel | optional/conditional | — |
| `mpe` | mpeSettings | optional/conditional | — |

## `controlChangeEvent`

MIDI 1.0 Control Change. Controllers 120..127 use channelMode instead.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional/conditional | default=1 |
| `channel` | midiChannel | optional/conditional | — |
| `type` | const `controlChange` | yes | — |
| `targetNoteId` | id | optional/conditional | — |
| `controller` | integer | yes | minimum=0; maximum=119 |
| `value` | integer | yes | minimum=0; maximum=127 |

## `channelModeEvent`

MIDI 1.0 Channel Mode message represented separately from ordinary Control Change.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional/conditional | default=1 |
| `channel` | midiChannel | optional/conditional | — |
| `type` | const `channelMode` | yes | — |
| `controller` | integer | yes | minimum=120; maximum=127 |
| `value` | integer | yes | minimum=0; maximum=127 |

## `programChangeEvent`

MIDI 1.0 Program Change using the wire data value 0..127. UI may display 1..128.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional/conditional | default=1 |
| `channel` | midiChannel | optional/conditional | — |
| `type` | const `programChange` | yes | — |
| `program` | integer | yes | minimum=0; maximum=127 |

## `pitchBendEvent`

MIDI 1.0 14-bit Pitch Bend Change. Bend range is receiver state/RPN data, not encoded in value14.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional/conditional | default=1 |
| `channel` | midiChannel | optional/conditional | — |
| `type` | const `pitchBend` | yes | — |
| `targetNoteId` | id | optional/conditional | — |
| `value14` | integer | yes | minimum=0; maximum=16383 |

## `channelPressureEvent`

MIDI 1.0 Channel Pressure (channel aftertouch).

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional/conditional | default=1 |
| `channel` | midiChannel | optional/conditional | — |
| `type` | const `channelPressure` | yes | — |
| `targetNoteId` | id | optional/conditional | — |
| `value` | integer | yes | minimum=0; maximum=127 |

## `polyPressureEvent`

MIDI 1.0 Polyphonic Key Pressure (poly aftertouch).

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `start` | signed | yes | — |
| `order` | signed | optional/conditional | default=1 |
| `channel` | midiChannel | optional/conditional | — |
| `type` | const `polyPressure` | yes | — |
| `targetNoteId` | id | optional/conditional | — |
| `pitch` | pitch | yes | — |
| `value` | integer | yes | minimum=0; maximum=127 |

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

## `lyricVoice`

Explicit relationship between a vocal/melody track and a lyric document. Several voices may reference the same lyric document without duplicating text.

| Field | Type | Required | Constraints |
|---|---|---|---|
| `id` | id | yes | — |
| `name` | string | optional/conditional | — |
| `role` | string | optional/conditional | minLength=1 |
| `trackId` | id | yes | — |
| `documentId` | id | yes | — |
