# OSTF 4.0 — Core責任範囲

## 目的

OSTF 4.0は、OSTF 3.5までの「曲・構成・和声・歌詞・確定Note」に、MIDI演奏制御と複数Melody/Voice関係を追加します。

**Structured Song + Arrangement + MIDI Performance + Multi-Voice**

を4.0 Coreの境界とします。

## 4.0 Coreに含む

- OSTF 3.5 Core全体
- MIDI Channel 1～16
- Logical MIDI Bus
- Control Change 0～119
- Channel Mode 120～127
- Program Change
- Bank Select（CC0/32として）
- Pitch Bend 14-bit
- Channel Pressure / Channel Aftertouch
- Polyphonic Key Pressure / Poly Aftertouch
- RPN/NRPN等の元CC列保持
- MPE zone/member構成
- MPE/per-note expressionのChannel関連付け
- MIDI EventとNote On/Offのsame-tick order
- 複数Melody Trackの明示
- Lead/Harmony/Backing等のLyric Voice関係
- Voice単位のLyric Alignment参照

## 4.0.0 Coreに含めない

- Physical MIDI Port assignment
- OS MIDI Device ID
- DAW Track routing
- Audio output routing
- Plugin preset/state
- System Exclusive
- MIDI Clock
- MIDI Time Code
- MIDI Machine Control
- MIDI Show Control
- MIDI 2.0 UMP
- MIDI-CI / Property Exchange
- Audio Clip本体
- 歌唱エンジン状態
- 一般DAW Automation Lane
- 完全な楽譜組版

## Portの境界

OSTFは次まで保存します。

```text
Song
  → Track
    → Logical MIDI Bus
      → MIDI Channel
        → Note / MIDI Event
```

次はHost側です。

```text
Logical MIDI Bus
  → Physical / Virtual MIDI Port
    → Hardware / Plugin / DAW destination
```

これにより複数Port相当の曲情報を失わず、特定PCやDAWの配線をOSTFへ固定しません。

## NoteとEventの境界

`notes[]`は引き続きConcrete Noteの正本です。

Note On/Offを`midiEvents`へ二重保存しません。

`midiEvents[]`はNote以外のChannel Voice/Mode制御です。

## 3.xとの境界

OSTF 3.5は3.xの最終版です。

3.5 Noteの意味は4.0で変更しません。
4.0 Eventは3.5 Noteを補助できますが、Noteを別形式へ再解釈しません。
