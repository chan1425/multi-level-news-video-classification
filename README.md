# Metadata-Assisted Multi-Label Classification of News Videos

A research internship project exploring the classification of news videos across multiple semantic and production-level attributes using complementary metadata-based and visual approaches.

![Project](https://img.shields.io/badge/Project-Research%20Internship-blue)
![Python](https://img.shields.io/badge/Python-3.x-yellow)
![Framework](https://img.shields.io/badge/Framework-PyTorch-orange)
![Status](https://img.shields.io/badge/Status-Completed-success)

---

## Overview

News videos contain multiple dimensions of information that cannot be represented effectively by a single category.

This project investigates a multi-label classification framework for analysing news videos across five target attributes:

1. **Production**
2. **Source Type**
3. **Primary Language**
4. **Geography**
5. **Content Format**

The work consists of two intentionally separate approaches:

- **Metadata Pipeline** — an experimental baseline using YouTube metadata and TF-IDF-based textual features.
- **Visual Pipeline** — the main/final approach using shot segmentation, representative keyframes and pretrained visual embeddings.

The project began with a metadata-based proof of concept and progressed toward an end-to-end visual classification pipeline for unseen videos.

---

## Project Objective

The objective is to investigate how different information sources can be used to classify news videos across multiple semantic levels.

The metadata pipeline explores information available through the YouTube Data API, while the visual pipeline analyses representative video frames using pretrained computer-vision models.

A future extension can combine metadata, visual, audio/transcript and OCR information into a unified multimodal framework.

---

## Classification Targets

| Target | Description |
|---|---|
| **Production** | Studio Produced, Field Report, Archive Footage |
| **Source Type** | Mainstream Media, Independent Creator, Citizen Journalism |
| **Primary Language** | English, Hindi, Bengali |
| **Geography** | India – National, Global, South Asia – Regional, Bangladesh |
| **Content Format** | 13 annotated news-video formats |

The Content Format categories include:

- Explainer
- News Bulletin
- Interview
- Press Conference
- Breaking News
- Fact Check
- Analysis
- Panel Discussion
- Documentary
- Debate
- Podcast Clip
- Reaction Video
- Vlog

---

# Dataset

The project uses a manually annotated dataset of **150 YouTube news videos**.

Each record contains:

- YouTube video URL
- Production label
- Source Type label
- Primary Language label
- Geography label
- Content Format label

The actual video files are **not included** in this repository.

The visual feature pipeline contains **146 usable video feature records** after video preprocessing and feature extraction.

### Dataset Distribution

| Target | Class | Count |
|---|---|---:|
| Source Type | Mainstream Media | 112 |
| Source Type | Independent Creator | 32 |
| Source Type | Citizen Journalism | 6 |
| Primary Language | English | 104 |
| Primary Language | Hindi | 43 |
| Primary Language | Bengali | 3 |
| Geography | India – National | 76 |
| Geography | Global | 62 |
| Geography | South Asia – Regional | 8 |
| Geography | Bangladesh | 4 |
| Production | Studio Produced | 95 |
| Production | Field Report | 40 |
| Production | Archive Footage | 15 |

---

# Project Architecture

The project uses two independent pipelines.

## 1. Metadata Pipeline — Experimental Baseline

```text
YouTube URL
     ↓
YouTube Data API v3
     ↓
Metadata / Textual Information
     ↓
Text Preprocessing
     ↓
TF-IDF Features
     ↓
Classifier Comparison
     ↓
Predictions + Evaluation
```

The metadata pipeline was developed as an experimental baseline to investigate whether information available from YouTube could provide useful classification signals.

### Metadata Features

The pipeline works with textual and metadata information such as:

- Video title
- Description
- Tags
- Channel-related information
- Other available textual metadata

Textual information is transformed into numerical representations using **TF-IDF**.

### Classifiers Compared

The following classical machine-learning models were evaluated:

- Logistic Regression
- Linear SVM
- Multinomial Naive Bayes
- Random Forest
- Decision Tree

### Metadata Baseline Results

The initial metadata baseline achieved:

- **Test Accuracy:** 60%
- **Macro F1-score:** 0.44
- **Weighted F1-score:** 0.53

The expanded metadata experiments focused particularly on Geography.

- **Cross-validation Accuracy:** 58%
- **Test Accuracy:** 60%

The metadata pipeline therefore serves as an experimental baseline rather than the final classification system.

---

## 2. Visual Pipeline — Main Approach

The visual pipeline operates on locally stored MP4 videos.

```text
Local MP4
     ↓
Shot Segmentation
     ↓
Representative Middle Keyframes
     ↓
Visual Feature Extraction
     ↓
Mean Pooling
     ↓
Video-level Representation
     ↓
Random Forest Classifier
     ↓
Five Target Predictions
```

The visual pipeline is the main/final classification approach of the project.

---

# Shot Segmentation

Shot boundaries are detected using **Bhattacharyya distance** between frame histograms.

The main configuration used for segmentation includes:

- Histogram bins: 16
- Frame sampling interval: 28
- Segmentation threshold: 0.5

The representative frame from each detected shot is selected from approximately the middle of that shot.

```text
k_shot = floor((f_start + f_end) / 2)
```

This reduces the number of frames that need to be processed while retaining representative visual information from different portions of the video.

---

# Visual Feature Extraction

Two pretrained visual backbones were compared:

## DINOv2-Small

- Architecture: ViT-S/14
- Feature dimension: **384**
- Parameters: approximately **22.06M**
- Computational complexity: approximately **5.53 GFLOPs**
- Measured inference time: approximately **6.58 ms/image**

## MobileNetV3-Small

- Feature dimension: **576**
- Parameters: approximately **0.93M**
- Computational complexity: approximately **0.06 GFLOPs**
- Measured inference time: approximately **4.95 ms/image**

### Backbone Selection

DINOv2-Small was selected as the main visual backbone because it provided stronger overall performance across several target attributes.

MobileNetV3-Small remained an important comparison because it is substantially lighter and faster and achieved a slightly higher weighted F1-score for Source Type.

---

# Video Representation

For a video containing `K` representative keyframes, the extracted frame-level embeddings are aggregated using mean pooling.

```text
z_video = (1/K) Σ z_k
```

For DINOv2-Small:

```text
z_k ∈ R^384
```

Therefore, each processed video is represented by a **384-dimensional video-level visual feature vector**.

---

# Classification

A separate classifier is trained for each of the five target attributes:

- Production
- Source Type
- Primary Language
- Geography
- Content Format

The visual experiments use:

- **Random Forest**
- **200 estimators**
- **2-fold StratifiedKFold cross-validation**

### Evaluation Metrics

The following metrics are reported:

- Accuracy
- Weighted Precision
- Weighted Recall
- Weighted F1-score

---

# Visual Results

| Target | Backbone | Accuracy | Weighted Precision | Weighted Recall | Weighted F1 |
|---|---|---:|---:|---:|---:|
| Production | DINOv2-Small | 70.55% | 67.12% | 70.55% | 63.53% |
| Production | MobileNetV3-Small | 65.75% | 59.67% | 65.75% | 55.05% |
| Source Type | DINOv2-Small | 76.71% | 78.84% | 76.71% | 68.31% |
| Source Type | MobileNetV3-Small | 76.71% | 75.03% | 76.71% | 69.99% |
| Primary Language | DINOv2-Small | 76.71% | 78.59% | 76.71% | 70.94% |
| Primary Language | MobileNetV3-Small | 76.03% | 80.10% | 76.03% | 69.73% |
| Geography | DINOv2-Small | 69.18% | 64.19% | 69.18% | 66.15% |
| Geography | MobileNetV3-Small | 67.12% | 61.74% | 67.12% | 64.16% |
| Content Format | DINOv2-Small | 24.66% | 24.58% | 24.66% | 20.43% |
| Content Format | MobileNetV3-Small | 24.66% | 18.23% | 24.66% | 18.62% |

---

# Results Interpretation

The visual experiments show that DINOv2-Small provides stronger overall results for several target attributes.

### Production

DINOv2-Small achieved a higher accuracy and weighted F1-score than MobileNetV3-Small.

### Source Type

Both backbones achieved the same accuracy of 76.71%.

MobileNetV3-Small achieved a slightly higher weighted F1-score:

- DINOv2-Small: 68.31%
- MobileNetV3-Small: 69.99%

### Primary Language

DINOv2-Small achieved:

- Accuracy: 76.71%
- Weighted F1: 70.94%

MobileNetV3-Small achieved:

- Accuracy: 76.03%
- Weighted F1: 69.73%

### Geography

DINOv2-Small achieved:

- Accuracy: 69.18%
- Weighted F1: 66.15%

MobileNetV3-Small achieved:

- Accuracy: 67.12%
- Weighted F1: 64.16%

### Content Format

Content Format is substantially more difficult for both visual backbones.

DINOv2-Small achieved a weighted F1-score of 20.43%, while MobileNetV3-Small achieved 18.62%.

This target contains multiple fine-grained classes with an imbalanced distribution, making the classification task considerably more challenging.

---

# Unseen Video Inference

An end-to-end inference pipeline was developed for videos that were not part of the training dataset.

```text
Unseen Video
     ↓
Video Loading
     ↓
Shot Segmentation
     ↓
Representative Keyframes
     ↓
DINOv2 Feature Extraction
     ↓
Mean-Pooled 384-D Representation
     ↓
Five Trained Classifiers
     ↓
Predictions
```

The complete pipeline integrates:

1. Video loading
2. Shot segmentation
3. Representative keyframe extraction
4. DINOv2 feature extraction
5. Mean pooling
6. Classification
7. Prediction output

The inference pipeline was tested on unseen news videos to verify that the complete workflow operates independently of the training dataset.

---

# Repository Structure

```text
multi-level-news-video-classification/
│
├── README.md
├── index.html
├── requirements.txt
│
├── assets/
│
├── data/
│   ├── README.md
│   └── NewsVideoDataset.xlsx
│
├── docs/
│
├── notebooks/
│   ├── metadata_pipeline.ipynb
│   └── visual_pipeline.ipynb
│
├── results/
│   ├── README.md
│   ├── metadata/
│   │   └── metadata_results.md
│   └── visual/
│       └── visual_results.md
│
└── src/
    └── video_segmentor.py
```

---

# Technologies

## Programming & Data

- Python
- Pandas
- NumPy
- scikit-learn
- OpenCV
- Pillow

## Deep Learning

- PyTorch
- torchvision
- DINOv2
- MobileNetV3

## Experimentation

- Google Colab
- CUDA / GPU
- fvcore

## Metadata

- YouTube Data API v3
- TF-IDF

---

# Project Workflow

The project progressed through the following stages:

```text
Dataset Annotation
        ↓
Metadata Proof of Concept
        ↓
Metadata Classifier Comparison
        ↓
Dataset Expansion
        ↓
Video Preprocessing
        ↓
Shot Segmentation
        ↓
Representative Keyframes
        ↓
DINOv2 / MobileNetV3 Comparison
        ↓
Master Visual Feature Dataset
        ↓
Classifier Training
        ↓
Evaluation
        ↓
Unseen Video Inference
```

---

# Setup

Clone the repository:

```bash
git clone https://github.com/chan1425/multi-level-news-video-classification.git
cd multi-level-news-video-classification
```

Install the required Python packages:

```bash
pip install -r requirements.txt
```

The notebooks are designed to be executed in **Google Colab**.

For the metadata pipeline, the YouTube API credential should be supplied through a secure secret or environment mechanism.

**API keys and other credentials must not be committed to the repository.**

---

# Notebooks

## Metadata Pipeline

`notebooks/metadata_pipeline.ipynb`

The metadata notebook contains the experimental metadata-based classification workflow, including:

- YouTube metadata extraction
- Metadata preprocessing
- Text preprocessing
- TF-IDF feature generation
- Classifier comparison
- Cross-validation
- Evaluation
- Prediction analysis

---

## Visual Pipeline

`notebooks/visual_pipeline.ipynb`

The visual notebook contains the main visual classification workflow, including:

- Video preprocessing
- Shot segmentation
- Representative keyframe extraction
- DINOv2 feature extraction
- MobileNetV3 comparison
- Feature aggregation
- Master feature dataset construction
- Classifier training
- Evaluation
- Unseen-video inference

---

# Results Directory

The `results/` directory contains summarized experimental results.

```text
results/
│
├── README.md
│
├── metadata/
│   └── metadata_results.md
│
└── visual/
    └── visual_results.md
```

The results are provided as research artifacts corresponding to the experiments documented in the project notebooks.

---

# Source Code

Reusable video-processing code is provided under:

```text
src/video_segmentor.py
```

This module contains the video segmentation functionality used by the visual processing pipeline.

---

# Limitations

The current system has several limitations:

1. The annotated dataset is relatively small.
2. Several target classes are imbalanced.
3. Content Format contains many fine-grained categories.
4. The visual-only pipeline cannot directly understand spoken language or transcripts.
5. Primary Language classification may therefore be limited when visual evidence alone is insufficient.
6. The current experiments use pretrained visual representations rather than end-to-end fine-tuning.
7. The visual feature dataset contains 146 usable records after preprocessing, compared with the 150 annotated records.
8. The results should be interpreted as research-experiment results rather than production-level performance.

---

# Future Work

Possible extensions include:

- Larger and more balanced datasets
- Improved minority-class handling
- Hyperparameter tuning
- Audio-based language identification
- Speech transcription
- OCR-based extraction of on-screen text
- Multimodal fusion of metadata, visual and audio information
- Confidence estimation
- Model explainability
- Optional web-based demonstration interface

The long-term direction is a multimodal news-video classification framework combining complementary information sources rather than relying on a single modality.

---

# Research Direction

The current project intentionally keeps the metadata and visual approaches separate.

The metadata pipeline establishes a textual/metadata baseline, while the visual pipeline investigates information contained directly in the video frames.

A future multimodal system could combine:

```text
Metadata
   +
Visual Features
   +
Audio / Speech
   +
OCR
   ↓
Multimodal Representation
   ↓
Unified Classification
```

This would allow the system to use complementary signals that are not available to a visual-only or metadata-only approach.

---

# Project Information

**Project Title:** Metadata-Assisted Multi-Label Classification of News Videos

**Project Type:** Research Internship Project

**Author:** Anwesha Choudhury

**Start Date:** 16 June 2026

**Institution:** Gauhati University Institute of Science and Technology

**Mentors / Advisors:**

- Prof. Prithwijit Guha
- Mohd. Amaan
- Parth Dhola

---

# Project Resources

### GitHub Repository

https://github.com/chan1425/multi-level-news-video-classification

### Project Website

https://chan1425.github.io/multi-level-news-video-classification/

### Google Colab

The notebooks in this repository can be opened and executed through Google Colab.

---

# Acknowledgements

This project was developed as part of a research internship and involved iterative experimentation across:

- Dataset annotation
- Metadata processing
- Text-based classification
- Video preprocessing
- Shot segmentation
- Keyframe extraction
- Visual representation learning
- Machine-learning classification
- Unseen-video inference

The project was developed through multiple stages of experimentation and refinement, with the final implementation focusing on the visual classification pipeline.

---

# Academic Note

This repository documents the implementation, experiments and research artifacts developed during the internship.

The reported results are specific to the dataset, preprocessing configuration, feature representations and evaluation methodology used in this project.

They should not be interpreted as general benchmarks for news-video classification.

---

# License

This repository is intended primarily for academic and research purposes.
