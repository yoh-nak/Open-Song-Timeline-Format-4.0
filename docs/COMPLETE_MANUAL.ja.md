# OSTF 4.0.0 — 全網羅マニュアル

> English: [`COMPLETE_MANUAL.md`](COMPLETE_MANUAL.md)

本書は **Open Song Timeline Format (OSTF) 4.0.0** を、作成・読込・再生・表示・編集・マージ・移行・検証するための総合実装マニュアルです。

OSTF 4.0.0 の正本は次の3層です。

1. **構造の正本** — `schema/ostf-4.0.0.schema.json`
2. **意味規則の正本** — `docs/FORMAT_SPECIFICATION.ja.md`
3. **全フィールドの型・必須性・値域** — `docs/PARAMETER_REFERENCE.ja.md`

本書はそれらを横断し、「実装するときに何をどの順番で扱うか」「どこを推測してはいけないか」「OSTF 3.xと4.0をどう共存させるか」を実務的に説明します。

---

## 1. OSTF 4.0の位置付け

OSTFはDAWプロジェクト形式ではありません。曲の意味と時間構造を、DAWやアプリケーションに依存せず交換するためのJSONフォーマットです。

OSTF 3.5までに扱っていた主な情報は次の通りです。

- 曲名・ジャンル・ムード・言語
- Key / Scale / Key Change
- Tempo / Meter / Section
- Chord / Scale context / Voicing
- Track / Clip / Note
- Drum Kit / Sound Slot
- Lyrics / Phrase / Unit / Alignment
- Credits / Rights / Resources
- Relative Phrase / Generation / Presentation
- Phrase Library / Clip Choice / Localization
- Document identity / Provenance

OSTF 4.0はこれらを維持しながら、次をCoreへ追加します。

- MIDI Channel 1–16
- Logical MIDI Bus
- `singleChannel` / `multiChannel` / `mpe`
- Control Change
- Channel Mode
- Program Change
- Bank Select（CC 0 / CC 32）
- 14-bit Pitch Bend
- Channel Pressure
- Polyphonic Key Pressure
- MPE Lower / Upper Zone
- per-note expressionと`targetNoteId`
- 同一tick MIDI Event順序
- 複数Melody Track
- 複数Lyric Voice
- Voice-specific Lyric Alignment

したがって4.0は、OSTF 3.5の曲構造を捨ててMIDI中心へ置き換えた版ではありません。

**OSTF 4.0 = OSTF 3.5のStructured Song Model + MIDI Performance Layer + Multi-Melody / Multi-Voice**

です。

---

## 2. OSTF 4.0が扱わないもの

4.0.0 Coreは次を標準化しません。

- 物理MIDI Port名
- USB MIDI device ID
- Ableton Live / Cubase等のTrack番号
- Audio routing
- Mixer state
- Plugin instance / plugin state
- VST/AUパラメーター一般
- Audio Clip
- SysEx
- MIDI Clock
- MIDI Time Code
- MIDI Machine Control
- MIDI Show Control
- MIDI 2.0 UMP / 32-bit Per-Note Controller

Logical MIDI BusはPortableな曲情報ですが、そのBusを「このPCのどの実機Portへ出すか」はHost設定です。

---

## 3. ファイル形式

推奨拡張子は `.ostf` です。

内容はUTF-8 JSON object一つです。

最小ルート概念：

```json
{
  "format": "OSTF",
  "version": "4.0.0",
  "ppq": 960,
  "song": {},
  "timeline": {},
  "harmony": {},
  "tracks": [],
  "soundSlots": [],
  "lyrics": {},
  "resources": []
}
```

実際の必須フィールドと型はSchemaを正本とします。

### 3.1 Root必須項目

4.0でも次の10項目を必須とし、3.5の基本形を維持します。

- `format`
- `version`
- `ppq`
- `song`
- `timeline`
- `harmony`
- `tracks`
- `soundSlots`
- `lyrics`
- `resources`

4.0で追加されたroot `midi`は任意です。MIDI演奏表現を使用しない曲へ空の`midi`を強制してはいけません。

### 3.2 バージョン

4.0.0文書は、

```json
"format": "OSTF",
"version": "4.0.0"
```

とします。

Readerは既知の4.0.0と、未知の4.1/5.xを同一扱いしてはいけません。

---

## 4. PPQと時間

OSTFの時間は整数tickです。

`ppq`は四分音符あたりのtick数です。

同じ曲内で、Tempo、Meter、Section、Chord、Clip、Note、Lyric Alignment、MIDI Eventなどは同じPPQ空間を共有します。

### 4.1 Global timeとClip-local time

代表的に、

- `clip.start` — global tick
- `note.start` — clip-local tick
- `midiEvent.start` — clip-local tick

です。

Noteのglobal onsetは、

```text
clip.start + note.start
```

MIDI Eventのglobal timeも、

```text
clip.start + midiEvent.start
```

です。

Clip-local `start`は一部の仕様で負値を許します。これは弱起やpre-roll的なEventを表すためのもので、Feature Fixtureで例示しています。

