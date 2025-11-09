# EyeTalk — Gaze-powered keyboard for accessible communication

EyeTalk lets someone type with their eyes. It tracks gaze and blinks using a webcam, scans over an on-screen keyboard, and speaks characters/feedback using ElevenLabs TTS.

> **Repo layout**
>
> ```
> .
> ├─ main.py
> ├─ shape_predictor_68_face_landmarks.dat   # dlib facial landmarks model
> └─ README.md
> ```

---

## ✨ Features

- **Hands-free typing:** look left/right to choose a keyboard side, blink to select a key.
- **Audible feedback:** ElevenLabs text-to-speech says “left”, “right”, and reads letters aloud.
- **Lightweight:** Python + OpenCV + dlib; no deep learning GPU needed.
- **Configurable:** tweak blink sensitivity, scan speed, and gaze threshold without changing architecture.

---

## 🧰 Requirements

- **Python** 3.9–3.12
- **Webcam** (built-in or USB)
- **Packages:** `opencv-python`, `numpy`, `dlib`, `elevenlabs`
- **Facial landmarks model:** `shape_predictor_68_face_landmarks.dat` (already present)
- **ElevenLabs API key** (for TTS)

### Install (macOS / Linux)

```bash
# 1) create and activate a virtualenv (recommended)
python3 -m venv .venv
source .venv/bin/activate

# 2) install dependencies
pip install --upgrade pip
pip install opencv-python numpy elevenlabs dlib
```

⚠️ macOS note:
If dlib build fails, install build tools first:
```bash
xcode-select --install
brew install cmake
```

Then re-run pip install dlib. (On some systems pip install dlib==19.24.4 is more reliable.)

### Environment variables

Export your ElevenLabs credentials before running:
```bash
# required
export ELEVENLABS_API_KEY="YOUR_API_KEY_HERE"

# optional: pick a voice; Rachel is default
export ELEVENLABS_VOICE_ID="21m00Tcm4TlvDq8ikWAM"
```

---

## ▶️ Run

```bash
python3 main.py
```

You’ll see three windows:
- Frame – live camera with eye outlines
- Virtual Keyboard – the scanning keyboard & left/right selection screen
- Board – the text you’ve typed

Press ESC to quit.

---

## 🕹️ Interactive User Guide

1. Sit centered in front of the camera with good light. Keep your face within the frame.
2. Choose a side (LEFT / RIGHT):
   - EyeTalk measures your gaze ratio.
   - Look right (your right) and hold ~1 second → RIGHT side selected (you’ll hear “right”).
   - Look center/left and hold → LEFT side selected (you’ll hear “left”).
3. Scan & select letters:
   - Once in a keyboard, the highlighted key advances automatically every few frames.
   - Blink deliberately to select the highlighted key.
   - Special keys:
     - _ inserts a space
     - < is currently ignored (placeholder for backspace—see “Customize” below).
4. Listen for speech: EyeTalk speaks each selection using ElevenLabs.
5. Exit: Press ESC anytime.

### On-screen cues
- A gray loading bar at the bottom of the camera window fills while you’re blinking to confirm a key.
- Red eye contours = eyes open; turn green while a blink is being recognized.

---

## ⚙️ Configuration (edit constants in `main.py`)

- Blink threshold (line where we check blinking_ratio > 5):
  
  Lower it if your blinks aren’t detected; raise it if false-positives happen.
- Blink hold time:
  
  `frames_to_blink = 6` → how long you must keep a blink to confirm (shorten/lengthen to taste).
- Scan speed:
  
  `frames_active_letter = 9` → how long each key stays highlighted. Increase to slow down scanning.

- Gaze right cutoff:

  In the selection menu, if `gaze_ratio <= 0.9:` → smaller = require stronger rightward look; larger = easier to trigger right.

- Text-to-speech voice:
  
  Set `ELEVENLABS_VOICE_ID` or change the default in code.

---

## ❓ Troubleshooting

- `ELEVENLABS_API_KEY not set`

  You didn’t export the key in this shell. Run export ELEVENLABS_API_KEY="..." then rerun.

- **ImportError**: `set_api_key`

  That’s the old SDK API. This repo uses v2 (`ElevenLabs` client + `text_to_speech.convert`). Keep:
  ```bash
  from elevenlabs.client import ElevenLabs
  from elevenlabs.play import play
  ```

- No speech / audio device error

  Make sure your system has an output device selected. On macOS, check System Settings → Sound.

- Blank / frozen camera

  Close other apps using the webcam. On macOS, grant Terminal (or your IDE) Camera access in Privacy & Security.

- `shape_predictor_68_face_landmarks.dat` not found

  The file must be in the same directory as main.py (it is, by default).

- dlib fails to install

  Install build tools (`xcode-select --install` and `brew install cmake`) then `pip install dlib`.
  On some systems `pip install dlib==19.24.4` helps.

---

## ❤️ Why EyeTalk?

EyeTalk is for anyone with limited motor control who needs a simple, low-friction way to be heard. If you’re building accessibility tech, you’re doing important work—thank you.

