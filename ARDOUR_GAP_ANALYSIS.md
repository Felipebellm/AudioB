# Ardour as a Base for AudioB — Gap Analysis

Comparison of the feature catalog in [PLANNING.md](PLANNING.md) against **Ardour 9.x**, the leading open-source DAW, evaluated as a potential foundation for AudioB.

Findings below were verified against the actual Ardour source tree (shallow clone of `github.com/Ardour/ardour`, ~**4,072 C/C++ files / ~1.1M lines**), not just documentation — several secondary sources were wrong (e.g. claims that Ardour lacks VST3 support; the source contains a full `libs/vst3` host implementation).

Legend:
- ✅ **Solid** — already competitive; inherit as-is
- 🟡 **Partial** — exists but needs work to match commercial DAWs
- ❌ **Missing** — must be built from scratch

---

## 0. The licensing constraint (read this first)

Ardour is **GPL v2.0 or later** (`COPYING`). This is the single most consequential fact about using it as a base:

- Any AudioB derived from Ardour's code **must also be GPL** and ship its source. A closed-source or proprietary commercial AudioB is not possible on this foundation.
- Ardour's `COPYING` includes an explicit **plugin clarification**: plugins loaded through third-party APIs (VST, AudioUnits, LV2, LADSPA, CLAP) are *not* considered derivative works. So AudioB could still host proprietary commercial plugins without a license conflict.
- The commercial precedent exists: **Harrison Mixbus** is a paid product built on the Ardour codebase, so a commercial GPL product is viable — the revenue model just has to be built on distribution/support/branding rather than code secrecy.
- "Ardour" is a trademark; a fork must be rebranded (which AudioB is anyway).

**This needs an explicit decision before any code is written.** If AudioB is ever intended to be proprietary, Ardour is the wrong base and the whole analysis below is moot.

---

## 1. What Ardour already does well (inherit, don't rebuild)

These are the hardest, least glamorous parts of a DAW — and Ardour has them at professional quality.