### 4.2 PPQ変換

異なるPPQの文書をマージする場合は、有理時間を壊さない共通PPQへ正確に変換します。

丸めが必要な場合、黙ってtickを丸めてはいけません。

---

## 5. IDと参照

OSTFはStable IDを多用します。

IDは表示名ではありません。

- Track名が同じでも別Trackになり得る
- Contributor名が同じでも別人として保持できる
- MIDI Bus名が同じでも別Busになり得る

Reader/Writerは、名前の一致だけでIDを統合してはいけません。

4.0で参照対象に加わる重要ID：

- `midiBus`
- `midiEvent`
- `lyricVoice`

`targetNoteId`、`busId`、`trackId`、`documentId`等は実在対象へ解決する必要があります。

---

## 6. Song metadata

`song.title`は曲の基本表示名です。

その他のmetadataは「分からない場合は省略」が原則です。

推測して、

- C major
- 120 BPM
- Pop
- English

などを書き足してはいけません。

主要項目：

- `title`
- `subtitle`
- `description`
- `localizedTitles`
- `alternateTitles`
- `credits`
- `genres`
- `key`
- `scale`
- `languages`
- `primaryLyricDocumentId`
- `moods`
- `rights`
- `primaryMelodyTrackId`
- `melodyTrackIds`

### 6.1 Primary Melodyと複数Melody

`primaryMelodyTrackId`はPrimary Melodyの正本です。

4.0の`melodyTrackIds`は、曲に含まれる複数のMelody-bearing Track集合を明示します。

例：

```json
{
  "primaryMelodyTrackId": "lead-vocal",
  "melodyTrackIds": [
    "lead-vocal",
    "counter-melody",
    "harmony-high"
  ]
}
```

配列の先頭をPrimaryと自動推測してはいけません。

---

## 7. Genre / Mood / Language

GenreとMoodはopen-endedです。

固定の「OSTF公式ジャンル表」へ強制変換しません。

GenreやMoodの配列順は著者の表示順として保持します。

Language tagは著者情報を保持し、未知tagを勝手に別tagへ修正しません。

---

## 8. Credits / Contributors / Rights

`contributors`は人物・組織等のidentity recordです。

`song.credits`はContributorと役割の関係です。

役割例：

- composer
- lyricist
- arranger
- performer
- data-editor
- software-developer

同名を理由にContributorを統合してはいけません。

`song.rights`は権利表示を保持しますが、OSTFへの記載自体が権利認証になるわけではありません。

---

## 9. Timeline

`timeline`は曲全体の時間構造です。

主な要素：

- `sections`
- `tempoChanges`
- `meterChanges`
- `keyChanges`
- `duration`
- `barOrigin`

### 9.1 Section

SectionはIntro / Verse / Chorus等の曲構成を時間範囲で表します。

Golden Demo Setでは、全12曲でSectionが曲頭から曲末まで隙間なく構成されることを監査しています。

名前だけから意味を決めず、必要なら`role`を使います。

### 9.2 Tempo

TempoはBPMとして保存します。

Tempo Changeが複数ある場合、時系列で解釈します。

### 9.3 Meter

Meterはnumerator / denominatorです。

変拍子では`beatGroups`を使用できます。

例：7/8を2+2+3として扱う場合、

```json
{
  "numerator": 7,
  "denominator": 8,
  "beatGroups": [2, 2, 3]
}
```

### 9.4 barOrigin

弱起等で小節起点が0 tickと一致しない場合に利用します。

---

## 10. Key / Key Change / Scale

OSTFではKeyとScale Contextを分離します。

- `song.key` — 初期／全体Key
- `timeline.keyChanges` — 時間付きKey Change
- `song.scale` — 全体Scale definition
- `harmony.scales` — 時間付き局所Scale Context

Key labelだけで音楽的な値を推測しない実装が安全です。

移調時は、

- Chord
- musical Note
- Key
- Scale context
- Lyric text以外の関連pitch情報

を一貫して扱い、`fixedTrigger`は移調しません。

---

## 11. Harmony

`harmony`は、

- `chords`
- `scales`

を持ちます。

### 11.1 Chord

Chordはlabelを持ち、必要なら構造化された、

- root
- intervals
- bass
- voicing

を持てます。

LabelだけのChordも有効です。

### 11.2 Voicing

Chord `voicing`は和声スナップショットです。

Track内のConcrete Noteを自動生成する命令ではありません。

Chord Voicingと演奏Noteを二重正本にしないでください。

### 11.3 Golden Demo

Golden DemoではChord timelineのgap/overlapが0であることを監査しています。

---

## 12. Track

Trackは演奏パートの上位単位です。

代表項目：

- `id`
- `name`
- `role`
- `instrument`
- `trackType`
- `enabled`
- `clips`
- `midi`（4.0）

Track名だけで役割を推測しないでください。

---

## 13. Clip

ClipはTrack内の時間配置単位です。

代表項目：

- `id`
- `name`
- `start`
- `duration`
- `enabled`
- `notes`
- `midiEvents`（4.0）

