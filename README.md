# FastSpeech 2: Multilingual Text-to-Speech (English & Telugu)

A multilingual Text-to-Speech (TTS) implementation based on **FastSpeech 2**, developed as an academic assignment. The project trains and evaluates separate models for English and Telugu, generates speech from test sentences, and analyzes the model's duration, pitch, and energy predictions.

The repository contains preprocessing artifacts, model configurations, checkpoints, training logs, generated audio, prediction data, visualizations, and technical documentation.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Objectives](#objectives)
- [Model Architecture](#model-architecture)
- [Datasets](#datasets)
- [Data Preparation](#data-preparation)
- [Training Experiments](#training-experiments)
- [Inference and Evaluation](#inference-and-evaluation)
- [Results](#results)
- [Repository Structure](#repository-structure)
- [Environment and Setup](#environment-and-setup)
- [Reproducibility](#reproducibility)
- [Limitations and Notes](#limitations-and-notes)
- [References](#references)

---

## Project Overview

FastSpeech 2 is a non-autoregressive neural TTS architecture that generates speech using intermediate representations such as phoneme or text embeddings, duration, pitch, and energy.

This project applies the architecture independently to two languages:

| Language | Dataset | Model |
|---|---|---|
| English | AI4Bharat Svarah | English FastSpeech 2 |
| Telugu | SPRING Lab IndicTTS Telugu | Telugu FastSpeech 2 |

The two experiments use separate preprocessing pipelines, vocabulary representations, configurations, checkpoints, and inference outputs.

A HiFi-GAN vocoder is used to convert predicted mel-spectrograms into audio waveforms.

## Objectives

1. Prepare English and Telugu speech datasets for FastSpeech 2.
2. Build language-specific preprocessing and vocabulary pipelines.
3. Train separate FastSpeech 2 models.
4. Generate speech for five test cases per language.
5. Examine predicted duration, pitch, and energy.
6. Visualize training and validation behavior.
7. Organize checkpoints, outputs, and documentation for reproducibility.

## Model Architecture

The implementation follows the FastSpeech 2 architecture.

**Main components:**

1. **Text Encoder** — transforms input token sequences into hidden representations.
2. **Duration Predictor** — predicts phoneme/token durations.
3. **Length Regulator** — expands encoder representations according to predicted durations.
4. **Pitch Predictor and Embedding** — models pitch-related information.
5. **Energy Predictor and Embedding** — models energy-related information.
6. **Mel Decoder** — predicts mel-spectrogram frames.
7. **Postnet** — refines the predicted mel-spectrogram.
8. **HiFi-GAN Vocoder** — converts mel-spectrograms into audio.

### Model configuration

The principal transformer configuration uses:

| Parameter | Configuration |
|---|---:|
| Encoder layers | 2 |
| Decoder layers | 2 |
| Attention heads | 2 |
| Hidden dimension | 128 |
| Convolution filter size | 512 |
| Variance predictor filter size | 128 |
| Variance predictor kernel size | 3 |
| Variance embedding bins | 256 |
| Mel channels | 80 |
| Sampling rate | 22,050 Hz |
| FFT size | 1,024 |
| Hop length | 256 |

Language-specific configurations and vocabulary sizes are maintained separately.

## Datasets

### English — AI4Bharat Svarah

Dataset: [`ai4bharat/Svarah`](https://huggingface.co/datasets/ai4bharat/Svarah)

The English experiment uses an IPA-based text representation.

| Split | Feature samples | Approximate duration |
|---|---:|---:|
| Training | 2,662 | 5.001 hours |
| Validation | 501 | 1.002 hours |

The English preprocessing pipeline uses a 67-entry vocabulary and phoneme-level duration, pitch, and energy features.

The saved dataset-readiness audit records zero overlap between the training and validation IDs.

### Telugu — SPRING Lab IndicTTS

Dataset: [`SPRINGLab/IndicTTS_Telugu`](https://huggingface.co/datasets/SPRINGLab/IndicTTS_Telugu)

The Telugu experiment uses a romanized character-level representation.

| Split | Selected samples | Approximate duration |
|---|---:|---:|
| Training | 1,281 | 5.012 hours |
| Validation | 251 | 1.003 hours |

The prepared feature set contains 1,280 training samples and 251 validation samples.

The Telugu vocabulary contains 361 entries. Speaker IDs are maintained for the two speaker categories in the prepared dataset.

## Data Preparation

### English pipeline

- Select approximately five hours of training audio and one hour of validation audio.
- Prepare IPA-based text representations.
- Use Montreal Forced Aligner-generated alignments.
- Extract mel-spectrogram, duration, pitch, and energy features.
- Save normalized statistics and vocabulary information.
- Verify alignment coverage against the prepared feature IDs.

The saved English alignment audit reports complete alignment coverage for the prepared training and validation feature IDs.

### Telugu pipeline

- Select approximately five hours of training audio and one hour of validation audio.
- Use Uroman for text romanization.
- Use the multilingual MMS forced-alignment pipeline.
- Extract mel-spectrogram, pitch, and energy features.
- Prepare token-level duration targets from word-level alignment intervals.
- Organize long samples into chunks at word boundaries.
- Preserve speaker IDs and normalization statistics.

**Important:** Telugu duration targets were constructed by distributing word-level alignment durations across romanized characters. They should not be interpreted as independently aligned Telugu phoneme durations.

## Training Experiments

### English

The English experiment uses the saved IPA-based preprocessing configuration and a 67-entry vocabulary.

The best saved checkpoint is at step 5,000.

| Metric | Best checkpoint |
|---|---:|
| Training total loss | 3.497280 |
| Validation total loss | 3.629413 |
| Validation mel loss | 0.929251 |
| Validation postnet mel loss | 0.929641 |
| Validation pitch loss | 0.876415 |
| Validation energy loss | 0.607043 |
| Validation duration loss | 0.287063 |

The latest saved checkpoint is at step 6,000. The best validation checkpoint is retained separately.

### Telugu

The Telugu experiment includes an initial training phase and a recovery phase.

The recovery phase starts from the original step-300 model weights and uses a fresh AdamW optimizer. Parameter updates were explicitly verified.

The final recovery checkpoint is at step 600.

#### Corrected validation audit

The following total losses were recomputed using the same 24 validation samples for each checkpoint. The total loss is taken directly from the model's total-loss output, rather than incorrectly summing the total and its individual components.

| Recovery checkpoint | Corrected validation total loss |
|---|---:|
| Step 400 | 4.646829 |
| Step 500 | 4.301743 |
| Step 600 | 3.483655 |

The model parameters were verified to change between these recovery checkpoints.

**Validation scope:** These corrected values are a fixed 24-sample validation audit. They are not full-validation-set metrics.

## Inference and Evaluation

Five test cases were generated for each language.

### English test cases

- E1
- E2(a)
- E2(b)
- E3
- E4

### Telugu test cases

- T1
- T2(a)
- T2(b)
- T3
- T4

For every test sentence, the inference pipeline saves:

- Generated WAV audio.
- Predicted duration values and visualization.
- Predicted pitch values and visualization.
- Predicted energy values and visualization.
- Numerical prediction files.

The generated mel-spectrograms are passed to the HiFi-GAN vocoder to produce speech waveforms.

## Results

The repository contains the following generated outputs:

| Deliverable | English | Telugu | Total |
|---|---:|---:|---:|
| Generated audio files | 5 | 5 | 10 |
| Duration plots | 5 | 5 | 10 |
| Pitch plots | 5 | 5 | 10 |
| Energy plots | 5 | 5 | 10 |
| Prediction files | 5 | 5 | 10 |

### Where to find the outputs

- `Results/English/test_generated_audios/`
- `Results/Telugu/test_generated_audios/`
- `Results/English/variance_adaptor_plots/`
- `Results/Telugu/variance_adaptor_plots/`
- `Results/English/predictions/`
- `Results/Telugu/predictions/`

Training histories, loss visualizations, inference summaries, and model checkpoints are also included in their respective experiment directories.

## Repository Structure

```text
FastSpeech2-Multilingual-TTS/
│
├── Code/
│   └── FastSpeech2/
│       └── FastSpeech 2 implementation
│
├── English/
│   ├── config/
│   ├── processed/
│   ├── alignment/
│   ├── checkpoints/
│   ├── logs/
│   └── inference/
│
├── Telugu/
│   ├── config/
│   ├── fs2_prepared/
│   └── recovery_training/
│       ├── checkpoints/
│       ├── logs/
│       └── results/
│
├── Results/
│   ├── English/
│   │   ├── test_generated_audios/
│   │   ├── variance_adaptor_plots/
│   │   └── predictions/
│   │
│   └── Telugu/
│       ├── test_generated_audios/
│       ├── variance_adaptor_plots/
│       └── predictions/
│
├── Report/
│   ├── Technical_Report.pdf
│   └── Telugu_Recovery_Addendum.pdf
│
├── README.md
└── requirements.txt
```

Some local working files and raw datasets may not be included in the public repository.

## Environment and Setup

The experiments were developed primarily in Google Colab.

The saved environment used Python 3.13, PyTorch 2.11, NumPy 2.x, librosa, and pyworld. Exact compatibility may depend on the installed CUDA runtime and package versions.

### 1. Clone the repository

```bash
git clone https://github.com/madhuri2006/FastSpeech2-Multilingual-TTS.git
cd FastSpeech2-Multilingual-TTS
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Prepare datasets

Download the required datasets:

- English: `ai4bharat/Svarah`
- Telugu: `SPRINGLab/IndicTTS_Telugu`

Follow the language-specific preprocessing configurations and preserve the corresponding train/validation splits.

### 4. Check experiment artifacts

Review the language-specific configuration, processed feature files, training history, and checkpoints before running training or inference.

The saved checkpoints and preprocessing artifacts are provided for inspection and reproducibility. Paths and runtime-specific settings may need adjustment when moving from the original Colab environment.

## Reproducibility

For each language, the repository maintains separate:

- Preprocessing configuration.
- Model configuration.
- Training configuration.
- Vocabulary and normalization statistics.
- Checkpoints.
- Training history.
- Inference outputs.

The Telugu recovery validation audit is stored at:

```text
Telugu/recovery_training/logs/corrected_validation_audit.json
```

The final Telugu inference summary is stored at:

```text
Results/Telugu/telugu_final_inference_summary.json
```

The English inference summary is stored at:

```text
Results/English/inference/english_inference_summary.json
```

Refer to the technical report and recovery addendum for further experimental details.

## Limitations and Notes

- English and Telugu use different text representations and preprocessing pipelines.
- Telugu uses romanized character-level input rather than a native Telugu phoneme vocabulary.
- Telugu character-level duration targets are derived from word-level alignment intervals.
- The corrected Telugu validation figures represent a fixed 24-sample audit, not a complete validation evaluation.
- The recovery experiment's final recorded step is 600; the configured training target should not be confused with the number of completed updates.
- Generated audio and prediction plots demonstrate the inference pipeline, but they do not establish human-perceived speech quality. A formal MOS or listening evaluation has not been reported here.
- Checkpoint files are large and may trigger GitHub's recommended file-size warnings.

## References

1. Ren et al., **FastSpeech 2: Fast and High-Quality End-to-End Text to Speech**, ICLR 2021.
2. AI4Bharat — [Svarah dataset](https://huggingface.co/datasets/ai4bharat/Svarah).
3. SPRING Lab — [IndicTTS Telugu dataset](https://huggingface.co/datasets/SPRINGLab/IndicTTS_Telugu).
4. Kong et al., **HiFi-GAN: Generative Adversarial Networks for Efficient and High Fidelity Speech Synthesis**, NeurIPS 2020.

---

## Author

Developed as a multilingual Text-to-Speech academic project.

**GitHub:** [madhuri2006](https://github.com/madhuri2006)

**Repository:** [FastSpeech2-Multilingual-TTS](https://github.com/madhuri2006/FastSpeech2-Multilingual-TTS)
