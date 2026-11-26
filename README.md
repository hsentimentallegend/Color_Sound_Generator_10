# Color Sound Generator Version 10

An improved version of the app used in the 2023 solo exhibition *The Color Cleanser*.

A web instrument that plays a photo as if reading it. Load a photo and press Play: a band of light slowly sweeps across it, and the colors inside the band ring out as sound. The composition of the photo becomes the score — the same photo always unfolds the same way, and a different composition makes entirely different music.

**Live:** https://hsentimentallegend.github.io/Color_Sound_Generator_10/

It runs from a single HTML file. No external libraries: every sound is synthesized on the spot with the browser's Web Audio API.

Part of the **Color Sound series** by [Hiroshi Mehata](https://www.mehatasentimentallegend.com/) (Experimental Music / Contemporary Art).

---

## How to play

1. Load a photo with **Choose a photo**, **Take a photo (phone)** or **Open camera**
2. Press **▶ Play** to start Scan
   - Every color extracted from the photo (up to 8) keeps sounding; the more of a color sits inside the band, the louder it gets
   - **Direction**: horizontal (band moves left–right) or vertical (band moves up–down). Horizontally striped photos change most with a vertical scan, and vice versa
   - **Speed**: how long the band takes to travel edge to edge (6–60 s)
   - **Reverb**: amount of hall-like reverberation (0–100%). Higher values push the dry sound back and widen the space
   - **Tremolo**: how deeply every color pulses (0–100%). Each color pulses at its own rate, so the voices drift in and out of phase
   - **Vinyl**: old-record character (0–100%) — the pitch wobbles like a turntable, with crackle and hiss on top. It colors everything, Glitch included
3. The photo itself is an instrument. Touch or trace it, and a processed accent of the color under your finger is layered softly behind Scan. It works with Scan stopped, and with several fingers at once
   - Tracing fires a short grain each time you cross into another color. Faster strokes make shorter grains, and the left–right position of your finger sets the stereo position
   - **Effect**: Reverse / Stutter loop / Octave shimmer / Tape stop / Random (changes every touch) / None
   - **Volume**: loudness of the touch sounds (0–200%), separate from the master volume
   - **Trigger**: tap for a fixed length, or hold to sound while pressed
   - **Length** (0.1–6 s), **Decay** (0% = sustained, 100% = plucked) and **Release** (fade-out after letting go)
4. Master volume, **Same-pitch colors** (shift colors that land on the same note to the next note, or raise them an octave) and **Bitcrush** can all be adjusted while playing
5. **Glitch** is the opposite of Scan: an aggressive, unpredictable mode
   - It reads the photo as raw data and breaks it. The read head warps around the photo, stutters, runs backwards and suddenly falls silent
   - **Chaos** (0–100%) goes from a faint glitch to total mayhem

> On iPhone, no sound may play while the silent switch is on. The camera only works on https pages.

---

## How colors become sound

Sound is decided in two layers.

### Layer 1: the key of the whole photo
The color with the strongest *area × saturation* becomes the key color. Its **emotion** sets the scale and root note for the whole photo.

| Color | Emotion | Scale |
|---|---|---|
| White | Clarity | Major |
| Black | Depth | Minor |
| Gray | Neutral | Pentatonic (major or minor by brightness) |
| Red | Passion | Major / Blues |
| Orange | Warmth | Major |
| Yellow | Joy | Major pentatonic |
| Yellow-green | Freshness | Major |
| Green | Peace | Pentatonic |
| Teal | Calm | Major / Minor |
| Blue | Melancholy | Minor |
| Purple | Mystery | Blues |
| Magenta | Creativity | Major / Minor |

### Layer 2: each color's sound
| Color property | Sound |
|---|---|
| Hue | Position within the Layer 1 scale (over 2 octaves). Every color falls in the same scale, so nothing clashes |
| Brightness | Volume (very dark colors drop an octave) |
| Saturation | Timbre: sine → triangle → sawtooth → square, getting sharper continuously as the filter opens |
| Share of the color inside the band | Volume during Scan (colors outside the band keep sounding faintly, by area) |
| Area | How much of the color lingers during Scan |
| Emotion | A sonic character (distortion, vibrato, tremolo, noise, overtones, FM, …) |

---

## Technical notes

- **Color extraction**: the image is downscaled and clustered into up to 8 colors with k-means (k-means++ initialization, fixed seed), so the same photo always gives the same result
- **Synthesis**: each color is built from Web Audio oscillators, filters, a WaveShaper and LFOs
- **Scan**: a per-pixel map of which color each pixel belongs to; each voice's volume smoothly follows that color's share inside the band, with a slow drift of a different period per color. The reverb uses an impulse response generated on the fly (decaying noise that darkens over time) through a ConvolverNode
- **Tremolo**: every voice has its own tremolo. Saturation shapes the pulse (smooth for muted colors, choppy for vivid ones), hue picks the rate from a set of ratios (1 : 9/8 : 5/4 : 4/3 …) so the voices phase against each other, and the rate itself slowly drifts. Peace and Calm, whose character is tremolo, pulse more deeply
- **Vinyl**: applied at the master output so everything wobbles together like a real turntable. Pitch wobble comes from modulating a short delay: wow at one 33⅓ rpm revolution (about 1.8 s), flutter, and slow random drift. A generated 11-second loop adds crackle, hiss and rumble, and the band is narrowed like an old pressing
- **Touch accents**: when a photo loads, each color's tone is pre-rendered in the background with an OfflineAudioContext. Touches play that material reversed, looped or pitch-shifted, sent into an echo to sit behind Scan
- **Glitch**: two sources driven by chance
  - Pixel glitch: the photo's raw R, G, B values are laid out as a waveform, and short segments are looped and reversed — the texture of the photo becomes the timbre
  - Chaos network: one oscillator per color, wired in a ring so each one frequency-modulates the next (cross-FM). A short delay inside the ring feeds the sound back into itself, so the state collapses in unpredictable directions. More saturated colors disturb their neighbors more strongly
  - A hard limiter at the end guards against sudden loud peaks
- **Bitcrush**: processed in an AudioWorklet (a dedicated audio thread) so heavy drawing never interrupts the sound, and bypassed entirely when off

---

## Background

This began as *Color Sound Generator V3*, built with Python + Streamlit as an extension app for the Color Cleanser exhibition. Version 10 keeps V3's idea of *color → emotion → scale* and rebuilds it for the web:

- **Every color is its own voice**, not just the single dominant color, so you can hear which color is which
- Instead of rendering a 10-second WAV, sound is **synthesized live in the browser**
- **Scan**, which reads the photo's composition as a score, is the heart of the instrument
- **Glitch** was added as its opposite: reading the photo as raw data and breaking it

---

## The Color Sound series

Version 10 reads a single photo as a score. Its sibling instruments play whatever your camera sees, live:

- **Gazer** — every cell's color becomes a roaring, distorted wall of sound
- **Hazer** — colors drift into slow ambient sound, with optional Satie-style piano
- **Scanner** — a scan bar sweeps across the screen and plays the colors it passes

→ https://hsentimentallegend.github.io/color-sound-scanner/

---

Created by [Hiroshi Mehata](https://www.mehatasentimentallegend.com/) — Experimental Music / Contemporary Art