Clipがdisabledなら、そのClipのNoteだけでなくMIDI Eventもscheduleしないのが4.0の重要な規則です。

---

## 14. Note

NoteはOSTFのConcrete Performance Noteです。

Note On / Note Offを別のMIDI Eventへ二重保存しません。

Noteは、

- `pitch`
- または` soundSlotId`

のどちらか一方を演奏対象として持ちます。

代表項目：

- `id`
- `start`
- `duration`
- `pitch`
- `soundSlotId`
- `velocity`
- `releaseVelocity`
- `enabled`
- `probability`
- `velocityDeviation`
- `pitchRole`
- `channel`（4.0）

### 14.1 velocity

Note On Velocityです。

省略は「未指定」であり、Writerが既定値を勝手に書き戻してはいけません。

### 14.2 releaseVelocity

Note Off / Release Velocityです。

### 14.3 enabled

falseならNoteを保持したまま発音抑制します。

### 14.4 probability

0.0–1.0の再生確率です。

乱数列そのものは標準化しません。

### 14.5 velocityDeviation

Velocityの変動幅です。

`velocity`が存在する場合だけ意味を持ちます。

### 14.6 pitchRole

- `musical`
- `fixedTrigger`

`fixedTrigger`はkeyswitch等の絶対MIDI Keyで、移調・Harmony分析対象にしません。

### 14.7 channel

4.0でConcrete Noteへ任意のMIDI Channelを追加しました。

---

## 15. Sound Slot / Drum Kit

打楽器や非音高トリガーは、安定した`soundSlotId`で表現できます。

Sound SlotとDrum Kitは、

- MIDI key mapping
- 音源識別
- percussion role

等の情報を曲内の安定した意味として扱うために使用します。

同じMIDI keyに複数Slotが存在しても、Slot IDが異なれば別の意味として保持できます。

---

## 16. Lyrics基本モデル

Lyricsは次の階層を持ちます。

```text
Lyric Document
  └─ Phrase
      └─ Unit
          └─ Alignment → Note
```

主な構造：

- `lyrics.documents`
- `lyrics.phrases`
- `lyrics.units`
- `lyrics.alignments`
- `lyrics.voices`（4.0）

Lyric textをNoteへ直接埋め込む方式にはしません。

---

## 17. Lyric Document

Lyric Documentは一つの歌詞本文系列です。

LeadとHarmonyが同じ歌詞を歌う場合、同じDocumentを共有できます。

Call & Responseで別の歌詞を歌う場合、別Documentを使用できます。

---

## 18. Lyric Alignment

AlignmentはLyric UnitとNoteを対応付けます。

4.0では任意`voiceId`を追加できます。

これにより、

- 同じLyric Unit
- 別Voice
- 別Note

を正しく表現できます。

Golden Demoの歌ものでは、各Voiceが歌う全NoteをVoice-specific Alignmentで一度だけ参照することを監査しています。

---

## 19. Lyric Voice

4.0の`lyrics.voices`は、Vocal VoiceとTrack / Lyric Documentの関係を明示します。

例：

```json
{
  "id": "voice-high",
  "name": "High Harmony",
  "role": "harmony",
  "trackId": "vocal-high",
  "documentId": "lyrics-main"
}
```

推奨role例：

- lead
- harmony
- backing
- unison
- double
- counter
- adlib
- response

roleはopen stringであり、未知roleをReaderが拒否してはいけません。

---

## 20. Resources

`resources`は外部素材参照です。

kind例：

- audio
- midi
- text
- other

Resource URIを読み込んだだけで自動ダウンロード・実行してはいけません。

Resourceが取得不能でも、OSTF文書内のNote/Chordが不正になるわけではありません。

---

## 21. Legacy extensions

OSTF 4.0でも`extensions`は互換性やthird-party dataの保持に使用できます。

ただし、4.0 Coreとして標準化済みの情報を新たにextensionsだけへ保存してはいけません。

Coreが現在の正本です。

未知Extensionを理解できないWriterは、可能な限りlosslessに保持します。

---

## 22. Dependencies / status

Relative Phrase、Generation、Presentation、Phrase Library、Clip Choices、Localization等の共有編集情報はCore本体との依存状態を持ちます。

代表status：

- `current`
- `stale`
- `unverified`

`current`は「Coreと対応が一致している」という意味です。

「高品質」「最新アルゴリズム」「生成成功」を意味しません。

Coreを編集して関係が壊れたら、再計算するかstale/unverifiedへ変更します。

---

## 23. Relative Phrases

`relativePhrases`はConcrete NoteとChord/Scale Contextの相対関係を保持します。

代表概念：

- chordTone
- scaleDegree
- anchorPitch
- index
- chromaticOffset

これは現在のConcrete Noteを置き換える正本ではありません。

staleな相対情報でNoteを書き換えてはいけません。

---

## 24. Generation

`generation`は生成履歴・生成意図を保存します。

保存対象例：

- generator id/version
- inputs
- outputs
- targetRange
- intent
- seed
- protectedNoteIds

