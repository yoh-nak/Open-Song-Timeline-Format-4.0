# OSTF 4.0 — Public Example Index

> English edition. Japanese: [`EXAMPLE_INDEX.ja.md`](EXAMPLE_INDEX.ja.md)

The release contains **58** public `.ostf` files.

## Canonical examples

| File | Title / purpose |
|---|---|
| `examples/01-midi-performance.ostf` | MIDI Performance Example |
| `examples/02-mpe.ostf` | MPE Example |
| `examples/03-multi-voice.ostf` | Multi Voice Example |
| `examples/04-multi-bus.ostf` | Multiple Logical MIDI Buses |
| `examples/05-multi-channel-order.ostf` | Multi Channel and Same Tick Ordering |

## Golden Demo Songs

| File | Title / purpose |
|---|---|
| `examples/demo-songs/OSTF4_Demo_01_Neon_Crosswalk.ostf` | Neon Crosswalk |
| `examples/demo-songs/OSTF4_Demo_02_Rust_and_Rain.ostf` | Rust and Rain |
| `examples/demo-songs/OSTF4_Demo_03_Last_Platform.ostf` | Last Platform |
| `examples/demo-songs/OSTF4_Demo_04_Blue_Lantern.ostf` | Blue Lantern |
| `examples/demo-songs/OSTF4_Demo_05_Above_the_Vector.ostf` | Above the Vector |
| `examples/demo-songs/OSTF4_Demo_06_Velvet_Corners.ostf` | Velvet Corners |
| `examples/demo-songs/OSTF4_Demo_07_Glass_Garden_MPE_Lower.ostf` | Glass Garden |
| `examples/demo-songs/OSTF4_Demo_08_Orbiting_Light_MPE_Upper.ostf` | Orbiting Light |
| `examples/demo-songs/OSTF4_Demo_09_Two_Sides_of_Morning.ostf` | Two Sides of Morning |
| `examples/demo-songs/OSTF4_Demo_10_Answer_Back_Call_and_Response.ostf` | Answer Back |
| `examples/demo-songs/OSTF4_Demo_11_Shifting_Ground_Mixed_Meter.ostf` | Shifting Ground |
| `examples/demo-songs/OSTF4_Demo_12_Signal_Bloom_Complete_Showcase.ostf` | Signal Bloom — Complete OSTF 4.0 Showcase |

## Feature / Boundary Fixtures

| File | Title / purpose |
|---|---|
| `examples/feature-fixtures/OSTF4_Fixture_Bank_Select_And_Program.ostf` | Bank Select CC0/32 followed by Program Change |
| `examples/feature-fixtures/OSTF4_Fixture_CC_Common_Controllers.ostf` | Common musical CC controllers |
| `examples/feature-fixtures/OSTF4_Fixture_CC_MAX.ostf` | CC boundary controller 119 value 127 |
| `examples/feature-fixtures/OSTF4_Fixture_CC_MIN.ostf` | CC boundary controller 0 value 0 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_120.ostf` | Channel Mode controller 120 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_121.ostf` | Channel Mode controller 121 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_122.ostf` | Channel Mode controller 122 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_123.ostf` | Channel Mode controller 123 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_124.ostf` | Channel Mode controller 124 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_125.ostf` | Channel Mode controller 125 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_126.ostf` | Channel Mode controller 126 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Mode_127.ostf` | Channel Mode controller 127 |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Pressure_Max.ostf` | Channel Pressure Max |
| `examples/feature-fixtures/OSTF4_Fixture_Channel_Pressure_Min.ostf` | Channel Pressure Min |
| `examples/feature-fixtures/OSTF4_Fixture_Explicit_Channel_Without_Track_MIDI.ostf` | Event channel is explicit without track MIDI settings |
| `examples/feature-fixtures/OSTF4_Fixture_Graceful_Degradation.ostf` | Graceful degradation compatibility playback |
| `examples/feature-fixtures/OSTF4_Fixture_Lyric_Voices_Separate_Documents.ostf` | Separate lyric documents across voices |
| `examples/feature-fixtures/OSTF4_Fixture_Lyric_Voices_Shared_Document.ostf` | Shared lyric document across voices |
| `examples/feature-fixtures/OSTF4_Fixture_MPE_Lower_Members_1.ostf` | MPE lower zone with 1 member channels |
| `examples/feature-fixtures/OSTF4_Fixture_MPE_Lower_Members_15.ostf` | MPE lower zone with 15 member channels |
| `examples/feature-fixtures/OSTF4_Fixture_MPE_Upper_Members_1.ostf` | MPE upper zone with 1 member channels |
| `examples/feature-fixtures/OSTF4_Fixture_MPE_Upper_Members_15.ostf` | MPE upper zone with 15 member channels |
| `examples/feature-fixtures/OSTF4_Fixture_MultiChannel_Channels_1_16.ostf` | MultiChannel endpoints 1 and 16 |
| `examples/feature-fixtures/OSTF4_Fixture_Multiple_Buses_Same_Channel_Number.ostf` | Multiple buses reuse Channel 1 |
| `examples/feature-fixtures/OSTF4_Fixture_Multiple_Melody_Tracks.ostf` | Multiple melodyTrackIds |
| `examples/feature-fixtures/OSTF4_Fixture_NRPN_Raw_CC_Sequence.ostf` | NRPN represented as raw CC sequence |
| `examples/feature-fixtures/OSTF4_Fixture_Negative_Clip_Local_Event_Start.ostf` | Negative clip-local event start with nonnegative absolute time |
| `examples/feature-fixtures/OSTF4_Fixture_Pitch_Bend_Center.ostf` | Pitch Bend Center |
| `examples/feature-fixtures/OSTF4_Fixture_Pitch_Bend_Max.ostf` | Pitch Bend Max |
| `examples/feature-fixtures/OSTF4_Fixture_Pitch_Bend_Min.ostf` | Pitch Bend Min |
| `examples/feature-fixtures/OSTF4_Fixture_Poly_Pressure_Max.ostf` | Poly Pressure Max |
| `examples/feature-fixtures/OSTF4_Fixture_Poly_Pressure_Min.ostf` | Poly Pressure Min |
| `examples/feature-fixtures/OSTF4_Fixture_Probability_With_TargetNoteId.ostf` | Targeted expression follows note probability scheduling |
| `examples/feature-fixtures/OSTF4_Fixture_Program_Max_127.ostf` | Program Change 127 |
| `examples/feature-fixtures/OSTF4_Fixture_Program_Min_0.ostf` | Program Change 0 |
| `examples/feature-fixtures/OSTF4_Fixture_RPN_Pitch_Bend_Sensitivity_Raw_CC.ostf` | RPN represented as raw CC sequence |
| `examples/feature-fixtures/OSTF4_Fixture_Same_Tick_Order_Negative_To_After_Note.ostf` | Explicit same-tick order -1,0,1,2,3 |
| `examples/feature-fixtures/OSTF4_Fixture_Single_Channel_1.ostf` | Single Channel 1 |
| `examples/feature-fixtures/OSTF4_Fixture_Single_Channel_16.ostf` | Single Channel 16 |
| `examples/feature-fixtures/OSTF4_Fixture_Single_Channel_Inheritance.ostf` | Single-channel inheritance with omitted note/event channel |
| `examples/feature-fixtures/OSTF4_Fixture_TargetNoteId_All_Supported_Events.ostf` | targetNoteId on CC, Pitch Bend, Channel Pressure and Poly Pressure |
