# OSTF 4.0 Core Scope

> English edition. Japanese: [`OSTF4_SCOPE.ja.md`](OSTF4_SCOPE.ja.md)


## Core boundary

OSTF 4.0 Core represents:

- structured song metadata;
- timeline/form/tempo/meter/key;
- harmony and scales;
- arrangement tracks, clips, notes, sound slots and drum kits;
- lyrics and alignments;
- shared editing/provenance/localization structures inherited from 3.5;
- MIDI channel-aware performance events;
- Logical MIDI Buses;
- MPE zone/member relationships;
- multiple melody declarations;
- explicit lyric voices.

## Outside Core 4.0.0

The following remain outside Core:

- physical/OS MIDI port assignment;
- Ableton/Cubase/Logic/etc. track routing;
- audio-interface routing;
- plug-in instance/state restoration;
- audio clips and full mixer state;
- SysEx;
- MIDI Clock/MTC/MMC/MSC;
- MIDI 2.0 UMP/MIDI-CI/Property Exchange;
- synthesis-engine internals;
- transient application UI state.

## Why channels are Core but physical ports are not

Channel identity is required to preserve standard MIDI performance semantics and MPE. Physical ports are environment-dependent. OSTF therefore stores a portable **Logical MIDI Bus** and leaves the mapping from logical bus to real device/DAW destination to the host.

## Future extensions

Future versions may standardize new common exchange needs, but they should receive typed, documented Core fields rather than being permanently hidden inside arbitrary application blobs.
