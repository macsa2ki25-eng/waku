# English pronunciation clips

The 177 bundled word and phrase clips use **Kokoro v0.19 neural speech synthesis**, the `af`
US English female voice, through **sherpa-onnx 1.13.8**. They replace the old
FFmpeg/Flite `slt` clips. These are machine-generated voices, not human recordings.
The synthesis model, speech library and phonemizer are not shipped to browsers.
Playing the bundled MP3s needs no remote speech service, API key or browser voice.

Source vocabulary: `AUDIO_VOCABULARY` in `src/game.ts`, including all six stages
and the video-inspired listening course. File names use stable vocabulary IDs
(single words or hyphenated IDs for phrases). Apostrophes and spaces are preserved
in the spoken English text; `tnt.mp3` pronounces the letter names “T N T”.
The model speaks at speed `0.95`, without pitch-changing audio processing.
FFmpeg normalizes each clip to approximately **−19 LUFS**, matching the previous
comfortable volume, with a −2 dBTP ceiling, a 40 ms opening buffer and a 150 ms
ending buffer. Output: mono 24 kHz MP3 at 96 kbps.

`manifest.json` records the source, model/voice/checksums, synthesis settings,
spoken text and decoded duration, level and SHA-256 for every clip. The generator
reuses a clip only when the engine, model, voice, synthesis settings, English
text and SHA-256 all match the manifest. It decodes both reused and new clips
to check usable speech, duration and clipping before replacing files. New
clips are staged on the output filesystem, so a generation failure leaves
existing clips intact. These checks do not replace listening by a person.

## Optional regeneration

Normal development uses the bundled clips. Regeneration needs Python 3.12,
Node.js 24, FFmpeg with libmp3lame and about 700 MB for the model and its archive.
Use existing dependency/model caches if present. In the cloud workspace:

```sh
set -eu
mkdir -p /workspace/.tools/yokai-tts /workspace/.cache/pip /workspace/.cache/yokai-tts
python3 -m pip install --cache-dir /workspace/.cache/pip --target /workspace/.tools/yokai-tts 'sherpa-onnx==1.13.8' 'sherpa-onnx-core==1.13.8'
curl --fail --location --silent --show-error 'https://github.com/k2-fsa/sherpa-onnx/releases/download/tts-models/kokoro-en-v0_19.tar.bz2' --output /workspace/.cache/yokai-tts/kokoro-en-v0_19.tar.bz2
echo '912804855a04745fa77a30be545b3f9a5d15c4d66db00b88cbcd4921df605ac7  /workspace/.cache/yokai-tts/kokoro-en-v0_19.tar.bz2' | sha256sum --check --status
tar -xjf /workspace/.cache/yokai-tts/kokoro-en-v0_19.tar.bz2 -C /workspace/.cache/yokai-tts
python3 scripts/generate_audio.py
```

The archive checksum was recorded from the official GitHub release downloaded
with normal HTTPS/TLS verification on 2026-10-11. It is a reproducibility pin,
not a claimed upstream signature or published upstream checksum. The generator
also rejects model/voice files whose SHA-256 no longer matches that archive.
Do not disable TLS or alter expected checksums to get a changed download to run.
Override `--model-dir`, `--dependencies-dir` and `--output-dir` for other machines.
Run normally after adding vocabulary to generate only missing or changed clips;
pass `--force` to intentionally regenerate every clip.
Each cloud task is isolated: use the existing checkout, without a new worktree.

## Sources and licenses

- Model: [Kokoro](https://huggingface.co/hexgrad/Kokoro-82M), Apache-2.0.
- Verified distribution: [sherpa-onnx official TTS models release](https://github.com/k2-fsa/sherpa-onnx/releases/tag/tts-models).
- Synthesis runtime: [sherpa-onnx](https://github.com/k2-fsa/sherpa-onnx), Apache-2.0.
- English phonemes: [eSpeak NG](https://github.com/espeak-ng/espeak-ng), GPL-3.0; used by the generation tool only and not included in the web app.
- Encoding and loudness adjustment: [FFmpeg](https://ffmpeg.org/); not included in the web app.

The original model license is reproduced in `KOKORO-LICENSE.txt` and the speech
runtime license in `SHERPA-ONNX-LICENSE.txt`. No third-party model or runtime
binary is redistributed by this application.
