# OSTF 4.0 — Player互換再生ポリシー

## 1. 目的

OSTF 4.0ではMIDI Channel、Control Change、Program Change、Pitch Bend、Aftertouch、MPE、Logical MIDI Bus、複数メロディー宣言、複数Lyric Voiceを追加した。一方、OSTF 3.x世代のPlayerがこれらすべてを直ちに発音できるとは限らない。

OSTF 4.0は、**既知の4.0文書を部分再生するCompatibility Playback**を認める。ただし、これは未来の未知バージョンを無条件で読み飛ばす規則ではない。

## 2. OSTF Playerの推奨動作

Playerは3.0.0～3.5.0を従来どおり解釈する。

`version="4.0.0"`を読み込んだ場合、4.0を既知バージョンとして認識し、実装済みの3.x互換部分を通常再生してよい。未実装の4.0 Core機能は、**再生時に限り**無視できる。

現行Playerで無視してよい代表例：

- ルート`midi.buses`
- `tracks[].midi`
- `notes[].channel`
- `clips[].midiEvents`
- `song.melodyTrackIds`
- `lyrics.voices`
- `lyrics.alignments[].voiceId`

ただし、既存のNote、Chord、Tempo、Meter、Section、Key、Scale、Lyrics本文、Track、Clip等は通常どおり処理する。

## 3. UI通知

4.0文書に未対応機能が存在する場合、読込を失敗させる代わりに一度だけ非ブロッキング通知を出すことを推奨する。

推奨英語表示：

`OSTF 4.0 loaded. Some MIDI performance features are not supported by this Player and will be ignored during playback.`

完全対応している機能まで「unsupported」と表示しない。実装能力に基づいて判定する。

## 4. 保存は別問題

Compatibility Playbackで無視した情報を、保存時に削除してはならない。

4.0文書をPlayerが編集・保存する場合は次のどちらかを満たす。

1. 未対応4.0フィールドを意味変更せずround-trip保持する。
2. それができない場合、元4.0ファイルへの上書きを禁止する。

3.5へ明示的にDowngradeする機能を提供する場合は、失われる4.0情報を事前に通知する。

## 5. 未知の未来版

`4.1.0`、`5.0.0`等、Playerが仕様を知らないバージョンを4.0として推測解釈してはならない。

未知版は未対応として扱うか、原文保持だけを行う限定モードにする。Compatibility Playbackは**仕様を知っている4.0.0の、未実装機能に対するフォールバック**である。

## 6. Demo Songの設計

OSTF 4.0のDemo Songは二層で作る。

### A. Musical Demo Songs

- 3.xまでのNote/Chord/Section/Lyricsを使って、曲として成立させる。
- その上に4.0 MIDI Performance情報を追加する。
- 4.0未対応Playerでも、演奏表現は簡略化されるが曲自体は再生できる。

### B. Feature Reference Examples

- CC、Program、Pitch Bend、MPE等を個別に短く示す。
- 音響的な差を現行Playerで証明することは目的にしない。
- Schema・Writer・Reader実装者がCanonicalな記述方法を参照するための資料とする。

## 7. 互換再生で変えてはいけないもの

未対応MIDI EventをNoteへ焼き込んだり、Pitch BendをNote pitchへ変換したり、MPEを単一Channelへ書き換えたりしない。

Compatibility Playbackは**無視して再生するだけ**であり、原データの意味を別表現へ自動変換する処理ではない。
