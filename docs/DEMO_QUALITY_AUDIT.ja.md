# OSTF 4.0 — Golden Demo Set Quality Audit

Audit date: 2026-11-01

## 1. 目的

この文書は `examples/demo-songs/` の12曲を、短期的なSchemaサンプルではなく、今後長期間参照できる **Golden Reference Demo Set** として公開できるかを監査した結果です。

動作検証はプロジェクト方針により対象外です。本監査はフォーマット、参照整合性、音楽データ構造、MIDI Performance、MPE、歌詞Alignment、3.5互換ビューの静的検証です。

## 2. Golden Demo必須条件

正式Demo Songはすべて次を満たします。

1. 他DemoのNote列を流用しただけの曲ではない。
2. 24小節以上の実用的な楽曲構造を持つ。
3. 複数Sectionを持ち、Sectionは0 tickから曲末まで隙間・重複なく連続する。
4. Harmony Chordは曲頭から曲末まで時間上の隙間・重複なく配置される。
5. Primary Melodyは十分な音高・リズム変化を持つ。MPE持続和音Demoはこのリズム条件を適用しない。
6. singleChannelのLead/Vocal/Response等の単旋律Trackには意図しないNote overlapがない。
7. `targetNoteId`付きMIDI Eventは対象Noteの発音時間内に存在する。
8. MPE Member Channelでは同一Channel上のNoteが時間的に衝突しない。
9. MPE Member Channelのper-note Expressionは`targetNoteId`で対象を明示する。
10. Lyric Voiceを持つ曲は、そのVoiceが歌唱する全NoteをVoice-specific Alignmentで一度だけ参照する。
11. Lyric Unitは少なくとも1つのAlignmentから参照される。
12. 4.0固有項目をCompatibility Playbackとして除いたViewがOSTF 3.5 Schemaにも適合する。

## 3. Demo Set

| # | File | Title | Bars | Sections | Tracks | Primary melody notes | MIDI events | Lyric voices |
|---:|---|---|---:|---:|---:|---:|---:|---:|
| 01 | `OSTF4_Demo_01_Neon_Crosswalk.ostf` | Neon Crosswalk | 32 | 5 | 4 | 132 | 10 | 0 |
| 02 | `OSTF4_Demo_02_Rust_and_Rain.ostf` | Rust and Rain | 32 | 5 | 4 | 126 | 15 | 0 |
| 03 | `OSTF4_Demo_03_Last_Platform.ostf` | Last Platform | 24 | 4 | 3 | 64 | 10 | 1 |
| 04 | `OSTF4_Demo_04_Blue_Lantern.ostf` | Blue Lantern | 32 | 4 | 4 | 192 | 4 | 0 |
| 05 | `OSTF4_Demo_05_Above_the_Vector.ostf` | Above the Vector | 32 | 5 | 4 | 80 | 26 | 0 |
| 06 | `OSTF4_Demo_06_Velvet_Corners.ostf` | Velvet Corners | 32 | 5 | 4 | 117 | 1 | 0 |
| 07 | `OSTF4_Demo_07_Glass_Garden_MPE_Lower.ostf` | Glass Garden | 24 | 3 | 1 | 96 | 386 | 0 |
| 08 | `OSTF4_Demo_08_Orbiting_Light_MPE_Upper.ostf` | Orbiting Light | 24 | 3 | 1 | 96 | 386 | 0 |
| 09 | `OSTF4_Demo_09_Two_Sides_of_Morning.ostf` | Two Sides of Morning | 36 | 6 | 6 | 115 | 0 | 3 |
| 10 | `OSTF4_Demo_10_Answer_Back_Call_and_Response.ostf` | Answer Back | 32 | 5 | 5 | 46 | 0 | 2 |
| 11 | `OSTF4_Demo_11_Shifting_Ground_Mixed_Meter.ostf` | Shifting Ground | 24 | 6 | 3 | 120 | 0 | 0 |
| 12 | `OSTF4_Demo_12_Signal_Bloom_Complete_Showcase.ostf` | Signal Bloom — Complete OSTF 4.0 Showcase | 40 | 7 | 8 | 128 | 400 | 2 |

## 4. 音楽面の再整備

旧Demo Setで問題だった「同一16小節曲の機能差し替え」は完全に廃止しました。

- Pop、Alternative Rock、3/4 Ballad、Jazz Swing、Trance、Neo Soul、MPE Lower、MPE Upper、3-part Vocal Pop、Call & Response、Mixed Meter Progressive Rock、Complete Showcaseを別作品として構成。
- Primary Melodyは曲ごとに別のMotif、Rhythm、Rangeを使用。
- Ballad、Vocal Pop、Call & Response、Complete Showcaseは旧版の1小節1長音中心の構造を廃止し、複数Note/小節、休符、Motif変形を持つ歌唱線へ変更。
- Vocal Harmonyは固定+4 semitoneではなく、Key/Scaleに沿うdiatonic 3rdを採用。
- `Two Sides of Morning` のHigh/Low HarmonyはLeadとの上下関係を保ち、3rdのVoice Leadingを維持。
- `Signal Bloom` のHarmony VocalもLeadのdiatonic 3rd上で交差しない。
- Jazz DemoはAABA 32小節、half-bar chord changes、walking bass、swing timing、compingを含む。
- Mixed Meter Demoは7/8、4/4、5/4を2サイクル持ち、beatGroups・tempo change・key changeを実曲構造内で共存させる。

## 5. MIDI / MPE監査

- Lower MPE Demo: 4 simultaneous voices, distinct member channels, targeted Pitch Bend / CC74 / Channel Pressure.
- Upper MPE Demo: 4 simultaneous voices, distinct member channels, targeted Pitch Bend / CC74 / Poly Pressure.
- Complete Showcase: 同一Logical MIDI Bus上でLower/Upper Zoneを非重複Channelとして共存。
- すべてのtargeted eventは対象Noteの発音時間内。
- MPE同一Member Channel上の同時Note衝突なし。
- multiChannel Guitarは「単音をChannel順番に回す」方式ではなく、同時LayerをChannelごとに役割分離。

## 6. Lyrics / Multi-Voice監査

- `Last Platform`: Lead Vocal全歌唱NoteをAlignmentで網羅。
- `Two Sides of Morning`: Lead / High / Lowの全歌唱NoteをVoice-specific Alignmentで網羅。同一Lyric Documentを共有。
- `Answer Back`: CallとResponseで別Lyric Documentを使用し、各Voiceの全歌唱Noteを網羅。
- `Signal Bloom`: LeadとHarmonyの全歌唱NoteをVoice-specific Alignmentで網羅。
- Alignmentに同一Voice Noteの二重参照なし。
- Lyric Unitの未参照なし。

## 7. Static validation result

- Public `.ostf`: **58 / 58 OSTF 4.0 Schema PASS**
- Core semantic reference checks: **58 / 58 PASS**
- Golden Demo strict musical/structural audit: **12 / 12 PASS**
- Golden Demo 3.5 Compatibility View: **12 / 12 PASS**
- Exact full-arrangement duplicate among 12 demos: **0**
- Section coverage gaps/overlaps: **0**
- Chord timeline gaps/overlaps: **0**
- Vocal note Alignment omissions: **0**
- Targeted MIDI Event outside target note: **0**
- MPE same-channel simultaneous collision: **0**

## 8. 判定

**Golden Demo Set: Release GO**

この判定はOSTF 4.0というファイルフォーマットの公開Demoとしての判定です。OSTF Player、Ableton Live、Max for Live、MPE hardware等での実動作は各実装側のConformance Testであり、本監査には含めません。
