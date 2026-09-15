# Limitations and Future Work

## Current Limitations

Although the project demonstrates a complete news-video classification workflow, several limitations remain.

---

## 1. Dataset Size

The annotated dataset contains approximately 150 news videos.

After visual preprocessing and feature extraction, 146 videos produced usable visual feature records.

This is relatively small for a fine-grained visual classification problem involving five different target attributes.

A larger dataset would provide a stronger basis for evaluating generalisation.

---

## 2. Class Imbalance

The target attributes do not have uniform class distributions.

Some categories contain substantially more examples than others.

This is particularly important for:

- Source Type
- Primary Language
- Geography
- Content Format

Class imbalance can make overall accuracy appear more favourable toward majority classes while minority classes remain difficult to predict.

---

## 3. Content Format Classification

Content Format is the most challenging visual classification target in the current experiments.

The dataset contains 13 different content-format categories, including:

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

The visual pipeline achieved relatively low weighted F1 for this target:

| Backbone | Weighted F1 |
|---|---:|
| DINOv2-Small | 20.43% |
| MobileNetV3-Small | 18.62% |

The large number of fine-grained categories and their uneven distribution make this task substantially more difficult than several of the other targets.

---

## 4. Visual-Only Language Understanding

The current main pipeline relies on visual information.

It does not directly analyse:

- Speech
- Audio
- Transcripts
- Spoken words

As a result, **Primary Language** can be difficult to determine when the language cannot be inferred reliably from visual information.

This is an important limitation of a visual-only approach.

---

## 5. No Current Multimodal Fusion

The metadata and visual pipelines are currently independent.

The project does not yet combine:

```text
Metadata + Visual + Audio + OCR
```

into a unified representation.

The metadata pipeline serves as an experimental baseline, while the visual pipeline is the main/final approach.

---

## 6. Pretrained Feature Representations

The visual pipeline uses pretrained DINOv2-Small and MobileNetV3-Small models as feature extractors.

The current experiments do not perform complete end-to-end fine-tuning of the visual backbone for the news-video dataset.

Task-specific fine-tuning could potentially improve performance if sufficient training data were available.

---

## 7. Computational Constraints

The visual pipeline requires video preprocessing, keyframe extraction and deep visual feature extraction.

Although MobileNetV3-Small is lightweight, DINOv2-Small requires substantially more computational resources.

The experiments were therefore conducted using GPU acceleration.

---

## 8. Generalisation

The current results are specific to the annotated dataset and the experimental configuration used in the notebooks.

The dataset size, class distribution, video sources and preprocessing choices may affect the observed performance.

Therefore, the reported results should be interpreted as research-experiment results rather than general benchmarks for news-video classification.

---

# Future Work

## 1. Dataset Expansion

A larger dataset could be collected and annotated to improve the reliability of the experiments.

Future datasets should aim for:

- More videos
- Better class balance
- Greater source diversity
- Greater geographical diversity
- More examples of minority classes

---

## 2. Improved Minority-Class Handling

Future experiments could investigate techniques such as:

- Class weighting
- Resampling
- Stratified sampling strategies
- Data augmentation
- Alternative evaluation strategies

These approaches could help improve performance on underrepresented categories.

---

## 3. Hyperparameter Tuning

The current experiments use a fixed Random Forest configuration.

Future work could explore:

- Number of estimators
- Maximum tree depth
- Minimum samples per split
- Minimum samples per leaf
- Feature-selection strategies

Systematic hyperparameter optimisation could provide improved performance.

---

## 4. Audio and Speech Processing

Audio information could provide important signals, particularly for Primary Language classification.

Future work could include:

```text
Video
  ↓
Audio Extraction
  ↓
Language Identification
  +
Speech Transcription
```

Speech transcripts could also provide additional semantic information for other classification targets.

---

## 5. OCR-Based Information

News videos frequently contain visual text such as:

- Headlines
- News tickers
- Location names
- Channel names
- Captions
- On-screen labels

OCR could extract this information and provide additional signals for classification.

A possible future pipeline is:

```text
Representative Keyframes
        ↓
OCR
        ↓
Extracted On-Screen Text
        ↓
Text Representation
```

---

## 6. Multimodal Fusion

A major future direction is to combine multiple information sources.

```text
                News Video
                    │
        ┌───────────┼───────────┐
        │           │           │
        ▼           ▼           ▼
    Metadata      Visual      Audio
        │           │           │
      TF-IDF      DINOv2    Transcript
        │           │           │
        └───────────┼───────────┘
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

This could allow the model to use complementary signals from metadata, visual content, speech and on-screen text.

---

## 7. Confidence and Explainability

Future versions could provide:

- Prediction confidence
- Class probabilities
- Important visual regions
- Representative frames supporting predictions
- Explanation of classification decisions

This would make the system easier to analyse and interpret.

---

## 8. Improved Content Format Classification

Because Content Format is currently the most difficult target, future research could investigate:

- Better class balancing
- More training examples per category
- Temporal video representations
- Audio and transcript features
- OCR information
- Multimodal classification

Content Format may benefit particularly from information beyond individual visual frames because many formats are defined by presentation style, speech patterns and temporal structure.

---

# Long-Term Direction

The long-term goal is to develop a multimodal news-video classification framework capable of analysing a video across multiple complementary dimensions.

The envisioned system would combine:

```text
Metadata
   +
Visual Information
   +
Audio / Speech
   +
OCR
   ↓
Multimodal Representation
   ↓
Multi-Target Classification
```

The current metadata and visual pipelines provide the experimental foundation for this future direction.

---

# Conclusion

The current project establishes a complete research workflow from dataset annotation and metadata-based experimentation to visual feature extraction, classification, evaluation and unseen-video inference.

The results demonstrate that pretrained visual representations can provide useful signals for several news-video attributes, while also highlighting the limitations of visual-only classification.

The most important next steps are dataset expansion, improved class balancing and incorporation of additional modalities such as audio, speech and OCR.
