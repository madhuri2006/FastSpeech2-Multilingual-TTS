# Reproducibility Guide

## 1. Project

This repository contains separate English and Telugu FastSpeech 2
experiments. The models, text representations, preprocessing settings,
checkpoints and inference outputs are language-specific.

Repository:
https://github.com/madhuri2006/FastSpeech2-Multilingual-TTS

## 2. Original development environment

The experiments were developed in Google Colab.

Recorded environment:
- Python 3.13.16
- PyTorch 2.11.0+cu130
- NumPy 2.4.6
- librosa 0.11.0
- pyworld 0.3.5

CUDA availability and hardware depend on the runtime. The recorded
inference recovery session also used CPU execution.

These are recorded environment details, not a guarantee that every
dependency will install unchanged on every operating system.

## 3. Installation

Clone the repository:

```bash
git clone https://github.com/madhuri2006/FastSpeech2-Multilingual-TTS.git
cd FastSpeech2-Multilingual-TTS
```

Create a clean Python environment compatible with the installed
PyTorch and audio dependencies.

Install the listed dependencies:

```bash
pip install -r requirements.txt
```

If a dependency fails to install, resolve the compatibility issue
for the local Python and CUDA versions before running the model.

## 4. Datasets

The project uses the following datasets. They are not replaced by
other corpora.

### English

AI4Bharat Svarah:

https://huggingface.co/datasets/ai4bharat/Svarah

Recorded selection:
- Training: approximately 5.001 hours
- Validation: approximately 1.002 hours
- Prepared training features: 2,662
- Prepared validation features: 501

The English experiment uses an IPA-based vocabulary.

### Telugu

SPRING Lab IndicTTS Telugu:

https://huggingface.co/datasets/SPRINGLab/IndicTTS_Telugu

Recorded selection:
- Training: approximately 5.012 hours
- Validation: approximately 1.003 hours
- Selected training samples: 1,281
- Selected validation samples: 251
- Prepared training features: 1,280
- Prepared validation features: 251

The Telugu experiment uses Uroman-based romanization and a
character-level token representation.

Raw datasets may need to be downloaded separately. Do not assume
that the public Git repository contains the original raw recordings.

## 5. Preprocessing

Use the language-specific preprocessing configurations under:

- `English/config/`
- `Telugu/config/`

The experiments use:
- Sampling rate: 22,050 Hz
- FFT size: 1,024
- Hop length: 256
- Mel channels: 80

English uses IPA-based text and Montreal Forced Aligner alignments.

Telugu uses Uroman romanization and multilingual MMS forced
alignment. Word-level alignment durations were distributed across
romanized characters to construct duration targets.

The prepared feature files and statistics are required for
checkpoint-compatible inference. If they are not present in a
fresh clone, reproduce preprocessing using the original datasets
and the language-specific configurations.

## 6. Model configurations

The language-specific model and training configurations are stored
under their respective experiment folders.

The core architecture uses:
- Transformer encoder and decoder
- Duration predictor
- Length regulator
- Pitch predictor and embedding
- Energy predictor and embedding
- Mel decoder and postnet
- HiFi-GAN vocoder

Do not interchange English and Telugu vocabularies, statistics,
speaker mappings, checkpoints or preprocessing configurations.

## 7. Checkpoints

### English

The best saved English checkpoint is:

`English/checkpoints/best_checkpoint.pth.tar`

Recorded best checkpoint step: 5,000.

### Telugu

The final recovery checkpoint is:

`Telugu/recovery_training/checkpoints/recovery_600.pth.tar`

It was initialized from the original step-300 model weights and
trained with a fresh AdamW optimizer through recovery step 600.

The Telugu corrected validation audit is:

`Telugu/recovery_training/logs/corrected_validation_audit.json`

The reported corrected values were measured on the same 24
validation samples for each checkpoint. They are not full
validation-set metrics.

## 8. Inference

The final generated audio and prediction artifacts are stored in:

- `Results/English/`
- `Results/Telugu/`

Each language has five test cases.

For each test case, the saved artifacts include:
- WAV audio
- Duration prediction plot
- Pitch prediction plot
- Energy prediction plot
- Numerical prediction data

The inference pipeline uses FastSpeech 2 to predict mel
representations and HiFi-GAN to synthesize waveforms.

Use the matching language-specific checkpoint, vocabulary,
normalization statistics and vocoder configuration.

The exact inference entry points and runtime-specific paths should
be checked in the source files and notebook before execution.
Some original working paths refer to Google Drive or Colab and
may need to be changed on a different machine.

## 9. Experiment summaries

English:
- `English/logs/english_training_history.csv`
- `English/inference/english_inference_summary.json`

Telugu:
- `Telugu/recovery_training/logs/training_history.json`
- `Telugu/recovery_training/logs/corrected_validation_audit.json`
- `Results/Telugu/telugu_final_inference_summary.json`

Reports:
- `Report/Technical_Report.pdf`
- `Report/Telugu_Recovery_Addendum.pdf`

## 10. Important reproducibility limitations

1. The raw datasets may need separate download and preparation.
2. Preprocessed features and alignments may not all be included in
   a fresh clone.
3. Some scripts contain paths originally configured for Google
   Colab and Google Drive.
4. Python, PyTorch, CUDA and audio-library versions may require
   compatibility adjustments.
5. Telugu input is romanized character-level text, not a native
   Telugu phoneme vocabulary.
6. Telugu character durations were derived from word-level
   alignment intervals.
7. The corrected Telugu validation audit uses a fixed subset of
   24 validation examples.
8. No formal Mean Opinion Score (MOS) or human listening study is
   claimed in this repository.

A fresh clone should therefore be treated as the starting point
for reproducing the documented pipeline, not as proof that all
training can run without downloading data and configuring paths.

## 11. Suggested verification order

1. Clone the repository.
2. Inspect `requirements.txt` and install compatible dependencies.
3. Inspect the English and Telugu configuration files.
4. Obtain the original datasets.
5. Recreate or verify preprocessing and alignment artifacts.
6. Verify vocabulary and normalization statistics.
7. Load the corresponding checkpoint.
8. Run inference on one test sentence.
9. Verify the generated WAV and prediction plots.
10. Run the complete five-sentence inference evaluation.

Do not start full training until the dataset splits, preprocessing,
vocabulary, model configuration and checkpoint paths have been
verified.
