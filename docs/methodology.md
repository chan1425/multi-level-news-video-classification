# Methodology

## 1. Problem Definition

The project investigates multi-label classification of news videos across five attributes:

- Production
- Source Type
- Primary Language
- Geography
- Content Format

Instead of assigning a single category to a video, the system predicts a label for each of these five dimensions.

---

# 2. Dataset Preparation

A manually annotated dataset of 150 YouTube news videos was prepared.

Each video was assigned labels for:

```text
Production
Source Type
Primary Language
Geography
Content Format
```

The dataset contains the original YouTube URLs and corresponding annotations.

The actual video files are not included in the GitHub repository.

During the visual processing stage, 146 videos produced usable visual feature records after preprocessing and feature extraction.

---

# 3. Metadata-Based Baseline

The first experimental stage investigated whether YouTube metadata could provide useful classification signals.

## 3.1 Metadata Extraction

YouTube video information was obtained using the YouTube Data API v3.

The metadata pipeline uses textual information such as:

- Video title
- Description
- Tags
- Channel-related information

The extracted information was prepared for machine-learning experiments.

---

## 3.2 Text Preprocessing

The textual metadata was processed before feature extraction.

The resulting text was converted into numerical representations using TF-IDF.

```text
Raw Metadata
     ↓
Text Preprocessing
     ↓
TF-IDF Vectorization
     ↓
Numerical Feature Matrix
```

---

## 3.3 Classifier Comparison

Several classical machine-learning classifiers were evaluated:

- Logistic Regression
- Linear SVM
- Multinomial Naive Bayes
- Random Forest
- Decision Tree

The purpose of this stage was to establish an experimental baseline and compare classical approaches.

---

# 4. Visual Dataset Preparation

After the metadata experiments, the dataset was expanded for visual classification.

The visual pipeline operates on locally stored MP4 video clips.

The processing workflow was:

```text
Video Clips
     ↓
Shot Segmentation
     ↓
Representative Keyframes
     ↓
Visual Feature Extraction
     ↓
Video-Level Feature Dataset
```

---

# 5. Shot Boundary Detection

Shot segmentation was performed using histogram-based comparison between sampled video frames.

The **Bhattacharyya distance** was used to measure the difference between frame histograms.

The distance is defined as:

```text
D_B(P,Q) = sqrt(1 - Σ_i sqrt(P_i Q_i))
```

The main segmentation configuration was:

```text
Number of histogram bins = 16
Frame skip = 28
Threshold = 0.5
```

Frames were sampled at the configured interval and changes in the histogram representation were used to identify shot boundaries.

---

# 6. Representative Keyframe Selection

After detecting shot boundaries, one representative frame was selected from each shot.

The representative frame was selected from approximately the middle of the detected shot.

```text
k_shot = floor((f_start + f_end) / 2)
```

This produces a compact set of representative keyframes for each video.

The keyframes are subsequently used for visual feature extraction.

---

# 7. Visual Backbone Comparison

Two pretrained visual models were evaluated:

## DINOv2-Small

DINOv2-Small uses a ViT-S/14 architecture and produces a 384-dimensional representation.

```text
Feature dimension: 384
Parameters: ~22.06M
GFLOPs: ~5.53
Measured inference time: ~6.58 ms/image
```

## MobileNetV3-Small

MobileNetV3-Small was evaluated as a lightweight convolutional alternative.

```text
Feature dimension: 576
Parameters: ~0.93M
GFLOPs: ~0.06
Measured inference time: ~4.95 ms/image
```

The comparison considered both predictive performance and computational characteristics.

DINOv2-Small was selected as the main visual backbone because it showed stronger overall performance across several target attributes.

---

# 8. Feature Extraction

Each representative keyframe is passed through the selected pretrained visual backbone.

For DINOv2-Small, each keyframe produces a 384-dimensional feature vector:

```text
z_k ∈ R^384
```

The visual features from all representative keyframes belonging to the same video are then aggregated.

---

# 9. Video-Level Feature Aggregation

Mean pooling is used to convert multiple keyframe representations into one video-level representation.

For a video containing `K` representative keyframes:

```text
z_video = (1/K) Σ z_k
```

where:

- `K` = number of representative keyframes
- `z_k` = feature vector of keyframe `k`
- `z_video` = video-level feature representation

The resulting DINOv2 representation has 384 dimensions.

---

# 10. Visual Classification

A separate classification model is trained for each target attribute.

The five classifiers correspond to:

```text
Production
Source Type
Primary Language
Geography
Content Format
```

The visual experiments use a Random Forest classifier with:

```text
Number of estimators = 200
Cross-validation = 2-fold StratifiedKFold
```

Stratified cross-validation is used to preserve class proportions as much as possible across the folds.

---

# 11. Evaluation

The visual models are evaluated using:

- Accuracy
- Weighted Precision
- Weighted Recall
- Weighted F1-score

These metrics provide complementary information about overall prediction performance and class-weighted behaviour.

---

# 12. Backbone Evaluation

The two visual backbones were evaluated across all five target attributes.

The results show that DINOv2-Small provides stronger overall performance on:

- Production
- Primary Language
- Geography

MobileNetV3-Small performs slightly better in weighted F1-score for Source Type.

Content Format remains difficult for both approaches because of its multiple fine-grained categories and imbalanced class distribution.

---

# 13. Unseen Video Inference

After training and evaluation, an end-to-end inference pipeline was developed.

The inference process is:

```text
Unseen MP4
     ↓
Video Loading
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
Predicted Labels
```

The inference pipeline was tested on unseen news videos to verify that the system could process a video independently of the training feature dataset.

---

# 14. Experimental Design

The project intentionally maintains two separate approaches.

## Metadata

```text
YouTube URL
     ↓
YouTube Data API
     ↓
Metadata / Text
     ↓
TF-IDF
     ↓
Classical ML
```

## Visual

```text
Local MP4
     ↓
Shot Segmentation
     ↓
Keyframes
     ↓
DINOv2 / MobileNetV3
     ↓
Mean Pooling
     ↓
Random Forest
```

The current implementation does not combine the two feature spaces.

This separation makes it possible to study the behaviour of metadata-based and visual approaches independently.

---

# 15. Future Multimodal Methodology

A future version of the project could extend the current architecture by incorporating additional modalities.

Potential inputs include:

- YouTube metadata
- Visual features
- Audio features
- Speech transcripts
- OCR-derived text

A possible future architecture is:

```text
             News Video
                 │
      ┌──────────┼──────────┐
      │          │          │
      ▼          ▼          ▼
  Metadata     Visual      Audio
      │          │          │
      ▼          ▼          ▼
    TF-IDF     DINOv2    Speech /
                         Transcript
      │          │          │
      └──────────┼──────────┘
                 │
                 ▼
               OCR
                 │
                 ▼
       Multimodal Representation
                 │
                 ▼
          Unified Classifier
```

This could provide information that is unavailable from any single modality.

---

# Summary

The methodology follows a progressive research workflow:

```text
Manual Annotation
        ↓
Metadata Baseline
        ↓
Classifier Comparison
        ↓
Dataset Expansion
        ↓
Video Preprocessing
        ↓
Shot Segmentation
        ↓
Keyframe Extraction
        ↓
Visual Backbone Comparison
        ↓
Feature Aggregation
        ↓
Classification
        ↓
Evaluation
        ↓
Unseen Video Inference
```

The current final visual system uses representative keyframes, DINOv2-Small visual embeddings, mean pooling and separate Random Forest classifiers for the five target attributes.
