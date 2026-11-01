# OSTF 4.0 — Golden Demo Song Guide

> English: [`DEMO_SONG_GUIDE.md`](DEMO_SONG_GUIDE.md)

## 1. 位置付け

`examples/demo-songs/` はOSTF 4.0の**長期公式Golden Musical Demo Set**です。

技術的な境界値や特殊ケースは `examples/feature-fixtures/` に分離し、Demo Song自体は「曲として理解できること」を優先します。

- `demo-songs/` — 完成楽曲としての統合例
- `feature-fixtures/` — 値域、境界、順序、特殊意味規則
- `examples/01...05` — 最小Canonical Example

この3層を合わせてOSTF 4.0のReference Example Setとします。

## 2. Golden Demo Set

| # | Demo | 長さ | 主目的 |
|---:|---|---:|---|
| 01 | `Neon Crosswalk` | 32 bars | Pop、Bank Select、Program Change、CC、Channel Mode、Drums |
| 02 | `Rust and Rain` | 32 bars | Alternative Rock、simultaneous multiChannel guitar、channel-specific Program/CC/Bend |
| 03 | `Last Platform` | 24 bars / 3/4 | Ballad、full lyric alignment、tempo relaxation、Pitch Bend、Channel/Poly Pressure |
| 04 | `Blue Lantern` | 32 bars AABA | Jazz Swing、extended voicing、half-bar changes、walking bass、comping |
| 05 | `Above the Vector` | 32 bars | Trance、multiple Logical MIDI Buses、dense CC automation、Pitch Bend |
| 06 | `Velvet Corners` | 32 bars | Neo Soul、multiple melody tracks、counter melody、7th/9th harmony |
| 07 | `Glass Garden` | 24 bars | MPE Lower Zone、simultaneous per-note expression |
| 08 | `Orbiting Light` | 24 bars | MPE Upper Zone、Polyphonic Key Pressure |
| 09 | `Two Sides of Morning` | 36 bars | Lead + High + Low、shared lyric document、complete voice alignment |
| 10 | `Answer Back` | 32 bars | Call & Response、separate lyric documents、counter melody、funk rhythm section |
| 11 | `Shifting Ground` | 24 bars mixed meter | 7/8→4/4→5/4 ×2、beatGroups、tempo/key changes、fixedTrigger |
| 12 | `Signal Bloom` | 40 bars | Complete Showcase、multiple buses、single/multi/MPE lower+upper、multi-voice lyrics、3.x inherited layers |

## 3. Compatibility Playback

OSTF Playerが4.0 MIDI Performanceをまだ実装していない場合でも、`version: "4.0.0"`を既知形式として受理し、未対応の4.0演奏表現のみを再生時に無視できます。

無視対象になり得る4.0情報：

- root `midi`
- `tracks[].midi`
- `notes[].channel`
- `clips[].midiEvents`
- `song.melodyTrackIds`
- `lyrics.voices`
- `lyrics.alignments[].voiceId`

ただし未対応情報を保持できないWriterは4.0原本へ破壊的上書きをしてはいけません。

12曲すべてについて4.0固有情報を除いた3.5 Compatibility Viewを作成し、OSTF 3.5 Schemaへ **12 / 12 PASS** しています。

## 4. Demo SongとFixtureを混同しない

Program 0/127、Pitch Bend 0/8192/16383、CC 0/119、Channel Mode 120〜127、MPE member count 1/15、RPN/NRPN、same-tick order等は、音楽デモへ不自然に押し込まずFeature Fixtureで網羅します。

Demo Songは音楽的な統合例、Fixtureは仕様の精密な技術例です。

## 5. 品質監査

詳細は `docs/DEMO_QUALITY_AUDIT.ja.md` を参照してください。
