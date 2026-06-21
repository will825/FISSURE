<div align="center">

<img src="screenshots/icon.png" width="120" alt="Fissure Icon" />

# FISSURE

### AI Stem Splitter for Mac
**Split any track into vocals, drums, bass, and more — fully offline, no subscription.**

[![Download on Gumroad](https://img.shields.io/badge/Download-Free-brightgreen?style=for-the-badge)](https://terraecho.gumroad.com/l/rlpqdr)
![Platform](https://img.shields.io/badge/Platform-macOS-lightgrey?style=for-the-badge)
![Version](https://img.shields.io/badge/Version-1.0.0-orange?style=for-the-badge)
![Rust](https://img.shields.io/badge/Rust-Backend-orange?style=for-the-badge&logo=rust)
![React](https://img.shields.io/badge/React-Frontend-blue?style=for-the-badge&logo=react)

</div>

---

## Screenshots

<div align="center">

<img src="screenshots/dropzone.png" alt="Fissure Drop Zone" width="800" />

<img src="screenshots/stems.png" alt="Fissure Stem Cards" width="800" />

<img src="screenshots/main.png" alt="Fissure History View" width="800" />

</div>

---

## Features

**3 Separation Modes**
- **4-Stem** — Vocals · Drums · Bass · Other. Fast, general purpose.
- **6-Stem** — Vocals · Drums · Bass · Guitar · Piano · Other. Live-instrument focus.
- **Vocal Priority** — Cleanest possible vocal isolation using MDX-Net.

**Core**
- **100% Offline** — all processing happens locally on your Mac. No data sent anywhere, ever.
- **Universal Binary** — runs natively on Apple Silicon (M1/M2/M3/M4) and Intel Macs.
- **Drag to DAW** — drag stems directly from Fissure into Ableton, Logic, Pro Tools, or any DAW.
- **Auto Analysis** — BPM, key, and bar count detected automatically when you load a file.
- **Separation History** — revisit and replay past sessions at any time.
- **Clean Producer UI** — dark, distraction-free interface built for music makers.

---

## Tech Stack

**Frontend** — React (TypeScript), Vite, Tailwind CSS, with a custom CSS-variable theming system.

**Core / Backend** — Rust handles all audio processing and inference:

| Crate | Role |
|---|---|
| Tauri 2 | Desktop shell (Rust backend + WKWebView frontend) |
| `ort` (ONNX Runtime) | Runs AI models locally — CoreML on Apple Silicon, CPU fallback |
| Symphonia | Audio decoding (WAV, FLAC, MP3, AIFF, OGG) |
| Rubato | High-quality resampling to 44.1 kHz |
| RealFFT | STFT / ISTFT for spectral processing |
| Hound | 24-bit WAV encoding for stem output |
| Tokio + Reqwest | Async model downloads and background tasks |

**AI Models** — downloaded once on first launch (~590 MB total):
- `htdemucs.onnx` — 4-stem separation (hybrid transformer, time + frequency domain)
- `htdemucs_6s.onnx` — 6-stem separation
- `Kim_Vocal_2.onnx` — MDX-Net vocal isolation

**Build & Distribution** — universal binary via `lipo` (aarch64 + x86_64), packaged to a signed DMG, distributed on Gumroad.

---

## Architecture Highlights

A few of the more interesting engineering problems behind Fissure:

**Hand-built Rust audio pipeline.**
Decoding, resampling, STFT/ISTFT, overlapping-chunk reconstruction, and WAV encoding are all implemented directly in Rust rather than shelling out to a Python runtime — keeping it fast and dependency-light.

**On-device ONNX model serving.**
A lazy-loaded session pool caches one ONNX session per model, behind an execution-provider layer that prefers CoreML on Apple Silicon and falls back to CPU. Models are verified by minimum file size on download and loaded only when first needed.

**Model-specific tensor handling.**
The 4-stem `htdemucs` model is a hybrid transformer requiring both a time-domain `mix` tensor `[1, 2, 441000]` and a frequency-domain `spec` tensor `[1, 2, 2048, 431, 2]` (N_FFT=4096, HOP=1024), with its time-domain output at a non-obvious second tensor index. The 4-stem and 6-stem models also use different internal segment sizes (441,000 vs 343,980 samples) — handled per-model rather than a single global constant.

**Native drag-to-DAW.**
Stems drag out of the app as real OS files using `tauri-plugin-drag`. A module-level drag state flag prevents the drop zone from re-ingesting a stem mid-drag.

**iCloud-safe build pipeline.**
The project lives on an iCloud-synced Desktop. Putting build artifacts in `dist/` caused iCloud to create conflict copies of `index.html` on every Vite rebuild, leaving Tauri to embed a stale file. The build script signs the `.app` in `/tmp` (to avoid iCloud xattr corruption of the codesign seal) and outputs DMGs to a separate `releases/` folder.

---

## System Requirements

| | |
|---|---|
| **OS** | macOS 12 Ventura or later |
| **Arch** | Apple Silicon or Intel |
| **Disk** | ~600 MB (AI models download once on first launch) |
| **Internet** | Required only for the first-launch model download |

---

## Download

**[→ Get Fissure on Gumroad (Free)](https://terraecho.gumroad.com/l/rlpqdr)**

---

## Installation

1. Download the DMG from Gumroad
2. Open it and drag **Fissure** into Applications
3. On first launch, macOS will block the app — this is normal for indie apps not signed with an Apple Developer certificate

**First-launch security steps:**
1. Double-click Fissure — click **Done** on the warning (not Move to Trash)
2. Open **System Settings → Privacy & Security**
3. Scroll to Security — click **Open Anyway** next to Fissure
4. Enter your Mac password when prompted
5. Click **Open Anyway** on the final dialog

You only need to do this once.

---

## How It Works

1. Drop in any audio file (WAV, FLAC, MP3, AIFF, OGG)
2. Choose a separation mode — 4-Stem, 6-Stem, or Vocal Priority
3. Hit **Split Stems** and let the models process on-device
4. Play back each stem, then drag them straight into your DAW

---

## About

Built by **Will Hall** / [Terra Echo Studios](https://terraecho.gumroad.com).

Questions or issues? → willhall.wch@gmail.com

---

<div align="center">
<sub>FISSURE v1.0.0 // TERRA ECHO STUDIOS</sub>
</div>
