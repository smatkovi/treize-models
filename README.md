# treize-models

The language model that [Treize](https://github.com/smatkovi/treize), a
French-learning app for one learner, downloads on first use. The app reads
`manifest.json`, picks the best-ranked model whose memory need the phone
meets and whose size fits, fetches the parts from the release, checks each
against its SHA-256 and keeps one model on the device. Nothing else ever
leaves the phone: the model runs locally through llama.cpp.

## Models

| Name | Parts | Size | Needs | Licence |
| --- | --- | ---: | --- | --- |
| Gemma 4 E2B it, QAT, Unsloth Dynamic Q4_K_XL | 2 | 2.62 GB | 6 GB phone (MemTotal ≥ 5.4 GB), ARMv8.2 with dotprod and fp16 | Apache-2.0 |

The parts were made with `llama-gguf-split --split-max-size 1900M` and are
byte-for-byte what Unsloth published; llama.cpp opens the first part and
finds the second. To get the original single file back:

    llama-gguf-split --merge gemma-4-E2B-it-qat-UD-Q4_K_XL-00001-of-00002.gguf gemma-4-E2B-it-qat-UD-Q4_K_XL.gguf

| French phonemizer, wav2vec2 base, int8 (release `ear-v1`) | 2 files | 91 MB | any phone | MIT |

| French voice fr_FR-siwis-medium for Piper (release `voice-v1`) | 2 files | 60 MB | any phone | CC-BY 4.0 |

The second row is "the ear": the phoneme recogniser behind the pronunciation
check, listed under `ears` in the manifest; the third is "the voice", the
Piper model that reads the sentences, under `voices`. Both are fetched the
same way as the language model.

## manifest.json

```
models[]:
  name        file stem on the device (first part: <name>-00001-of-0000N.gguf)
  rank        1 is best; the app takes the lowest rank it can afford
  min_ram_mb  MemTotal the phone must report
  cpu         instruction-set features the build on the phone expects
  size        total bytes; sizes[] and sha256s[] per part, sha256 of the merged file
  urls        one per part, in order
  template    chat template family; thinking_token is kept out of the output
```

Why this model: measured on a Snapdragon 695 (two Cortex-A78 like the
Galaxy A25), it writes about ten words per second with a 2.9 GB peak and
produced the cleanest simple French of the candidates tried; see
`docs/model-bench.md` in the app repository.

See `NOTICE` for the provenance of the weights and `LICENSE` for the
Apache License, Version 2.0.
