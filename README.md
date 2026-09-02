# wbw-voices

Chunked [Piper](https://github.com/rhasspy/piper) TTS voice models for the
[Word By Word](https://github.com/eddydong/WordByWord) language-learning app,
served through jsDelivr.

Word By Word synthesizes spoken audio in the browser using Piper. Its default
path downloads each voice model from Cloudflare R2, but R2 throttles large
downloads for users in some regions — notably mainland China, where a ~63 MB
model can drop to ~15 KB/s. This repository hosts the same models split into
≤16 MB pieces so they can be served from jsDelivr's GitHub CDN, which stays
fast in those regions.

## Layout

- `configs/<model-id>.onnx.json` — Piper voice config files.
- `<model-id>/<index>.bin` — binary chunks of the `<model-id>.onnx` model. Each
  chunk is ≤16 MB, safely under jsDelivr's 20 MB per-file limit for GitHub
  repositories.

The app downloads the chunks, reassembles them in the browser, and verifies
the SHA-256 of the reassembled model against the expected `modelSha256` before
use.

## Where the models come from

All voices are [Piper](https://github.com/rhasspy/piper) TTS models from the
[Rhasspy](https://rhasspy.github.io/) project, originally published in
[rhasspy/piper-voices](https://huggingface.co/rhasspy/piper-voices).

- English voices that already export the `durations` tensor are chunked as-is.
- The remaining voices (Spanish, and several English) have a `durations`
  output added with the patcher in the Word By Word repository
  (`scripts/patch-piper-model.py`), matching the format used by
  [rinaldow/piper-onnx-durations](https://github.com/rinaldow/piper-onnx-durations).

## Regenerating

The chunks are produced by `scripts/chunk-piper-models.js` in the Word By Word
repository: it downloads each model, verifies its SHA-256, splits it into
≤16 MB pieces, and records the chunk list on the model card the app reads.

## License

Piper voice models are distributed by Rhasspy under the
[MIT License](https://huggingface.co/rhasspy/piper-voices) (`license: mit`).
The chunks and configs in this repository are unchanged (or re-exported with
the `durations` tensor) copies of those MIT-licensed files, so they remain
under the same MIT license. See [LICENSE](LICENSE).
