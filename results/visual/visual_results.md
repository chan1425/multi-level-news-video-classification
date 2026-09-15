# Visual Pipeline Results

The visual pipeline evaluates video classification using representative keyframes extracted through shot segmentation and pretrained visual feature extractors.

## Visual Backbones

Two pretrained backbones were compared:

- DINOv2-Small (ViT-S/14)
- MobileNetV3-Small

DINOv2-Small produces a 384-dimensional representation, while MobileNetV3-Small produces a 576-dimensional representation.

## Evaluation

A Random Forest classifier with 200 estimators was evaluated using 2-fold StratifiedKFold cross-validation.

The reported metrics are:

- Accuracy
- Weighted Precision
- Weighted Recall
- Weighted F1-score

## Results

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

## Backbone Comparison

DINOv2-Small was selected as the main visual backbone because it provided stronger overall performance across several classification targets.

MobileNetV3-Small is considerably lighter and faster, and achieved a slightly higher weighted F1-score for Source Type.

## Interpretation

The results show that visual features can provide useful signals for Production, Source Type, Primary Language, and Geography classification.

Content Format remains substantially more difficult for both backbones, particularly because it contains multiple classes with an imbalanced distribution.

These results should be interpreted as research-experiment results rather than production-level performance.
