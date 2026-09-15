# Experimental Results

This directory contains the evaluation results produced during the metadata-based and visual classification experiments.

## Metadata Pipeline

The metadata pipeline was developed as an experimental baseline using YouTube video metadata and TF-IDF-based textual features.

The baseline experiments include classifier comparisons and evaluation of classification performance across selected target labels.

## Visual Pipeline

The visual pipeline evaluates representative video keyframes extracted through shot segmentation and visual embeddings generated using pretrained CNN/vision-transformer backbones.

The main visual experiments compare:

- DINOv2-Small
- MobileNetV3-Small

Separate classifiers are trained for:

- Production
- Source Type
- Primary Language
- Geography
- Content Format

## Evaluation

The visual pipeline uses stratified cross-validation and reports:

- Accuracy
- Weighted Precision
- Weighted Recall
- Weighted F1-score

Additional plots and comparative tables are provided in the corresponding subdirectories.

> Note: The results correspond to the experiments documented in the project notebooks. They are provided as research artifacts and are not intended to represent production-level performance.