Promptを保存しても、それを自動外部送信する命令ではありません。

Generatorが違えばseedが同じでも同じ結果を保証しません。

---

## 25. Presentation

`presentation`は表示・役割のportableな補助情報です。

例：

- roleDefinitions
- sectionRoleAssignments
- appearance
- roleColors
- objectColors

色や名前から演奏意味を推測しません。

Presentationは音楽値の正本ではありません。

---

## 26. Phrase Library

`phraseLibrary`は再利用可能なPhrase TemplateとConcrete Clip/Noteの対応を保存します。

重要な分離：

- Template Source Note — 原型
- Concrete Track Note — 実際に鳴るNote

Source Note自体は発音しません。

4.0の`channel`はPhrase Source Noteへ追加しません。

MPE Channelは具体的なPerformance配置であり、Phrase原型ではないためです。

Phrase refresh時は存続Concrete Noteのchannelを保持します。

---

## 27. Clip Choices

`clipChoices`は複数候補Clipと選択状態を保持します。

読み込んだだけで候補を再抽選してはいけません。

4.0ではClip enabledがMIDI Eventにも適用されるため、無効候補からController/Programだけが漏れないようにします。

---

## 28. Localization

原文は通常フィールドが正本です。

翻訳はLocalization layerで保持します。

4.0では次も翻訳対象にできます。

- MIDI Bus name
- Lyric Voice name

翻訳で、

- ID
- role
- mode
- chord value
- musical pitch

等の意味を変えてはいけません。

---

## 29. Document identity

`document`は文書系列のidentityとrevisionを扱います。

代表概念：

- document id
- revisionId
- parents
- createdBy
- modifiedBy

同じ作業文書系列と、独立コピーを区別できます。

ID宣言だけで暗号学的真正性を保証するものではありません。

---

## 30. Provenance

`provenance`は履歴・由来の自己申告記録です。

action例：

- create
- import
- conversion
- edit
- transpose
- merge
- generation
- other

Provenanceは現在の音楽値より優先する正本ではありません。

---

# Part II — OSTF 4.0 MIDI Performance

## 31. MIDI Performance Layerの基本原則

4.0では、ControllerやProgram等をClipの`midiEvents[]`へ保存します。

対応Event：

- `controlChange`
- `channelMode`
- `programChange`
- `pitchBend`
- `channelPressure`
- `polyPressure`

Note On / Note Offは`notes[]`が正本です。

---

## 32. MIDI Channel

OSTF内のChannel値は **1–16** です。

Wire status nibbleの0–15ではありません。

### 32.1 singleChannel

Track全体の既定Channelを持ちます。

Note/Eventは必要に応じてTrack Channelを継承できます。

### 32.2 multiChannel

同一Track内で複数Channelを利用します。

Channel identityが必要なNote/Eventには具体Channelを持たせます。

### 32.3 mpe

MPE Zoneに従ってMember ChannelをNote単位に割り当てます。

---

## 33. Logical MIDI Bus

Root `midi.buses[]`で論理出力空間を定義します。

例：

```json
{
  "midi": {
    "buses": [
      { "id": "bus-main", "name": "Main MIDI" },
      { "id": "bus-synth", "name": "External Synth" }
    ]
  }
}
```

各Busは独立した16 Channel namespaceを持ちます。

したがって、

```text
Bus A / Ch 1
Bus B / Ch 1
```

は別の演奏先です。

Physical Port名は保存しません。

---

## 34. Track MIDI設定

Trackの`midi`は、

- `mode`
- `busId`
- `channel`
- `mpe`

を組み合わせます。

`busId`がないことは、文書が不正という意味ではありません。Host既定空間等の扱いは意味規則に従います。

---

## 35. Control Change

```json
{
  "id": "cc-1",
  "start": 960,
  "type": "controlChange",
  "controller": 74,
  "value": 96
}
```

範囲：

- controller: 0–119
- value: 0–127

120–127は`channelMode`です。

---

## 36. Bank Select

Bank SelectのOSTF独自bank fieldは作りません。

MIDI標準と同様、

1. CC 0 — Bank Select MSB
2. CC 32 — Bank Select LSB
3. Program Change

として保持します。

この順序が重要な場合は配列順や`order`を使用します。

---

## 37. Program Change

```json
{
  "type": "programChange",
  "program": 40
}
```

保存値は0–127です。

UIがProgram 1–128と表示しても、保存時にwire valueへ戻します。

---

## 38. Pitch Bend

Pitch Bendは14-bitです。

- min = 0
- center = 8192
- max = 16383

```json
{
  "type": "pitchBend",
  "value14": 8192
}
```

Pitch Bend Sensitivityは別状態です。

必要ならRPNをraw CC sequenceとして保存します。

---

## 39. Channel Pressure

Channel全体のAftertouchです。

```json
{
  "type": "channelPressure",
  "value": 80
}
```

valueは0–127です。

---

## 40. Polyphonic Key Pressure

特定PitchへのPoly Aftertouchです。

