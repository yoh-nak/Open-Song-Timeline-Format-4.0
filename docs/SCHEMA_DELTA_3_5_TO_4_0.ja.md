# OSTF 3.5.0 → 4.0.0 Schema差分

4.0は3.5 Schemaを置き換えて再設計するのではなく、3.5の既存Core構造を維持したMajor拡張です。

## Root

追加：

- `midi`（optional）

変更：

- `version`: `3.5.0` → `4.0.0`

既存10必須Rootは変更なし。

## `song`

追加：

- `melodyTrackIds` — optional ordered unique Track ID array

維持：

- `primaryMelodyTrackId`
- `primaryLyricDocumentId`

## `track`

追加：

- `midi`
  - `mode`: `singleChannel | multiChannel | mpe`
  - `busId` optional
  - `channel` for singleChannel
  - `mpe` for MPE

## `note`

追加：

- `channel`: optional MIDI Channel 1–16

3.5の既存Noteフィールド・意味は変更なし。

## `clip`

追加：

- `midiEvents[]`

Note On/Offは`midiEvents`へ移動せず、従来どおり`notes[]`が正本。

## 新しいMIDI Event型

- `controlChangeEvent`
- `channelModeEvent`
- `programChangeEvent`
- `pitchBendEvent`
- `channelPressureEvent`
- `polyPressureEvent`
- union `midiEvent`

## 新しいMIDI設定型

- `midiChannel`
- `midiBus`
- `midi`
- `trackMidi`
- `mpeSettings`

## `lyrics`

追加：

- `voices[]`

新型：

- `lyricVoice`

## `lyricAlignment`

追加：

- `voiceId` optional

## `ref.type`

追加：

- `midiBus`
- `midiEvent`
- `lyricVoice`

## Localization

`localizationTarget.kind`へ追加：

- `midiBus`
- `lyricVoice`

翻訳対象fieldは`name`。

## `soundSource`

構造は変更なし。

意味説明のみ更新し、4.0では`track.midi.busId`がLogical MIDI routingのCore参照であることを明確化。`soundSource.identifier`は物理Port正本ではない。

## 変更しない主な3.5領域

- Timeline / Sections / Tempo / Meter / Key Changes
- Harmony / Chords / Scales / Voicing
- Sound Slots / Drum Kits / MIDI note mapping
- Note enabled / releaseVelocity / probability / velocityDeviation / pitchRole
- Relative Phrases
- Generation
- Presentation
- Phrase Library
- Clip Choices
- Provenance / Document identity / Merge rules
- Resources / Contributors / Rights / Genres / Moods
