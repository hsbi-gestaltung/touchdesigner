# AKAI APC Mini mk1 for TouchDesigner

A TouchDesigner component (`apcMiniEmu.tox`) that emulates a complete AKAI APC Mini
mk1: 8×8 pad grid, 16 round buttons, Shift, and 9 faders. Build your network against
the emulator, then swap in the real hardware later — same physical layout as the
reference photo below.

![AKAI APC Mini mk1 for TouchDesigner — panel screenshot](./assets/screenshots/screenshot-akai-apc-mini-mk1-interface.png)

## Support

This component is free (and stays free). If it saves you a controller or a
headache, you can support development on Ko-fi:
**[ko-fi.com/axelzander](https://ko-fi.com/axelzander)**

## Install

> **Download:** grab the latest release zip from the
> [Releases page](../../releases) — it bundles the `.tox`, this README, and the
> demo project.

1. Copy `td/apcMiniEmu.tox` into your project (drag it into the network editor), or
   save it to your TouchDesigner Palette for reuse across projects.
2. Wire its single CHOP output (`out1`) into your patch.
3. Requires TouchDesigner 2023.x or newer. No commercial-license-only operators are
   used — works in the free non-commercial edition.

## Usage

- Every physical control is a live, clickable/draggable panel — click a pad or round
  button, drag a fader — exactly like the hardware.
- Custom parameters (on the component itself) control behavior:
  - **Midi** page — `Midichannel` (informational), `Outputmode` (Normalized 0-1 /
    Raw 0-127), `Togglemode` (pads latch on/off instead of momentary).
  - **Style** page — background/pad/button/fader colors.
  - **Geom** page — `Uiscale` and spacing/size constants.
  - **Labels** page — `Userlabelstable` points at the editable label table;
    `Resetlabels` clears every label.
  - **Build** page — `Rebuildui` regenerates the entire surface from the config
    table (use after editing `config/controlConfig`).
  - **Info** page — `Version`.
- **User labels**: open `config/userLabels` (table DAT inside the component) and
  type a label next to any channel name — it renders live under the matching pad,
  round button, or fader. The table is plain text and lives inside your `.toe`, so
  labels save automatically with your project; export/import it like any other DAT.

## Design principle: drop-in parity

UI layout mirrors the physical panel 1:1. Output values are normalized 0-1 by
default (buttons: 0 = released, 1 = pressed; faders: 0-1 continuous), matching
TouchDesigner's MIDI In CHOP defaults for a real APC Mini on MIDI channel 1.

**Channel names are friendly, not raw MIDI names** (`pad1`, `sceneClipStop`,
`trackVolume`, `shift`, `fader1`, `faderMaster`...) — this was a deliberate choice
to keep downstream networks readable. The exact MIDI note/CC each control
corresponds to is documented in the table below and stored in
`config/controlConfig` inside the component, so it's straightforward to build a
small rename layer (`ch1n{note}` / `ch1c{cc}` → the same friendly names) if you
want to route a real MIDI In CHOP through the identical naming convention instead
of swapping the whole component out.

## Channel reference

`pad1` = bottom-left grid pad = MIDI note 0. Pads number left-to-right, then
bottom-to-top (`padN` = note `N-1`), matching the note order the physical hardware
actually sends.

### 8×8 pad grid — `pad1`–`pad64` (notes 0–63)

| Grid row (top→bottom) | Channels | Notes |
|---|---|---|
| Row 1 (top) | pad57–pad64 | 56–63 |
| Row 2 | pad49–pad56 | 48–55 |
| Row 3 | pad41–pad48 | 40–47 |
| Row 4 | pad33–pad40 | 32–39 |
| Row 5 | pad25–pad32 | 24–31 |
| Row 6 | pad17–pad24 | 16–23 |
| Row 7 | pad9–pad16 | 8–15 |
| Row 8 (bottom) | pad1–pad8 | 0–7 |

Momentary by default (`Togglemode` off): 1 while pressed, 0 on release. With
`Togglemode` on, each press flips the pad's state.

### Scene launch round buttons (right column, top→bottom) — notes 82–89

| Channel | Hardware label | Note |
|---|---|---|
| sceneClipStop | Clip Stop | 82 |
| sceneSolo | Solo | 83 |
| sceneRecArm | Rec Arm | 84 |
| sceneMute | Mute | 85 |
| sceneSelect | Select | 86 |
| scene6 | (unlabeled) | 87 |
| scene7 | (unlabeled) | 88 |
| sceneStopAllClips | Stop All Clips | 89 |

Momentary. Labels are Ableton semantics printed on the hardware — no special logic
attached, matching real APC Mini behavior.

### Track round buttons (bottom row, left→right) — notes 64–71

| Channel | Hardware label | Note |
|---|---|---|
| trackUp | ▲ | 64 |
| trackDown | ▼ | 65 |
| trackLeft | ◄ | 66 |
| trackRight | ► | 67 |
| trackVolume | Volume | 68 |
| trackPan | Pan | 69 |
| trackSend | Send | 70 |
| trackDevice | Device | 71 |

Momentary, raw notes only.

### Shift — note 98

`shift` — momentary, no LED on hardware, no modifier logic (matches the real
device: Shift semantics live in Ableton's script, not the controller itself).

### Faders — CC 48–56

| Channel | Function | CC |
|---|---|---|
| fader1–fader8 | Track faders 1–8 (below grid columns 1–8) | 48–55 |
| faderMaster | Master fader (right, below scene buttons) | 56 |

Continuous 0–1 (or 0–127 in Raw output mode).

## What's in this repo

- `td/apcMiniEmu.tox` — the component, drag-and-drop or Palette-ready.
- `assets/` — hardware reference photo and screenshots.