| Feature area | Status | Evidence in source |
|---|---|---|
| Audio backends (ALSA, CoreAudio, JACK, PortAudio/ASIO, PulseAudio) | ✅ | `libs/backends/` — 6 backend implementations |
| Plugin hosting: **AU, LADSPA, LV2, VST2 (Win/Mac/Linux), VST3, Lua DSP** | ✅ | `libs/ardour/ardour/plugin_types.h` enum; `libs/vst3/`, `libs/fst/` |
| Non-destructive, non-linear editing model | ✅ | Region/playlist architecture, `libs/ardour/region.cc` |
| Time-stretch & pitch-shift — **three** engines | ✅ | `rb_effect.cc` (RubberBand), `st_stretch.cc` (SoundTouch), `sp_stretch.cc` (StaffPad), `midi_stretch.cc` |
| Mixer: buses, sends, VCA masters, sidechain routing | ✅ | `libs/ardour/route.cc`, `route_group.cc` |
| Automation (Off / Write / Touch / Play / Latch) | ✅ | `enum AutoState`, `libs/ardour/ardour/types.h:211` |
| Clip launching / Session-view equivalent + cue recording | ✅ | `libs/ardour/triggerbox.cc` (Ardour 9.0 feature) |
| Per-region/clip plugin FX | ✅ | `libs/ardour/region_fx_plugin.cc` (Ardour 9.0) |
| Control surfaces — **18 device modules** | ✅ | `libs/surfaces/`: Mackie, FaderPort/FaderPort8, Push2, Launchpad Pro/X, Launch Control XL, Launchkey Mk4, Maschine2, US2400, Console1, CC121, Contour, OSC, WebSockets, generic MIDI |
| Surround / immersive audio architecture | ✅ | `surround_send.cc`, `surround_return.cc`, `surround_pannable.cc`, VBAP panner, `a-vapor.lv2` |
| Timecode sync (LTC/MTC/MMC) | ✅ | `libs/libltc/` |
| Loudness analysis (EBU R128 / LUFS) + export normalization | ✅ | `libs/audiographer/src/general/loudness_reader.cc`, `analyser.cc` |
| Transient/onset detection | ✅ | `onset_detector.cc`, `rhythm_ferret.cc`, `libs/vamp-plugins/` |
| **Pro Tools session import** | ✅ | `libs/ptformat/` — a genuine competitive advantage |
| **AAF interchange** (post-production pipelines) | ✅ | `libs/aaf/` |
| Lua scripting API (≈ REAPER's ReaScript) | ✅ | `libs/lua/`, `luabindings.cc`, `lua_api.cc`, Lua-as-plugin-type |
| Video timeline & scoring to picture | ✅ | `gtk2_ardour/video_timeline.cc` (via harvid/xjadeo) |
| Crash recovery / session snapshots / undo history | ✅ | `save_history`/`restore_history`; 9.8 caps recording loss at ~5s |
| Musical keys & scales with note enforcement | ✅ | `scale.h`, `scale_provider.h`, `chord_provider.h` (Ardour 9.8) |
| Multi-touch GUI | ✅ | Ardour 9.0, Windows + Linux |

**Takeaway:** roughly the entire §1–§5, §7, §11, §13, §14 and §16 of PLANNING.md is already built and battle-tested. Replicating this from zero is a multi-year, multi-developer effort.

---

## 2. Partial — exists, but not yet competitive (improve)

| Feature | Ardour today | What's needed for parity |
|---|---|---|
| **Comping / take management** | Playlists provide take lanes | Pro Tools/Logic-grade comping UI: swipe-to-comp across lanes, take ratings, auto-crossfade at comp boundaries |
| **Bundled effects** | Only 7 plugins: `a-comp`, `a-delay`, `a-eq`, `a-exp`, `a-reverb`, `a-vapor`, `a-fluidsynth` | A credible stock set: brickwall limiter, multiband compressor, saturation, chorus/flanger/phaser, convolution reverb (`zita-convolver` is already vendored but not exposed as a user plugin), de-esser, transient shaper |
| **Automation modes** | Off/Write/Touch/Play/Latch | Missing **Trim** and **Latch Preview** (both present in Pro Tools and REAPER) |
| **Step sequencing / drum programming** | Step entry exists; 9.8 added an arpeggio MIDI tool | No integrated drum-machine environment (cf. Cubase 14 Drum Track, FL Channel Rack) |
| **Chord tooling** | `chord_provider.h`, 9.5 chord-drawing sidebar | No chord *suggestion*/progression generation, no voice-leading modes (cf. FL Studio 2026 Chord Stamp) |
| **Tempo detection from audio** | Onset detection via Rhythm Ferret + Vamp | No one-click tempo extraction / automatic tempo mapping (cf. Studio One 7) |
| **Atmos / ADM BWF delivery** | Surround architecture + `a-vapor` renderer | Not a certified Dolby Atmos renderer with full ADM BWF master export (cf. Pro Tools, Logic, Nuendo) |
| **Mastering chain** | Loudness analysis + dithering on export | No reference-track A/B comparison, no loudness-matched monitoring, no streaming-target presets |
| **Video support** | Video timeline present | Depends on external `harvid`/`xjadeo` binaries — fragile install story; should be embedded |
| **Content browser** | Functional file browser with preview | No tagging, no curated library, no semantic/natural-language search |

---

## 3. Missing entirely — must be built (this is where AudioB differentiates)

| Feature | Why it matters | Build difficulty |
|---|---|---|
| **AI stem separation** | Zero occurrences in the source. Now standard in Ableton Live 12 Suite and Studio One 7 — a table-stakes expectation in 2026 | Medium — integrate an existing model (Demucs-class) as an offline region operation |
| **Modulation system** (Bitwig-style) | Ardour has no modulator layer at all; its developers have stated modulation isn't part of the design. This is Bitwig's entire identity: LFOs/MSEGs/envelope followers routable to *any* parameter, including mixer params | **High** — touches the core parameter/automation architecture |
| **CLAP plugin support** | Ardour supports 6 formats but not CLAP; Studio One 7 and Bitwig have adopted it. Ardour upstream would accept a contribution but hasn't prioritized it | Medium — well-specified open API, cleanly slots beside the existing VST3 host |
| **Notation / score editor** | Verified absent. Cubase 14 shipped a Dorico-derived editor; Studio One has notation. Blocks classical/film-scoring users entirely | **High** — effectively a separate application |
| **Cloud collaboration & project sync** | No cloud layer; version history is local snapshots only | High — needs backend infrastructure, not just client code |
| **AI harmonic analysis (Chord ID)** | Logic Pro 12's flagship feature — identify chords from any audio/MIDI region | Medium |
| **AI session/accompaniment players** | Logic's Session Players (drummer/bassist/keys that follow the performance) | High |
| **Retroactive capture** | Logic's Flashback Capture / FL Studio's Audio Logger — recover a performance you forgot to record. Ardour has crash recovery but no rolling pre-record buffer | **Low** — cheap to build, high perceived value |
| **DAWproject interchange** | The emerging open cross-DAW session format (adopted by Cubase 14, Studio One, Bitwig). Ardour has AAF + PT import but not this | Low–Medium |
| **Sample marketplace integration** | Splice-class integration is now standard (Ableton 12, Studio One 7) | Medium — mostly API/business work |
| **Bundled instruments** | Only `a-fluidsynth` (SoundFont player) + `reasonablesynth`. No sampler, no drum machine, no wavetable/granular synth | High — but partially solvable by bundling existing LV2/CLAP instruments |
| **Natural-language sound search** | Where content browsing is heading (Logic on iPad) | Medium |
| **Network audio streaming** | Ableton's Link Audio streams audio between machines on a LAN; Ardour has transport sync but not audio streaming | Medium |

---

## 4. Strategic read

The gaps are not randomly distributed, and that's the good news:

**Ardour has solved the expensive, invisible problems** — the real-time audio graph, latency compensation, plugin hosting across six formats, automation, routing, timecode, file I/O, control surfaces, crash safety. This is the part that takes years and where bugs are catastrophic and unglamorous.

**What Ardour lacks is almost exactly the modern commercial differentiator set** — AI features, modulation, content/instruments, collaboration, and UX polish. These are the things users actually compare DAWs on in 2026, and they're mostly *additive* — they can be layered on without rewriting the engine.

So the fork thesis is sound: inherit the engine, compete on the layer above it.

### Suggested priority ordering for AudioB

**Tier 1 — high value, low-to-medium cost (best starting ROI)**
1. Retroactive/rolling capture — small feature, strong "how did I live without this" reaction
2. AI stem separation — highest expectation gap vs. competitors, solvable by integrating an existing model
3. CLAP plugin support — clean, well-specified, immediately widens the plugin ecosystem
4. Expanded stock effects (limiter, multiband, convolution reverb — `zita-convolver` is already in the tree)

**Tier 2 — defines the product's identity**
5. Modulation system — the single biggest architectural differentiator, but expensive
6. Chord ID / harmonic analysis
7. Tempo extraction and auto tempo-mapping
8. Comping UX overhaul

**Tier 3 — large, strategic, deferrable**
9. Cloud collaboration
10. Notation editor
11. Bundled instrument suite
12. Full Atmos/ADM delivery

### Open questions this analysis raises

1. **GPL: accepted or dealbreaker?** Everything depends on this.
2. **Fork vs. upstream contribution?** Some of Tier 1 (CLAP especially) is work Ardour upstream has said it would accept. Contributing upstream reduces long-term merge burden; forking gives full control. A hard fork of a 1.1M-line codebase means permanently owning rebases against a fast-moving project (9.0 → 9.8 in ~6 months).
3. **Can you build on the GTK stack?** Ardour's UI is GTK2-era (`gtk2_ardour/`) with a custom canvas. Much of the "UX polish" gap is rooted there, and modernizing it is arguably a bigger job than any single feature on this list.
4. **Realistic team size?** Most Tier 2/3 items are multi-person-year efforts.

---

## Sources

- [Ardour 9.0 — What's new](https://ardour.org/news/9.0.html)
- [Ardour 9.8 — What's new](https://ardour.org/whatsnew.html)
- [Ardour 9.0 Audio Workstation Released — Phoronix](https://www.phoronix.com/news/Ardour-9.0-DAW)
- [Ardour 9.0 brings Pianoroll Windows, Cue Recording — 9to5Linux](https://9to5linux.com/ardour-9-0-open-source-daw-brings-pianoroll-windows-cue-recording-and-more)
- [Ardour 9.5 Released with Major MIDI Editing Improvements — Linuxiac](https://linuxiac.com/ardour-9-5-daw-released-with-major-midi-editing-improvements/)
- [Ardour 9.8 Released: Scale Tracking, Crash Recovery, Clip Overhaul](https://www.linuxcompatible.org/story/ardour-98-released-scale-tracking-crash-recovery-and-clip-overhaul)
- [Ardour 9.8 Brings Scales Support — Open Source For You](https://www.opensourceforu.com/2026/08/ardour-9-8-brings-scales-support-to-open-source-daw/)
- [Ardour (software) — Wikipedia](https://en.wikipedia.org/wiki/Ardour_(software))
- [New plugin standard (CLAP) — Ardour Discourse](https://discourse.ardour.org/t/new-plugin-standard/107337)
- [CLever Audio Plugin API two years later — Libre Arts](https://librearts.org/2024/11/clap-api-two-years-later/)
- [Ardour Reviews 2026: Pros & Cons — Techjockey](https://www.techjockey.com/reviews/ardour)
- Primary source: `github.com/Ardour/ardour` @ shallow clone, September 2026
