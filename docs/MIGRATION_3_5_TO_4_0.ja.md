# OSTF 3.5.0 → 4.0.0 移行規則

## 1. 原則

OSTF 3.5.0を読み込んだだけでは4.0.0へ変更しません。3.5.0は3.5.0のSchemaと意味で保持し、利用者が4.0保存または4.0機能の追加を明示した場合だけ4.0.0へ移行します。

3.5→4.0は既存Note、Harmony、Lyrics、Generation、Presentation等の意味を変更しません。4.0で新しく追加された情報がない場合、既存Core値を変換する必要はありません。

## 2. 最小移行

必須変更はルート`version`を`"4.0.0"`にすることだけです。ただし、4.0 Schemaへの保存を選んだ時点で4.0の構造・意味規則すべてに適合させます。

次のフィールドは情報がない場合に自動追加しません。

- `midi`
- `song.melodyTrackIds`
- `tracks[].midi`
- `tracks[].clips[].notes[].channel`
- `tracks[].clips[].midiEvents`
- `lyrics.voices`
- `lyrics.alignments[].voiceId`

Schemaのdefaultは保存時の自動追記命令ではありません。

## 3. 複数Melodyへの移行

3.5の`primaryMelodyTrackId`はそのまま保持します。

`melodyTrackIds`を追加する場合、既知のPrimaryを最初の候補として含められますが、Track名、role、音域、歌詞の有無、配列位置から他のMelody Trackを推測しません。

例：

```json
{
  "primaryMelodyTrackId": "melody-main",
  "melodyTrackIds": ["melody-main", "melody-counter"]
}
```

Primaryとlistを併記するとき、Primaryはlistに含めます。

## 4. Lyric Voiceへの移行

3.5の複数Lyric Documentはそのまま保持します。Documentが複数あることだけからLead/Harmony/Backingを推測しません。

Voice関係が明示されている移行元だけ、次のように4.0 Coreへ移します。

```json
{
  "id": "voice-high",
  "role": "harmony",
  "trackId": "vocal-high",
  "documentId": "lyrics-main"
}
```

同じ本文をLeadとHarmonyが歌う場合、Lyric Documentを複製せず同一`documentId`を複数Voiceから参照します。

既存Alignmentへ`voiceId`を付けるのは、aligned UnitのDocumentとaligned Noteの所属TrackからVoiceが一意に確定できる場合だけです。曖昧なら省略したまま保持します。

## 5. MIDI Channelへの移行

4.0 Channel保存値は1～16です。

MIDI wire、SMF、DAW API等が0～15 Channel indexを返す場合は、移行元の契約が明確な場合に限り`+1`して保存します。元データの番号体系が不明な文字列やUI値を推測変換しません。

Track全体が一つのChannelであることが明示されている場合：

```json
"midi": {
  "mode": "singleChannel",
  "channel": 1
}
```

複数Channelが明示されている場合：

```json
"midi": {
  "mode": "multiChannel"
}
```

として各Note/EventへChannelを保持します。

## 6. Logical MIDI Busへの移行

複数Port相当の情報がある場合、物理Port名そのものをCoreの正本にせず、同一論理出力空間を表すBusへ変換できます。

```json
"midi": {
  "buses": [
    {"id": "bus-a", "name": "External A"},
    {"id": "bus-b", "name": "External B"}
  ]
}
```

Trackは`track.midi.busId`で参照します。

次をBus IDとして自動採用しません。

- Windows/macOSのPort名
- USB Device ID
- Ableton LiveのMIDI To表示文字列
- Cubase/Logic等のTrack番号
- Plugin instance名

それらはHost側Mapping情報です。

3.5の`soundSource.identifier`にPortらしい値があっても、自動的にBusへ移しません。

## 7. Control Change

MIDI 1.0 CCを次へ移します。

```json
{
  "id": "cc-001",
  "start": 960,
  "type": "controlChange",
  "channel": 1,
  "controller": 1,
  "value": 96
}
```

Controller 0～119は`controlChange`。
Controller 120～127は`channelMode`。

未知Controllerでも番号と値が有効範囲なら保持し、既知名へ推測変換しません。

## 8. Bank Select / Program Change

Bank Selectは専用bankオブジェクトへ畳みません。

```json
[
  {"id":"bank-msb","start":0,"type":"controlChange","channel":1,"controller":0,"value":0},
  {"id":"bank-lsb","start":0,"type":"controlChange","channel":1,"controller":32,"value":4},
  {"id":"program","start":0,"type":"programChange","channel":1,"program":42}
]
```

ProgramはMIDI wire値0～127を保存します。UIが1～128の場合、UI値であることが明確なら1を引きます。不明な番号を推測しません。

## 9. Pitch Bend

MIDIのLSB/MSBから14-bit値を正確に組み立てます。

`value14 = LSB + 128 * MSB`

範囲0～16383、中央8192です。

Pitch Bend Sensitivityを`value14`へ焼き込みません。感度を保持する場合は元のRPN CC列として移行します。

## 10. Aftertouch / Pressure

Channel Aftertouchは：

```json
{"type":"channelPressure","value":80}
```

Polyphonic Aftertouchは：

```json
{"type":"polyPressure","pitch":64,"value":90}
```

へ移します。

MIDI上で別メッセージなので相互変換しません。

## 11. RPN / NRPN / 14-bit CC

4.0.0ではcompound Eventへまとめません。CC番号と値を元の順序のまま複数`controlChange`として保存します。

これにはBank Select、Data Entry、RPN/NRPN Select、14-bit controller pair等を含みます。

同tick順序が意味を持つ場合は保存配列順または`order`で保持します。

## 12. MPE

MPEと明示された移行元のみ`track.midi.mode="mpe"`へ移します。通常の複数ChannelデータをNote数やPitch Bendの分布だけからMPEと推測しません。

Lower Zone：Master Ch1、MemberはCh2から上方向。
Upper Zone：Master Ch16、MemberはCh15から下方向。

保存は`zone`と`memberChannelCount`を正本とし、Master/Member配列を重複保存しません。

NoteごとのChannel、Pitch Bend、Channel Pressure、CC等は通常のNote/MIDI Eventとして保持します。

## 13. 同tick順序

元データに明示的なイベント順がある場合、4.0の`order`と同一Clip内配列順へ写します。

Canonical基準：

- Note Off: implicit order 0
- MIDI Event: default order 1
- Note On: implicit order 2

元データがこれと違う順を要求する場合、Eventの`order`を変更して保持します。

## 14. 4.0 → 3.5 Downgrade

次は3.5で標準表現できません。

- `midi`
- `track.midi`
- Note `channel`
- `clip.midiEvents`
- MPE zone
- `song.melodyTrackIds`
- `lyrics.voices`
- Alignment `voiceId`

Writerは削除される情報をパス単位で提示し、確認なしにダウングレードしません。

4.0 MIDI Eventを3.5の独自`extensions`へ書き戻すことをOSTF標準Downgradeとは呼びません。

## 15. 非推測原則

次の自動推定を禁止します。

- 低音域Note → Keyswitch
- 複数Channel → MPE
- Portらしい文字列 → Logical Bus
- Track名"Harmony" → melodyTrackIds/Voice
- 複数Lyric Document → Lead/Backing関係
- Program表示番号の0-origin/1-origin判定
- Pitch Bend値 → Bend Range
- CC番号 → 音楽的意味の置換

意味が明示できない情報は原データを保持し、移行未確定として扱います。
