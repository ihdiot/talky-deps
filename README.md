# talky-deps

LGPL-rebuilt dependency wheels for **[Talky](https://github.com/ihdiot/talky)**, a fully-offline AI dictation app for Windows & macOS.

## Why this repo exists

Talky uses FFmpeg's audio resampler (`libswresample`) to feed 16 kHz audio to Whisper. The
`av` (PyAV) wheel on PyPI bundles an FFmpeg built with `--enable-gpl` (libx264/libx265),
which is **GPL-3.0** and unsuitable for a closed/paid binary. Talky never touches x264/x265 â€”
only the LGPL resampler â€” so this repo hosts **PyAV rebuilt against an LGPL-only FFmpeg 8.0**
(no libx264/libx265). Talky's installer pulls this wheel via pip's `--find-links`.

## What's published

| Release | Wheel | Details |
|---|---|---|
| [`ffmpeg-lgpl-av-8.0.1`](https://github.com/ihdiot/talky-deps/releases/tag/ffmpeg-lgpl-av-8.0.1) | `av-17.1.0+lgpl-cp311-abi3-win_amd64.whl` | PyAV 17.1.0 Â· LGPL FFmpeg 8.0.1 Â· Windows x64 Â· Python 3.11+ |

The `+lgpl` [PEP 440 local version](https://peps.python.org/pep-0440/#local-version-identifiers)
makes pip prefer this wheel over PyPI's GPL `av==17.1.0`.

### Install

```
pip install "av==17.1.0" --find-links https://github.com/ihdiot/talky-deps/releases/download/ffmpeg-lgpl-av-8.0.1/av-17.1.0%2Blgpl-cp311-abi3-win_amd64.whl
```

## Licenses

Everything here is redistributable; nothing proprietary:

- **PyAV** bindings â€” BSD-3-Clause.
- **Bundled FFmpeg** shared libraries â€” **LGPL-2.1-or-later**, from
  [BtbN/FFmpeg-Builds](https://github.com/BtbN/FFmpeg-Builds) (`win64-lgpl-shared`, FFmpeg 8.0.1).
  FFmpeg source: <https://ffmpeg.org/>. The FFmpeg DLLs ship as separate, replaceable files
  (`av.libs/`), per LGPL dynamic-linking terms.
- **No GPL components, no secrets, no application source.**

## How the wheels are built

By a CI workflow in the Talky project: PyAV is compiled from source against the LGPL FFmpeg,
bundled with `delvewheel`, retagged to a `+lgpl` local version, and gated by a verifier that
asserts there's no libx264/libx265, that `libswresample` is present, and that a 48kâ†’16k
resample works â€” all before anything is published here.
