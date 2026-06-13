# Air Instruments — Hand-Controlled Virtual Instrument

A virtual instrument you play with your hands in the air. No keyboard, no touchscreen — your index finger is the cursor, and hovering over a pad, instrument button, or volume slider triggers the action.

Built as a single self-contained HTML file using Web Audio API for synthesis and MediaPipe Hands for finger tracking.

## Features

- **5 instrument types** — switchable from the right-hand column:
  - **Drums** — Kick, Snare, Hi-Hat, Tom, Crash, Clap (4 sub-kits: 808, Acoustic, Lo-Fi, Rock)
  - **Bass** — Sub, Reese, Wobble, Slap, Funky, Acid
  - **Synth** — Saw, Square, Plucky, Detune, Supersaw, Arp
  - **Keys** — Piano, EP, Organ, Rhodes, Clav, Honky
  - **Pluck** — Bell, Marimba, Kalimba, MusicBox, Harp, Vibra
- **Vertical volume slider** on the left
- **Hand tracking** via MediaPipe Hands (legacy package, no WebGL/CPU regression)
- **Mouse / touch fallback** — click or drag to test without a camera
- **HiDPI-aware canvas** — fingertip marker stays aligned with the pads on retina displays
- **DPR-correct coordinate mapping** — finger position matches the pad layout across aspect ratios

## Run locally

The page needs a secure context (HTTPS or `localhost`) for the camera API. The easiest way:

```bash
# From the project directory
python3 -m http.server 8000
```

Then open <http://localhost:8000> in Chrome or Safari, click **Start Instruments**, and grant camera access.

## How it works

- **Hand tracking** uses the legacy `@mediapipe/hands@0.4` UMD package with `modelComplexity: 0` (lite, fast on CPU). The `Hands.send({ image: video })` API feeds video frames to the detector.
- **Coordinate mapping** accounts for the camera's actual `videoWidth`/`videoHeight`, the canvas's `devicePixelRatio`, and the `object-fit: cover` cropping of the background `<video>`. The fingertip lands in the same place the user sees in the mirrored feed.
- **Audio synthesis** is fully Web Audio API — no samples. Drums use filtered noise + oscillators with envelopes; bass and synth use detuned saws with lowpass filters and optional LFO modulation; keys use additive harmonic stacks; plucks use quick-decay additive tones with filter sweeps.

## File layout

```
computer-drum/
├── index.html      # Everything: UI + audio engine + hand tracking
├── .gitignore
└── README.md
```

## Tech

- Vanilla HTML / CSS / JavaScript — no build step
- Web Audio API (oscillators, biquad filters, gain envelopes, noise buffers)
- MediaPipe Hands (legacy UMD package)
- `getUserMedia` for camera access
- HTML5 Canvas for the fingertip marker and skeleton overlay