```json
{
  "type": "polyPressure",
  "pitch": 64,
  "value": 90
}
```

必要なら`targetNoteId`も付けます。

---

## 41. Channel Mode

CC 120–127相当は`channelMode`へ分離します。

これにより普通のcontinuous controllerと、

- All Sound Off
- Reset All Controllers
- Local Control
- All Notes Off
- Omni
- Mono/Poly

等を構造上区別できます。

---

## 42. RPN / NRPN

4.0.0では独立`rpn` / `nrpn` Event型を作りません。

通常のCC列としてlosslessに保持します。

Feature Fixtureに、

- Pitch Bend Sensitivity RPN
- NRPN raw sequence

の例があります。

---

## 43. targetNoteId

4.0では次のEventが任意`targetNoteId`を持てます。

- Control Change
- Pitch Bend
- Channel Pressure
- Poly Pressure

これは特にMPE/per-note expressionの意味関係を明示するために使います。

targeted Eventは対象Noteと同じTrack/Channel文脈を満たす必要があります。

Golden DemoではtargetNoteId付きEventが対象Noteの発音時間外に出ないことを監査しています。

---

## 44. Probabilityとtargeted Event

対象Noteが`probability`によりその回に鳴らなかった場合、targetNoteId付きper-note Eventも送らないのが安全な意味規則です。

対象Noteなしでper-note Bend/CCだけ送ると、同じChannel上の別Noteへ表現が漏れる可能性があります。

---

## 45. 同一tick順序

既定概念順：

```text
Note Off     = 0
MIDI Event   = 1
Note On      = 2
```

MIDI Eventには任意`order`を付けられます。

例：

- order < 0 — Note Offより前
- order 0 — Note Off相当位置
- order 1 — 通常MIDI Event
- order 2 — Note On相当位置
- order > 2 — Note Onより後

同じstart/orderの場合は文書配列順を保持するのが安全です。

---

## 46. MPE基本

MPE専用のEvent typeは作りません。

MPEは、

- Note Channel
- Pitch Bend
- CC
- Channel Pressure
- Poly Pressure

の組み合わせです。

### 46.1 Lower Zone

Master Channel = 1

Member Channelは2から上方向へ連続します。

### 46.2 Upper Zone

Master Channel = 16

Member Channelは15から下方向へ連続します。

`memberChannelCount`は1–15です。

---

## 47. MPE Note配置

MPEでは同時発音Noteを別Member Channelへ割り当てます。

同一Member Channel上で同時Noteを衝突させると、Channel単位のPitch Bend等をNoteごとに分離できません。

Golden MPE Demoでは同一Member Channel同時衝突0を監査しています。

---

## 48. MPE per-note expression

代表的なper-note表現：

- Pitch Bend
- CC74
- Channel Pressure
- Poly Pressure

必要に応じて`targetNoteId`を使用します。

Golden Demo 07はLower Zone、Demo 08はUpper Zone、Demo 12は同一Logical Bus上のLower+Upper非重複共存を示します。

---

## 49. MPEとPhrase Library

Phrase Source NoteにMPE Channelを埋め込みません。

MPE ChannelはConcrete Performance時に決定されます。

Phraseをmaterializeした後で、TrackのMPE Zoneに従って有効Member Channelを割り当てます。

---

# Part III — 複数メロディー／複数ボーカル

## 50. Multi-Melody

`song.melodyTrackIds`は複数のMelody-bearing Trackを明示します。

用途：

- lead melody
- counter melody
- harmony melody
- obbligato
- instrumental secondary melody

Trackのrole/nameから自動推測して保存してはいけません。

---

## 51. Multi-Voice

`lyrics.voices`は「誰がどのTrackでどのLyric Documentを歌うか」を表します。

同一歌詞をLead/High/Lowが共有しても、Lyric text自体を3重に複製する必要はありません。

---

## 52. Shared Lyric Document

同じ歌詞を複数Voiceが歌う場合：

```text
Lyric Document A
├─ Lead Voice
├─ High Harmony Voice
└─ Low Harmony Voice
```

各VoiceのNoteへの対応は`alignment.voiceId`で分けます。

Golden Demo 09がこの例です。

---

## 53. Separate Lyric Documents

Call & Response等で別本文なら、

```text
Lead Voice     → Lyric Document A
Response Voice → Lyric Document B
```

とします。

Golden Demo 10がこの例です。

---

# Part IV — Reader / Writer / Editor

## 54. Reader実装順序

推奨手順：

1. UTF-8 JSON parse
2. `format`確認
3. `version`確認
4. Schema validation
5. Core ID collection
6. Reference validation
7. Timeline構築
8. Harmony構築
9. Track/Clip/Note構築
10. Lyrics構築
11. Shared editing layers構築
12. 4.0 MIDI Bus/Channel/Event構築
13. MPE semantic validation
14. Unsupported optional featureの扱い決定
15. Playback / UIへ渡す

読込だけで、

- regeneration
- candidate selection
- external download
- physical MIDI binding

を実行してはいけません。

---

## 55. Writer実装原則

