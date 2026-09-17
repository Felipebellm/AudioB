# AudioB — Development Setup (Ubuntu)

Target development machine: **Dell Inspiron, i7 8th gen, 16 GB RAM, M.2 SSD, Radeon 550, Ubuntu LTS.**

This is the right platform for this project. Ardour is developed on Linux, every Ardour developer builds on Linux, and the Windows build path is explicitly unsupported by the project. Nothing below fights the grain.

Hardware notes:
- **i7 8th gen / 16 GB** — fine. First full build is the only slow part (see timings below); incremental rebuilds are seconds.
- **M.2 SSD** — matters more than you'd think; the build produces a lot of intermediate objects.
- **Radeon 550** — irrelevant to DAW performance, but a quiet win: the open-source `amdgpu` driver is in-kernel, so none of the proprietary-driver friction an NVIDIA card would bring.
- **Audio interface** — not required to start (onboard audio works), but a class-compliant USB interface is the single biggest latency/quality-of-life upgrade on Linux, and needs no drivers.

---

## Step 0 — Prove it builds before planning anything else

This is the gate. Until AudioB compiles and runs on your machine, no feature plan is real. Timebox it to a weekend. On Ubuntu this should be straightforward — unlike Windows, where it would not be.

---

## Step 1 — System audio stack

Modern Ubuntu LTS ships **PipeWire**, which now replaces PulseAudio *and* the standalone JACK server while providing both APIs. Ardour talks to it through PipeWire's JACK compatibility layer, so you do not install or run jackd separately.

Recommended:

```bash
sudo apt install linux-lowlatency
```

Recent Ubuntu kernels use `PREEMPT_DYNAMIC` (full preemption by default), which is adequate for most audio work without a separate realtime kernel — but the lowlatency kernel still helps under load.

Give yourself realtime privileges (required for low-latency audio):

```bash
sudo usermod -aG audio $USER
```

Log out and back in for the group change to apply.

If you want a GUI for buffer sizes, realtime privileges and CPU governor, the **Ubuntu Studio Audio Configuration** tool can be installed on plain Ubuntu — you do not need to reinstall as Ubuntu Studio. With PipeWire plus the lowlatency kernel and a dedicated interface, stable latency under 10 ms is realistic.

---

## Step 2 — Build dependencies

The robust way — let apt resolve the exact set Ubuntu uses to build its own Ardour package. First enable source repositories (Software & Updates → Ubuntu Software → tick **Source code**, or edit `/etc/apt/sources.list.d/ubuntu.sources` to add `deb-src` to the `Types:` line), then:

```bash
sudo apt update && sudo apt build-dep ardour
```

This is preferable to a hand-written package list, which drifts out of date. If `build-dep` is unavailable, the manual baseline is:

```bash
sudo apt install build-essential git python3 libboost-dev libcurl4-openssl-dev libfftw3-dev liblo-dev liblilv-dev lv2-dev libpangomm-1.4-dev libsamplerate0-dev libsndfile1-dev libxml2-dev libgtkmm-2.4-dev libaubio-dev libasound2-dev libzita-resampler-dev libzita-alsa-pcmi-dev libarchive-dev librubberband-dev libusb-1.0-0-dev libudev-dev libcppunit-dev libreadline-dev libwebsockets-dev
```

Cross-check against the authoritative list at [ardour.org/current_dependencies.html](https://ardour.org/current_dependencies.html) if configure complains.

---

## Step 3 — Get the source

```bash
git clone https://github.com/Ardour/ardour.git AudioB
cd AudioB
git remote rename origin upstream
git remote add origin https://github.com/Felipebellm/AudioB.git
```

Keeping `upstream` separate from `origin` is what makes it possible to pull Ardour's ongoing work into your fork later. Ardour moves fast (9.0 → 9.8 in roughly six months), so this matters.

Do your own work on branches, and keep your `master` tracking upstream cleanly:

```bash
git checkout -b feature/my-change
```

---

## Step 4 — Configure and build, branded as AudioB

Ardour's build system exposes `--program-name` (see `wscript:840`, *"The user-visible name of the program being built"*). This is the supported rebranding mechanism — the same one Harrison Mixbus uses — so you get an app called AudioB without patching source or touching Ardour's trademark.

```bash
./waf configure --optimize --freedesktop --program-name=AudioB --with-backends=jack,alsa,dummy
./waf -j$(nproc)
sudo ./waf install
```

Notes:
- `--optimize` is **required for a real build** — waf defaults to a debug build.
- `--freedesktop` generates the `.desktop` and MIME files so AudioB appears properly in your application menu.
- `jack` backend routes through PipeWire's JACK layer; `alsa` is the direct fallback.
- Valid backends are `jack,alsa,portaudio,coreaudio,pulseaudio` (`wscript:844`).

**Expected timings on your machine:** first full build roughly **30–60 minutes** (~1.1M lines of C++, 4 cores / 8 threads). This is a one-time cost. After that, changing one file and rebuilding takes seconds. If the linker exhausts memory on 16 GB, drop to `./waf -j4`.

Building from source also gives you the fully unrestricted application — the 10-minute-silence limitation applies only to the official free *demo binary*, never to a source build.

---

## Step 5 — Run it

```bash
AudioB
```

Create a session, confirm audio I/O works through PipeWire, load a plugin, record something. **If you reach this point, the project is viable** and everything in [ARDOUR_GAP_ANALYSIS.md](ARDOUR_GAP_ANALYSIS.md) becomes actionable.

---

## Working method

The plan is "use it daily, fix what annoys me." That's the strongest possible basis for this kind of project — it's how REAPER started, and how Ardour itself started. Practical advice for keeping it sustainable:

1. **Use AudioB for real music first.** Let genuine friction generate the backlog, not speculation.
2. **Keep a friction log.** Every time something annoys you mid-session, write it down rather than fixing it immediately — you're wearing the musician hat at that moment.
3. **Start with small, self-contained changes** (keybindings, defaults, UI annoyances) before touching the engine. It builds familiarity with a very large codebase.
4. **Rebase onto upstream regularly.** Small, frequent merges; never let the fork drift for months.
5. **Consider upstreaming the good bits.** Fixes that aren't specific to your workflow are worth contributing back — you get expert code review for free and permanently shed the maintenance burden for that code.

---

## Sources

- [Building Ardour on Linux](https://ardour.org/building_linux.html)
- [Ardour current dependencies](https://ardour.org/current_dependencies.html)
- [Ardour development](https://ardour.org/development.html)
- [Compile Ardour on Ubuntu — Tomasz Muras](https://muras.eu/2024/06/02/compile-ardour-in-Ubuntu-22.04/)
- [Ubuntu Studio LTS release notes](https://ubuntustudio.org/2026/04/ubuntu-studio-26-04-lts-released/)
- [Ubuntu Studio audio configuration](https://ubuntustudio.org/audio-configuration/)
- [Configure PipeWire for low-latency audio on Ubuntu](https://oneuptime.com/blog/post/2026-03-02-configure-pipewire-low-latency-audio-ubuntu/view)
- Primary source: Ardour `wscript` (configure options), local clone
