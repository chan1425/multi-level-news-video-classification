# Project Architecture

## Overview

The project implements two intentionally separate classification pipelines for news videos:

1. **Metadata Pipeline** — an experimental baseline using YouTube metadata and textual information.
2. **Visual Pipeline** — the main/final approach using representative video keyframes and pretrained visual embeddings.

The two approaches are evaluated independently rather than being fused in the current implementation.

---

# 1. Metadata Pipeline

The metadata pipeline investigates whether information available from YouTube can provide useful signals for news-video classification.

## Architecture

```text
YouTube URL
     ↓
YouTube Data API v3
     ↓
Metadata / Textual Information
     ↓
Text Preprocessing
     ↓
TF-IDF Representation
     ↓
Classifier Comparison
     ↓
Predictions + Evaluation
```

## Input

The pipeline takes YouTube video URLs as input.

Metadata and textual information are obtained using the **YouTube Data API v3**.

The available information includes signals such as:

- Title
- Description
- Tags
- Channel-related information
- Other textual metadata

## Feature Representation

Textual information is transformed into numerical features using **TF-IDF (Term Frequency–Inverse Document Frequency)**.

## Classifier Comparison

The following classical machine-learning classifiers were evaluated:

- Logistic Regression
- Linear SVM
- Multinomial Naive Bayes
- Random Forest
- Decision Tree

## Role in the Project

The metadata pipeline serves as an experimental baseline.

It provides a reference point for understanding how much classification information can be obtained from YouTube metadata before introducing visual information.

---

# 2. Visual Pipeline

The visual pipeline is the main/final classification approach.

Unlike the metadata pipeline, it operates on locally stored MP4 video clips.

## Architecture

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
Video-Level Representation
     ↓
Random Forest Classifier
     ↓
Five Target Predictions
```

---

# 3. Shot Segmentation

The first stage of the visual pipeline identifies changes between shots within a video.

Shot boundary detection is performed using **Bhattacharyya distance** between frame histograms.

The implementation uses:

```text
Number of histogram bins = 16
Frame skip = 28
Threshold = 0.5
```

The distance between consecutive sampled frames is used to identify potential shot transitions.

---

# 4. Representative Keyframe Extraction

After shot segmentation, one representative frame is selected from each detected shot.

The representative frame is chosen from approximately the middle of the shot:

```text
k_shot = floor((f_start + f_end) / 2)
```

This provides a compact representation of the visual content while avoiding the need to process every frame in the video.

---

# 5. Visual Backbone Comparison

Two pretrained visual feature extractors were evaluated:

## DINOv2-Small

```text
Architecture: ViT-S/14
Feature dimension: 384
Parameters: approximately 22.06M
GFLOPs: approximately 5.53
Measured inference time: approximately 6.58 ms/image
```

## MobileNetV3-Small

```text
Feature dimension: 576
Parameters: approximately 0.93M
GFLOPs: approximately 0.06
Measured inference time: approximately 4.95 ms/image
```

DINOv2-Small was selected as the main visual backbone because it provided stronger overall performance across several classification targets.

MobileNetV3-Small was retained as an important lightweight comparison because it requires substantially fewer parameters and computational resources.

---

# 6. Video-Level Feature Representation

Each representative keyframe is passed through the selected visual backbone to obtain a frame-level embedding.

For DINOv2-Small:

```text
z_k ∈ R^384
```

The frame-level representations are aggregated using mean pooling:

```text
z_video = (1/K) Σ z_k
```

where:

- `K` is the number of representative keyframes
- `z_k` is the embedding of the `k`-th keyframe
- `z_video` is the resulting video-level representation

The resulting DINOv2 representation is therefore a **384-dimensional feature vector per video**.

---

# 7. Classification Layer

The video-level feature representation is provided to a separate classifier for each target.

The five classification targets are:

```text
Production
Source Type
Primary Language
Geography
Content Format
```

The visual experiments use:

```text
Random Forest
200 estimators
2-fold StratifiedKFold
```

The evaluation reports:

- Accuracy
- Weighted Precision
- Weighted Recall
- Weighted F1-score

---

# 8. End-to-End Inference

After training, the visual pipeline can process an unseen news video.

```text
Unseen MP4
     ↓
Shot Segmentation
     ↓
Representative Keyframes
     ↓
DINOv2 Feature Extraction
     ↓
Mean Pooling
     ↓
384-D Video Representation
     ↓
Five Trained Classifiers
     ↓
Predictions
```

The inference pipeline therefore does not require the unseen video to already exist in the training feature dataset.

---

# 9. Current System Design

The current architecture deliberately keeps metadata and visual information separate.

```text
                 NEWS VIDEO
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
   Metadata Pipeline       Visual Pipeline
          │                     │
          ▼                     ▼
   YouTube Metadata       Local MP4 Video
          │                     │
          ▼                     ▼
       TF-IDF              Shot Segmentation
          │                     │
          ▼                     ▼
   ML Classifiers          Keyframes
          │                     │
          ▼                     ▼
   Baseline Results       DINOv2 / MobileNet
                                │
                                ▼
                         Video Representation
                                │
                                ▼
                         Random Forest Models
                                │
                                ▼
                         Five Predictions
```

---

# 10. Future Multimodal Extension

The current implementation does not combine the two pipelines.

A future extension could integrate multiple information sources:

```text
              News Video
                  │
      ┌───────────┼───────────┐
      │           │           │
      ▼           ▼           ▼
  Metadata      Visual      Audio
      │           │           │
      ▼           ▼           ▼
    TF-IDF     DINOv2      Speech /
                          Transcription
      │           │           │
      └───────────┼───────────┘
                  │
                  ▼
              OCR Features
                  │
                  ▼
        Multimodal Representation
                  │
                  ▼
          Unified Classifier
```

Potential future components include:

- Audio-based language identification
- Speech transcription
- OCR-based extraction of on-screen text
- Metadata + visual + audio fusion
- Larger and more balanced datasets
- Improved handling of minority classes
- Confidence estimation
- Explainability

---

# Summary

The project progresses from a metadata-based experimental baseline toward a visual classification system capable of processing unseen news videos.

The current final visual pipeline is:

```text
MP4 Video
   ↓
Shot Segmentation
   ↓
Representative Keyframes
   ↓
DINOv2-Small
   ↓
384-D Features
   ↓
Mean Pooling
   ↓
Random Forest
   ↓
Production
Source Type
Primary Language
Geography
Content Format
```

This architecture provides the foundation for future multimodal news-video classification.