Writerは「分からない情報を消さない」が重要です。

特に4.0ファイルを部分対応アプリで編集する場合、

- 未対応MIDI Event
- MPE
- Lyric Voice
- Generation
- Localization
- Provenance
- Extension

等を保持できないなら、原本への破壊的上書きを拒否します。

---

## 56. Editorの安全な削除

Noteを削除すると、

- Lyric Alignment
- targetNoteId
- Relative Phrase assignment
- Phrase Library mapping
- Generation protectedNoteIds
- Presentation objectColor
- Provenanceそのものではなく現在参照

等への影響を調べます。

参照を壊したまま保存してはいけません。

---

## 57. Track移動・Section編集

Track/Clip/Sectionを移動する場合、関連するTempo/Key/Meter/Chordが自動追従するとは限りません。

UIは、

- 移動対象
- 取り残す対象
- 新しい境界
- staleになる共有編集情報

を明示するのが安全です。

---

## 58. Merge

Mergeは「同じIDだから同じ物」と盲信しません。

特に独立文書間ではID衝突があり得ます。

担当TrackをMergeする場合、4.0では次も担当演奏データとして比較できます。

- Note.channel
- Clip.midiEvents
- Track.midi

ただしroot `midi.buses`は共有参照情報です。

同名Busへ黙って付け替えてはいけません。

---

## 59. Atomic save

MergeやMigrationは一部だけ書き換えた状態で失敗ファイルを残さないようにします。

すべての参照・制約・保持可否を確認後、一括保存します。

---

# Part V — 3.x互換

## 60. 3.5 → 4.0

3.5文書は、明示Migrationなしに自動で4.0へ書き換えないでください。

最小Migrationでは既存音楽データを変更する必要はありません。

4.0情報を推測追加しない例：

- MIDI Bus
- Channel
- MPE
- Lyric Voice
- Counter Melody

既存`primaryMelodyTrackId`から`melodyTrackIds:[primary]`を提案することはできますが、自動追加必須ではありません。

---

## 61. 4.0 → 3.5

Downgradeでは次を失う可能性があります。

- root midi
- Track MIDI mode/bus/channel
- Note channel
- MIDI Events
- MPE
- melodyTrackIds
- lyrics.voices
- alignment.voiceId

保存前にloss reportを提示します。

---

## 62. Compatibility Playback

4.0完全対応前のOSTF Playerでも、

1. `version: "4.0.0"`を既知Versionとして受理
2. 3.xから継承したSection/Chord/Track/Clip/Note/Lyricsを再生
3. 未対応4.0 optional performance dataを再生時だけskip

というCompatibility Playbackを実装できます。

ただし、skipした4.0 dataを保存時に削除してはいけません。

Golden Demo 12曲は4.0固有層を除いたCompatibility ViewがOSTF 3.5 Schemaへ12/12適合することを静的確認しています。

---

# Part VI — Validation

## 63. 構造Validation

構造Validationの正本はJSON Schemaです。

検証対象：

- required
- type
- enum
- min/max
- pattern
- oneOf
- additionalProperties
- dependentRequired

等。

---

## 64. Semantic Validation

Schemaだけでは検証できない意味規則があります。

例：

- ID参照先の存在
- `primaryMelodyTrackId`のTrack存在
- `melodyTrackIds`参照
- busId参照
- targetNoteId参照
- MPE Channel範囲
- Lower/Upper Zone重複
- Lyric Voice Track/Document関係
- Voice AlignmentのDocument/Track整合
- Poly Pressure pitchとtarget Note pitchの整合
- Relative Phrase式
- Phrase Library materialization整合
- current dependency整合

---

## 65. Golden Demo品質Validation

公式12 Demoでは次を追加監査しています。

- 24小節以上
- 複数Section
- Section gap/overlap 0
- Chord timeline gap/overlap 0
- Primary Melodyの十分な音高・リズム変化
- 不要なNote overlapなし
- targetNoteId付きEventが対象Note発音中
- MPE同一Member Channel同時衝突なし
- Vocal全NoteがVoice-specific Alignmentで一度だけ参照
- Lyric Unit未参照なし
- 3.5 Compatibility View適合
- 12曲間の完全Arrangement重複なし

詳細は`DEMO_QUALITY_AUDIT.ja.md`を参照してください。

---

# Part VII — 公開Reference Set

## 66. Canonical Examples

`examples/`直下の5ファイルは短い実装例です。

1. `01-midi-performance.ostf`
2. `02-mpe.ostf`
3. `03-multi-voice.ostf`
4. `04-multi-bus.ostf`
5. `05-multi-channel-order.ostf`

最小の理解にはここから読むのが適しています。

---

## 67. Golden Demo Songs

`examples/demo-songs/`には12曲の長期公式Golden Musical Demoがあります。

長さは24〜40小節です。

