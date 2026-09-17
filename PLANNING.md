# AudioB — Initial Planning

This document is the starting reference for AudioB: a professional Digital Audio Workstation (DAW) for recording, editing, mixing, and producing with plugins. The feature catalog below was compiled from the current (2026) feature sets of the leading commercial and independent DAWs — **Avid Pro Tools (Ultimate)**, **Apple Logic Pro**, **Ableton Live 12**, **Steinberg Cubase 14**, **PreSonus Studio One 7**, **Bitwig Studio 5**, **Image-Line FL Studio**, and **Cockos REAPER** — so we start scoping AudioB against what "professional" actually means in this market, not a guess at it.

It's organized by subsystem. Treat it as a menu to prioritize, not a v1 requirements doc — the next step is picking an MVP slice out of this list.

---

## 1. Audio Engine & I/O

- Multi sample-rate support (44.1kHz–192kHz+), internal 32-bit float (or 64-bit, like Pro Tools' mixer) processing to avoid rounding error buildup
- Low-latency driver support: ASIO (Windows), Core Audio (macOS), WASAPI fallback
- Multi-core / multi-thread DSP scheduling; freeze/bounce for CPU relief on heavy tracks
- High track-count ceiling: audio, aux/bus, instrument, and MIDI tracks handled as distinct types (Pro Tools Ultimate supports thousands of each)
- Sample-accurate automation, edits, and plugin delay compensation (PDC) across the whole signal path
- Network audio streaming between instances/devices (Ableton's Link Audio pattern) in addition to transport sync (Ableton Link, MTC/MMC, SMPTE)

## 2. Recording

- Multi-track simultaneous recording with per-track input routing
- Punch in/out — manual and automatic, with pre/post-roll and count-in
- Loop recording with **comping**: multiple takes on lanes, best-take assembly, take playlists
- Non-destructive track freeze and bounce-in-place
- Retroactive capture: a rolling record buffer that recovers a performance you didn't hit "record" for (Logic's Flashback Capture) and a continuous master-output logger (FL Studio's Audio Logger)
- Direct/low-latency hardware monitoring path independent of plugin latency

## 3. MIDI & Composition

- Full piano roll: velocity, CC/automation lanes, articulation, per-note probability (Cubase 14)
- Step sequencer / drum-pattern editor (Cubase Drum Track, FL Studio Channel Rack, pattern sequencers)
- MPE (MIDI Polyphonic Expression) input and instrument support
- Chord/harmony assistance: real-time chord detection and suggestion (Logic's Chord ID, FL Studio's Chord Panel/Chord Stamp with top-down/bottom-up voice leading), scale/key-locking for MIDI input
- Arpeggiators, groove/humanize quantization
- MIDI transformation tools — reusable, non-destructive operations applied to MIDI clips (Ableton 12)
- Notation/score editor for print-quality parts (Cubase 14's Dorico-derived score editor, Studio One notation)
- AI "session player" instruments that follow a performance in real time (Logic's Session Players)

## 4. Editing

- Non-linear, non-destructive editing as the default (never mutate source media)
- Elastic Audio / Flex Time / Warp-marker style time-stretching and pitch-shifting, independent of playback speed
- Crossfades with adjustable curves, fade automation, static-vs-automated volume offset editing
- Tempo detection/extraction from audio with one click; global transpose across a project
- Range-based editing tools, bounce groups, "paste bounced audio" workflows (render a clip's processed output back as audio in place)

## 5. Mixing

- Full mixer console: inserts, sends/returns, groups, VCA-style faders, routable buses
- Automation modes: Read, Write, Touch, Latch, Latch Preview, Trim — every plugin parameter automatable, not just volume/pan
- Sidechain input routing on dynamics processors
- Mixer reachable without leaving the arrangement/timeline view (Cubase's in-window MixConsole, Ableton 12's Arrangement-view mixer)
- Professional metering: LUFS/loudness, RMS, spectrum analyzer, phase correlation, multi-meter panels (Cubase SuperVision-style)

## 6. Plugins & Instruments

- Plugin format support: **VST3, VST2 legacy, AU (macOS), AAX, CLAP, LV2** — CLAP support is now a competitive differentiator (Studio One 7, Bitwig)
- Stock instruments: subtractive/wavetable/granular synths, sampler, drum machine
- Stock effects: EQ, compressor/limiter, gate, reverb, delay, modulation FX, distortion
- Deep modulation system: LFOs, envelope followers, multi-segment envelope generators (MSEGs), macro knobs, step modulators, and a mod matrix that can target **any** parameter — including mixer and track-level params, not just device knobs (Bitwig Studio 5's model)
- Instrument racks / chains for layering and keyboard/velocity splits

## 7. Arrangement & Workflow

- Linear timeline (Arrangement view) as the primary editing surface
- Clip-based, non-linear performance view with scene launching for live improvisation (Ableton Session View, Bitwig's clip launcher, Logic's Live Loops)
- Track folders/grouping, color coding, track freeze
- Deep customization: remappable key commands, macros, custom toolbars/themes (REAPER's model — the most extreme example on the market)
- Project templates, multi-level undo history, project snapshots/versioning

## 8. Mastering & Metering

- Master-bus limiter with streaming-loudness targets (Spotify/YouTube/Apple Music LUFS normalization presets)
- Dithering on bounce/export, reference-track A/B loudness-matched comparison
- Multiband dynamics and multiband metering

## 9. Collaboration & Cloud

- Cloud project sync and multi-user collaboration
- AI-powered stem separation (splitting a mixed track into vocals/drums/bass/other) — now standard-tier in Ableton Live 12 Suite and Studio One 7
- Version history / project snapshots with rollback
- Sample-marketplace integration (Splice-style) directly in the browser panel

## 10. Video & Post-Production

- Video track import with frame-accurate scoring to picture
- SMPTE timecode support for dialogue/ADR/sound-to-picture work
- Immersive/spatial audio: built-in Dolby Atmos renderer, Ambisonics, surround formats (5.1/7.1) — table stakes for a "professional" DAW as of 2026, present in Pro Tools, Logic, and Cubase

## 11. Hardware & Control Surfaces

- Standard control-surface protocols (Mackie Control/HUI-style) for third-party hardware
- Native/deep integration profiles for common controllers (e.g. Ableton Push/Move-class devices)
- Flexible MIDI-learn / custom controller mapping

## 12. AI-Assisted Features (2025–2026 trend across the market)

- AI stem separation (see §9)
- AI chord/harmony analysis of existing audio or MIDI (Logic's Chord ID)
- AI accompaniment/session players that adapt to a live performance (Logic's Session Players)
- Natural-language sample/sound search in the content browser
- Smart tempo detection and automatic tempo-mapping of imported audio

## 13. Extensibility / Scripting

- A scripting API (REAPER's ReaScript supports Lua, Python, and EEL2) for custom actions, batch processing, and UI extension
- OSC support for external control
- SDK for third-party DSP/plugin development if AudioB ever hosts its own plugin format

## 14. File & Project Interchange

- Import/export of standard formats: WAV, AIFF, FLAC, MP3, OGG
- Cross-DAW project interchange: OMF/AAF for post-production pipelines, and the newer open **DAWproject** format (adopted by Cubase 14) for moving whole sessions between DAWs
- Stem/track export presets for mixing and mastering handoff

## 15. Content & Library Management

- Bundled sound library: instruments, loops, samples
- Taggable, searchable browser with audio preview and drag-and-drop into the timeline
- Natural-language search over the sound library (Logic's iPad implementation points at where this is heading)

## 16. Reliability & Project Management

- Autosave with configurable interval, crash recovery, rolling backups
- Project templates for common session types (band recording, podcast, mixing-only, scoring)

---

## Sources

- [Ableton Live 12.4 release notes — synthanatomy.com](https://synthanatomy.com/2026/04/ableton-live-12-stay-creative-and-in-focus-an-overview-of-the-new-features.html)
- [Live 12 Release Notes — Ableton](https://www.ableton.com/en/release-notes/live-12/)
- [Top 10 Features in the Live 12 Update — Production Music Live](https://www.productionmusiclive.com/blogs/news/top-10-features-in-live-12-update)
- [Apple Logic Pro 12 update — synthanatomy.com](https://synthanatomy.com/2026/06/apple-logic-pro-12.html)
- [Final Cut Pro and Logic Pro updates — MacRumors](https://www.macrumors.com/2026/01/28/final-cut-pro-and-logic-pro-updated/)
- [Logic Pro for Mac release notes — Apple Support](https://support.apple.com/en-us/109503)
- [Pro Tools — Avid](https://www.avid.com/pro-tools)
- [Pro Tools Audio Mixing Software — Avid](https://www.avid.com/pro-tools/audio-mixing-software)
- [Buyer's Guide: Avid Pro Tools — Vintage King](https://vintageking.com/blog/buyers-guide-avid-pro-tools/)
- [Steinberg Cubase 14 review — MusicTech](https://musictech.com/reviews/digital-audio-workstations/steinberg-cubase-14-review/)
- [Steinberg Cubase 14 announced — Production Expert](https://www.production-expert.com/production-expert-1/steinberg-cubase-14-announced-with-a-raft-of-new-features)
- [Cubase 14 DAW update — synthanatomy.com](https://synthanatomy.com/2024/11/steinberg-cubase-14-daw-update-brings-pattern-sequencer.html)
- [Studio One Pro 7: 9 New Features — Splice Blog](https://splice.com/blog/top-features-presonus-studio-one/)
- [PreSonus Studio One Pro 7 arrives — Sound on Sound](https://www.soundonsound.com/news/presonus-studio-one-pro-7-arrives)
- [What's New In Studio One Pro 7 — PreSonus](https://www.presonus.com/blogs/home/what-is-new-in-studio-one-pro-7)
- [Bitwig Studio 5 now available — Sound on Sound](https://www.soundonsound.com/news/bitwig-studio-5-now-available)
- [Bitwig Studio 5, modulation engine — synthanatomy.com](https://synthanatomy.com/2023/04/bitwig-studio-5-modulation-engine-taken-to-the-next-level-with-msegs-and-more.html)
- [Bitwig Studio 5 Is Out Now — Bitwig](https://www.bitwig.com/stories/bitwig-studio-5-is-out-now-256/)
- [REAPER — About](https://www.reaper.fm/about.php)
- [REAPER — Audio Production Without Limits](https://www.reaper.fm/)
- [Reaper DAW Guide 2026 — softlinked.com](https://softlinked.com/multimedia-software/daw-software-reaper)
- [FL Studio 2026 — everything you need to know — MusicTech](https://musictech.com/news/gear/fl-studio-2026-everything-you-need-to-know/)
- [FL Studio 2026 — What's New — Image-Line](https://www.image-line.com/fl-studio/release/2026)
- [FL Studio 2026 rundown — Gear Gods](https://geargods.net/news/heres-the-rundown-of-whats-new-in-fl-studio-2026/)

---

## Next steps

This is a feature census, not a spec. To move forward we need to decide:

1. **Platform target** — desktop (Windows/macOS/Linux), which language/framework, native vs. web-based UI
2. **Audio engine strategy** — build a custom engine vs. building on an existing framework (e.g. JUCE)
3. **MVP scope** — which slice of the list above defines a usable v1 (likely: audio engine + basic multi-track recording/editing + mixer + VST3 hosting, deferring notation, Atmos, and AI features)
4. **Plugin format priority** — which formats to host first (VST3 is the pragmatic default for cross-platform reach)

Happy to turn any of these into their own planning doc once you've picked a direction.
