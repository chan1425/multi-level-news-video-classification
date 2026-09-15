# Metadata Pipeline Results

The metadata pipeline was developed as an experimental baseline using YouTube video metadata and TF-IDF-based textual features.

## Approach

Metadata and textual signals were extracted from YouTube videos and transformed using TF-IDF.

Several classical machine-learning classifiers were compared:

- Logistic Regression
- Linear SVM
- Multinomial Naive Bayes
- Random Forest
- Decision Tree

## Baseline Results

The initial metadata baseline achieved:

- Test Accuracy: 60%
- Macro F1-score: 0.44
- Weighted F1-score: 0.53

The expanded metadata experiments focused on Geography because of its comparatively established baseline and class distribution.

For the expanded dataset:

- Cross-validation Accuracy: 58%
- Test Accuracy: 60%

## Role in the Project

The metadata pipeline serves as an experimental baseline for evaluating how much classification information can be obtained from textual and YouTube metadata alone.

The main/final pipeline of the project is the visual pipeline based on representative video keyframes and pretrained visual feature extraction.

## Limitations

Metadata-based classification depends on the quality and completeness of available YouTube metadata.

The metadata pipeline is therefore treated as a baseline rather than the final classification system.