1. Neon Crosswalk — Pop / MIDI Program・CC・Drums
2. Rust and Rain — Alternative Rock / simultaneous multiChannel
3. Last Platform — 3/4 Ballad / full lyric alignment
4. Blue Lantern — 32-bar AABA Jazz
5. Above the Vector — Trance / multiple Logical Buses
6. Velvet Corners — Neo Soul / multiple melody tracks
7. Glass Garden — MPE Lower
8. Orbiting Light — MPE Upper
9. Two Sides of Morning — Lead/High/Low vocals
10. Answer Back — Call & Response / separate lyric docs
11. Shifting Ground — mixed meter / tempo+key changes
12. Signal Bloom — 40-bar Complete Showcase

Demo Songは境界値テストの羅列ではなく、音楽として統合した例です。

---

## 68. Feature Fixtures

`examples/feature-fixtures/`には41個の技術Fixtureがあります。

主なCoverage：

- Channel 1 / 16
- CC controller 0 / 119
- CC value 0 / 127
- Channel Mode 120–127
- Program 0 / 127
- Pitch Bend 0 / 8192 / 16383
- Channel/Poly Pressure 0 / 127
- MPE Lower/Upper
- MPE member count 1 / 15
- Bank Select
- RPN / NRPN
- targetNoteId
- same-tick order
- negative clip-local event
- multiple buses same channel
- singleChannel inheritance
- multiChannel
- shared/separate lyric documents
- multiple melody tracks
- Probability + targeted expression
- Graceful Degradation

---

## 69. DemoとFixtureの役割分離

Golden Demo Songへ無理にProgram 0/127等の境界値を詰め込むと、曲として不自然になります。

そのためReference Setは、

```text
Musical Demo Songs
+ Technical Feature Fixtures
+ Canonical Minimal Examples
```

の3層です。

これらを合わせてOSTF 4.0の公開Reference Example Setとします。

---

# Part VIII — Anti-patterns

## 70. Note On/OffをmidiEventsへ重複保存しない

Noteの正本は`notes[]`です。

---

## 71. Physical PortをLogical Busへ書かない

`"Scarlett MIDI Out"`のような実機名をPortable Coreの正本にしません。

---

## 72. Program番号をUI値のまま保存しない

UI 1–128でも保存値は0–127です。

---

## 73. Pitch Bendを7-bitへ丸めない

14-bit 0–16383を保持します。

---

## 74. MPE専用独自Event型を作らない

通常のChannel Voice EventをMember Channelで表します。

---

## 75. Harmonyを固定半音移動だけで作らない

Format上はどんなPitchも保存できますが、公式Demoでは音楽的に意味のあるHarmonyを使用します。

---

## 76. 未対応4.0情報を保存時に捨てない

Compatibility Playbackで無視することと、Writerが削除することは別です。

---

## 77. 名前からIDを推測しない

Track名、Bus名、Contributor名、Role名の一致で自動マージしません。

---

## 78. unknown future versionを4.0として読むと決めつけない

4.1/5.xには4.0とは異なる意味変更があり得ます。

---

# Part IX — Conformance

## 79. Reader適合

Readerは少なくとも、

- Versionを識別
- Core構造を安全にParse
- 未対応機能を明示
- 対応機能の意味規則を守る

必要があります。

---

## 80. Writer適合

Writerは、

- Schema適合
- Semantic reference整合
- 未対応情報の保持
- lossの明示
- 推測で正本を作らない

ことが重要です。

---

## 81. Full Performance Player

完全4.0 Performance Playerは、

- Logical Bus
- Channel modes
- MIDI Events
- same-tick order
- MPE Lower/Upper
- per-note expression
- Multi-Voice relationship

まで解釈します。

---

## 82. Format releaseとImplementation releaseの分離

OSTF 4.0仕様のReleaseと、OSTF Playerの4.0完全対応Releaseは別です。

Formatが先に公開され、PlayerがCompatibility Playbackから段階的に完全対応しても問題ありません。

---

# Part X — 実装レシピ

## 83. 単純な1ch Piano

- Track `mode=singleChannel`
- `channel=1`
- Note channel省略可
- CC64等をmidiEventsへ保存

---

## 84. 16ch Multi-timbral Track

- `mode=multiChannel`
- 各Note/EventにChannel
- ChannelごとのProgram/Controllerを明示

---

## 85. External Synth群

- Logical Busを複数作る
- TrackからbusId参照
- Physical device mappingはHost設定

---

## 86. MPE Synth

- Track `mode=mpe`
- zone + memberChannelCount
- 同時Noteを別Member Channel
- targeted Bend/CC/Pressure
- 同一Member Channel衝突を避ける

---

## 87. Lead + Harmony

- melodyTrackIdsへ複数Track
- lyrics.voicesを作成
- 同じ歌詞ならDocument共有
- AlignmentでvoiceIdを分ける

---

## 88. Call & Response

- Lead/Response別Track
- 必要なら別Lyric Document
- VoiceごとにAlignment

---

## 89. DAWからImport

Import時は、

- Note pitch/velocity/duration
- Channel
- CC
- Program
- Bend
- Pressure
- ordering

の明示情報を保持します。

DAWの物理Port名をLogical Busへ自動変換しません。

---

## 90. SMFへExport

