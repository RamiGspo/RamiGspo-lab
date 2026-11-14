# Voicebox

[Voicebox](https://github.com/jamiepine/voicebox) is a local voice studio:
text-to-speech with voice cloning (Qwen3-TTS, Kokoro, LuxTTS, Chatterbox, ...)
and Whisper transcription, wrapped in a Tauri desktop app.

These notes cover running it from source on Ubuntu 26.04 without a GPU,
the problems that came up, and how they were fixed.

## Files

| File | Description |
|------|-------------|
| `linux-dev-setup.patch` | Fixes for the Linux dev setup, against upstream commit `8af7efe` |

## Setup on Ubuntu 26.04

1. System libraries for the Tauri build:

   ```bash
   sudo apt install libwebkit2gtk-4.1-dev libgtk-3-dev libsoup-3.0-dev \
     librsvg2-dev libasound2-dev libayatana-appindicator3-dev patchelf
   ```

2. Tools, all installed per user without sudo:

   ```bash
   curl -LsSf https://astral.sh/uv/install.sh | sh   # uv -> ~/.local/bin
   curl -fsSL https://bun.sh/install | bash          # bun -> ~/.bun
   uv python install 3.12                            # Python 3.12 with headers
   rustup update stable                              # rustc 1.88 or newer
   ```

   Open a new terminal afterwards so `uv` and `bun` are on the `PATH`.

3. Clone, apply the patch and set up:

   ```bash
   git clone https://github.com/jamiepine/voicebox.git
   cd voicebox
   git switch -c fix/linux-dev-setup
   git apply ../RamiGspo-lab/voicebox/linux-dev-setup.patch
   just setup
   just dev
   ```

   Without the patch, create the venv by hand before `just setup`:
   `$(uv python find 3.12) -m venv backend/venv`

`just dev` starts:

- the backend on `http://localhost:17493` (`/health` should report `healthy`)
- the Vite frontend on `http://localhost:5173`
- the Tauri desktop window

Closing the window stops the backend as well.
The first `just dev` compiles the Rust app and takes several minutes;
later starts take seconds.

## Problems and fixes

| Problem | Root cause | Fix in the patch |
|---------|------------|------------------|
| `pyopenjtalk` fails: `Python.h` not found | The system `python3.12` is left over from Ubuntu 24.04; 26.04 has no `python3.12-dev` | `setup-python` skips interpreters without headers and uses uv's Python |
| ~2.5 GB of CUDA libraries in the venv | The PyPI torch wheel bundles CUDA | Without an NVIDIA or AMD GPU, install CPU-only torch |
| Cargo fails minutes into the build | rustc too old; the `time` crate needs 1.88 | New `_check-tauri-deps` recipe checks the rustc version first |
| Cargo fails on missing GTK/WebKit | Tauri system libraries not installed | `_check-tauri-deps` checks them with `pkg-config` and prints the `apt` command |
| `unknown print request host-tuple` | Old rustc does not know the option | Silenced in `setup-dev-sidecar.js`; the script already has a fallback |
| Docs did not mention any of this | | `docs/.../developer/setup.mdx` updated |

`tauri/src-tauri/Cargo.lock` also changes after a build (version 0.5.0 -> 0.6.0).
Cargo does that on its own; it is not part of the patch.

### Status of the patch

Tested:

- `just --list` loads the justfile.
- `_check-tauri-deps` fails with the right message for an old rustc
  and for a missing library, and passes on a complete system.
- A fresh `setup-python` skips the system `python3.12`,
  uses uv's Python and picks CPU-only torch.

Still to do:

- Let a fresh `setup-python` run to the end and check that
  `torch`, `pyopenjtalk` and `fastapi` import.
- Commit it in the voicebox clone.
  Do not push to `origin`: it is the upstream repo.

## CPU-only torch on an existing venv

Without a GPU, the CUDA build is never used.
Switching shrinks the venv from 4.8 GB to 1.7 GB:

```bash
backend/venv/bin/pip uninstall -y torch torchaudio triton
backend/venv/bin/pip list | grep -E '^(nvidia|cuda)-' | cut -d' ' -f1 \
  | xargs -r backend/venv/bin/pip uninstall -y
backend/venv/bin/pip install torch torchaudio \
  --index-url https://download.pytorch.org/whl/cpu
```

On a machine with an NVIDIA GPU, keep the CUDA build.

## Models

Each model downloads once per machine, the first time it is used,
into `~/.cache/huggingface/hub/`.
Qwen3-TTS 1.7B is about 4 GB; Whisper base about 260 MB.

To skip the download on another machine, copy the cache:

```bash
rsync -a ~/.cache/huggingface/hub/ other-pc:~/.cache/huggingface/hub/
```

To keep models elsewhere, e.g. on an external disk:

```bash
VOICEBOX_MODELS_DIR=/absolute/path just dev
```

On CPU, Qwen3-TTS 1.7B is slow.
Kokoro (~300 MB) and LuxTTS are much faster for trying things out.

## Audio

If the app plays no sound, WebKit is missing GStreamer plugins:

```bash
sudo apt install gstreamer1.0-plugins-good gstreamer1.0-plugins-bad
```
