# OSTF 4.0 — Demo / Fixture Coverage Matrix

この表はOSTF 4.0で新規追加したCore領域について、**どの公開`.ostf`が実例を持つか**を追跡するためのものです。

`demo-songs`は音楽的な統合例、`feature-fixtures`は境界値・規則確認用です。

## 1. 4.0新規Coreフィールド

| 4.0追加項目 | Demo Song | Feature Fixture | 状態 |
|---|---|---|---|
| root `midi` | Demo 01/05/07/08/12ほか | Multiple Buses | Covered |
| `midi.buses[].id/name` | Demo 05/12 | Multiple Buses | Covered |
| `tracks[].midi` | 全MIDI Demo | Channel/MPE Fixtures | Covered |
| `track.midi.mode=singleChannel` | Demo 01等 | Single Channel | Covered |
| `track.midi.mode=multiChannel` | Demo 02/12 | MultiChannel 1/16 | Covered |
| `track.midi.mode=mpe` | Demo 07/08/12 | MPE Lower/Upper | Covered |
| `track.midi.busId` | Demo 05/12 | Multiple Buses | Covered |
| `track.midi.channel` | Demo 01等 | Channel 1/16 | Covered |
| `track.midi.mpe.zone` | Demo 07/08/12 | Lower/Upper | Covered |
| `track.midi.mpe.memberChannelCount` | Demo 07/08/12 | 1/15 boundaries | Covered |
| `notes[].channel` | Demo 02/07/08/12等 | Channel fixtures | Covered |
| `clips[].midiEvents` | Demo 01/02/03/05/07/08/12 | Event fixtures | Covered |
| `midiEvent.id/start/type` | MIDI Demo群 | 全Event Fixture | Covered |
| `midiEvent.channel` | Demo 02/07/08/12 | Explicit Channel | Covered |
| `midiEvent.order` | — | Same Tick Order | Covered |
| CC `controller/value` | Demo 01/05/07/12 | CC min/max/common | Covered |
| Program `program` | Demo 01/02/04/12 | Program 0/127 | Covered |
| Pitch Bend `value14` | Demo 02/03/05/07/08/12 | 0/8192/16383 | Covered |
| Pressure `value` | Demo 03/07/08/12 | 0/127 | Covered |
| Poly Pressure `pitch` | Demo 03/08/12 | Poly Pressure Fixtures | Covered |
| `targetNoteId` | Demo 02/03/07/08/12 | All Supported Events | Covered |
| `song.melodyTrackIds` | Demo 06/09/10/12 | Multiple Melody Tracks | Covered |
| `lyrics.voices` | Demo 03/09/10/12 | Shared/Separate Docs | Covered |
| `lyricVoice.id/name/role/trackId/documentId` | Demo 09/10/12 | Voice Fixtures | Covered |
| `alignments[].voiceId` | Demo 03/09/10/12 | Voice Fixtures | Covered |

**4.0で新設したSchemaプロパティは公開例セット上で100%登場します。**

## 2. 値域・境界

| 項目 | 公開例 |
|---|---|
| MIDI Channel minimum 1 | `OSTF4_Fixture_Single_Channel_1.ostf` |
| MIDI Channel maximum 16 | `OSTF4_Fixture_Single_Channel_16.ostf` |
| CC controller minimum 0 | `OSTF4_Fixture_CC_MIN.ostf` |
| CC controller maximum 119 | `OSTF4_Fixture_CC_MAX.ostf` |
| CC value minimum 0 | `OSTF4_Fixture_CC_MIN.ostf` |
| CC value maximum 127 | `OSTF4_Fixture_CC_MAX.ostf` |
| Channel Mode 120～127 | 8個の`OSTF4_Fixture_Channel_Mode_*.ostf` |
| Program minimum 0 | `OSTF4_Fixture_Program_Min_0.ostf` |
| Program maximum 127 | `OSTF4_Fixture_Program_Max_127.ostf` |
| Pitch Bend minimum 0 | `OSTF4_Fixture_Pitch_Bend_Min.ostf` |
| Pitch Bend center 8192 | `OSTF4_Fixture_Pitch_Bend_Center.ostf` |
| Pitch Bend maximum 16383 | `OSTF4_Fixture_Pitch_Bend_Max.ostf` |
| Channel Pressure 0 / 127 | Min / Max fixtures |
| Poly Pressure 0 / 127 | Min / Max fixtures |
| MPE member count 1 | Lower/Upper Members 1 |
| MPE member count 15 | Lower/Upper Members 15 |
| MIDI Event order < 0 / 0 / 1 / 2 / >2 | Same Tick Order fixture |
| MIDI Event negative local start | Negative Clip Local Event Start fixture |