OSTFからSMFへ出す場合、

- Note
- Channel
- Program
- Bank
- CC
- Bend
- Pressure
- same-tick order

をMIDIへ変換できます。

OSTF固有のSection/Chord/Lyrics/Multi-Voice等はSMFだけでは完全保持できないため、lossを明示します。

---

# Part XI — 全構造索引

## 91. Root object

Rootの全フィールドは`PARAMETER_REFERENCE.ja.md`の`Root`を参照します。

---

## 92. Schema object catalog

Parameter ReferenceはSchemaの **全91 `$defs`** を1対1で見出し化しています。

主なグループ：

### 基本型
`id`, `nonnegative`, `positive`, `signed`, `pitch`, `ref`, `range`, `spelledPitchClass`

### Song / Timeline / Harmony
`song`, `section`, `tempo`, `meter`, `timeline`, `chord`, `scale`, `harmony`, `key`, `keyChange`, `scaleDefinition`, `scaleTarget`

### Performance
`instrument`, `note`, `clip`, `track`, `soundSlot`, `drumKit`, `midiMapping`, `soundSource`, `voicing`

### Lyrics
`lyricDocument`, `lyricPhrase`, `lyricUnit`, `lyricAlignment`, `lyrics`, `lyricVoice`

### Resource / identity
`sha256`, `resource`, `contributor`, `credit`, `alternateTitle`, `rightsStatement`, `documentInfo`, `revisionRef`, `software`

### Shared editing
`dependencies`, `relativeAssignment`, `relativePhrases`, `generator`, `generationInput`, `generationProcess`, `generation`, `generationIntent`, `grooveIntent`

### Presentation
`color`, `roleDefinition`, `roleColorMap`, `customRoleColorMap`, `objectColor`, `presentation`

### Phrase / choices
`phraseTemplate`, `phraseNoteMapping`, `phraseInstance`, `corePhraseSourceNote`, `phraseLibrary`, `clipCandidate`, `clipChoiceGroup`, `clipChoices`

### Localization
`localizedNames`, `languageMap32`, `localizationTarget`, `localizationEntry`, `localization`

### Provenance
`provenanceSource`, `provenanceRecord`, `provenance`

### OSTF 4 MIDI
`midiChannel`, `midiBus`, `midi`, `mpeSettings`, `trackMidi`, `controlChangeEvent`, `channelModeEvent`, `programChangeEvent`, `pitchBendEvent`, `channelPressureEvent`, `polyPressureEvent`, `midiEvent`

### Legacy compatibility
`legacy33`

各Objectの全Field・型・required・rangeは`PARAMETER_REFERENCE.ja.md`を参照してください。

---

# Part XII — Release checklist

## 93. GitHub公開前チェック

- Schema JSONがparse可能
- 全`.ostf`がJSON parse可能
- 全公開`.ostf`が4.0 Schemaへ適合
- Semantic reference check PASS
- Golden Demo strict audit PASS
- 3.5 Compatibility View PASS
- Manual/Reference/SchemaのVersion一致
- READMEのDemo長・数が最新
- 100ファイル以内
- SHA256SUMS更新
- ZIP checksum生成

---

## 94. 本Release Packageの構成

- 12 Golden Musical Demo Songs
- 41 Feature Fixtures
- 5 Canonical `.ostf` Examples
- 58 total `.ostf`
- JSON Schema
- 全網羅仕様書
- 全網羅マニュアル
- 全網羅パラメーターリファレンス
- Migration / Compatibility / Scope / Delta
- Demo Guide / Coverage Matrix / Quality Audit
- Example Index / Reference Coverage Audit
- Release notes / readiness / checksums

---

## 95. 参照順序

実装者が初めて読む場合：

1. `README.md`
2. `docs/COMPLETE_MANUAL.ja.md`
3. `examples/01-midi-performance.ostf`
4. `docs/FORMAT_SPECIFICATION.ja.md`
5. `docs/PARAMETER_REFERENCE.ja.md`
6. `docs/DEMO_SONG_GUIDE.ja.md`
7. `docs/DEMO_COVERAGE_MATRIX.ja.md`
8. `docs/EXAMPLE_INDEX.ja.md`
9. `schema/ostf-4.0.0.schema.json`

Validator実装者はSchemaとParameter Referenceから読むのが適しています。

---

## 96. 最終原則

OSTF 4.0の設計で最も重要なのは、正本を混同しないことです。

```text
Song structure       → Timeline / Harmony
Concrete performance → Tracks / Clips / Notes
MIDI expression      → MIDI Events
Portable routing     → Logical MIDI Bus / Channel
Lyrics text          → Lyric Documents
Vocal relationships  → Lyric Voices / Alignments
Editing relations    → Relative/Generation/Phrase/Choices
History              → Document / Provenance
Local device routing → Host settings (OSTF Core外)
```

この境界を守ることで、OSTF 4.0はDAWや特定実装に依存せず、OSTF 3.xの資産を維持しながら、MIDI Performanceと複数Melody/Voiceへ拡張できます。
