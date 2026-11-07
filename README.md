# FastSpeech 2 — English and Telugu

This project implements and evaluates FastSpeech 2 for English and Telugu speech synthesis.

## Languages and datasets
- English: ai4bharat/Svarah
- Telugu: SPRINGLab/IndicTTS_Telugu

## Project contents
- `English/`: English preprocessing, configuration, checkpoints, training logs and inference outputs.
- `Telugu/`: Telugu preprocessing, configuration, checkpoints, training logs and inference outputs.
- `Telugu/recovery_training/inference/`: Telugu checkpoint-600 recovery inference outputs.
- `Report/Technical_Report.pdf`: Technical report.
- `requirements.txt`: Python dependencies.

## Generated test sentences
Each language has five test outputs: E1/E2a/E2b/E3/E4 for English and T1/T2a/T2b/T3/T4 for Telugu.

For each test sentence, the project stores generated audio, predictions and duration, pitch and energy visualizations where available.

## Important Telugu training limitation
The Telugu experiment reached checkpoint step 600. An audit found that model trainable parameters and Adam optimizer state did not advance after step 300, although BatchNorm running statistics continued changing. The checkpoint-600 inference outputs were generated successfully, but successful inference does not establish high speech quality. This limitation should be considered when interpreting Telugu results.

## Reproducibility
Use the language-specific configuration files and checkpoints in their respective folders. Dataset preparation and training are computationally intensive; existing prepared features and checkpoints are included where available.

## Results
See `Results/` where present, and the language-specific inference folders for generated audio and variance-adaptor plots.