## 3. MIDI意味規則

| 規則 | 代表例 |
|---|---|
| singleChannelのNote/Event Channel省略継承 | `OSTF4_Fixture_Single_Channel_Inheritance.ostf` |
| multiChannelの具体Channel | Demo 02 / MultiChannel Fixture |
| MPE Lower master/member | Demo 07 |
| MPE Upper master/member | Demo 08 |
| 同一BusでLower+Upperを非重複共存 | Demo 12 |
| Bus A Ch1 と Bus B Ch1を別空間として使用 | Multiple Buses Fixture |
| Track MIDI設定なしでもEvent自身のChannelで一意化 | Explicit Channel Without Track MIDI |
| Bank SelectはCC0/32 + Program | Bank Select Fixture / Demo 01 |
| RPNをraw CC列で保持 | RPN Fixture |
| NRPNをraw CC列で保持 | NRPN Fixture |
| per-note CC | TargetNoteId Fixture / Demo 07 |
| per-note Pitch Bend | TargetNoteId Fixture / Demo 07/08 |
| per-note Channel Pressure | TargetNoteId Fixture / Demo 07 |
| per-note Poly Pressure | TargetNoteId Fixture / Demo 08 |
| Probability対象Noteが鳴らない回はtargeted eventも送らない | Probability With TargetNoteId Fixture |
| same-tick順序を`order`で明示 | Same Tick Order Fixture |

## 4. 複数Melody / Voice

| ケース | 代表例 |
|---|---|
| Primary + Counter Melody | Demo 06 |
| Lead + High + Low Harmony | Demo 09 |
| Lead Call + Response + Counter | Demo 10 |
| 同一Lyric Documentを複数Voiceが共有 | Demo 09 / Shared Document Fixture |
| Voiceごとに別Lyric Document | Demo 10 / Separate Documents Fixture |
| Voice-specific Alignment | Demo 03/09/10/12 |
| PrimaryがmelodyTrackIdsに含まれる | 全複数Melody Demo |

## 5. 3.x継承機能との共存

OSTF 4.0はMIDIイベントだけのフォーマットではありません。以下は正式Demoで4.0と共存させています。

| 3.x継承領域 | Demo |
|---|---|
| Chord root/interval | 全Harmony Demo |
| Chord voicing | Demo 03/04/06/07/08/12 |
| Scale / mode | 多数 |
| 3/4 | Demo 03 |
| 7/8 + beatGroups | Demo 11 |
| 5/4 + beatGroups | Demo 11 |
| Tempo Change | Demo 03/11/12 |
| Key Change | Demo 11/12 |
| Sound Slot / Drum Kit | Demo 01/02/05/11/12 |
| releaseVelocity | Demo 01/12 |
| probability | Demo 12 |
| velocityDeviation | Demo 01/12 |
| fixedTrigger | Demo 11/12 |
| Document identity | Demo 12 |
| Contributor/Credit/Rights | Demo 12 |
| Provenance | Demo 12 |
| Localization | Demo 12 |
| Presentation | Demo 12 |
| Generation | Demo 12 |
| Phrase Library | Demo 12 |
| Relative Phrase | Demo 12 |
| Clip Choice | Demo 12 |

## 6. Compatibility Playback静的確認

12個のDemo Songについて、

1. 4.0固有のMIDI/Voice/Melody集合情報をCompatibility Viewから除外
2. `version`を`3.5.0`として静的に検証
3. 3.5から継承した楽曲本体がOSTF 3.5 Schemaに適合することを確認

というチェックを行っています。

結果：**12 / 12 PASS**

これはOSTF Player実機動作試験ではありません。4.0未実装演奏表現を無視した際にも、継承されたNote/Chord/Track/Lyrics構造が3.5として成立していることの静的確認です。
