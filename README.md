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

The Content Format categories include formats such as Explainer, News Bulletin, Interview, Press Conference, Breaking News, Fact Check, Analysis, Panel Discussion, Documentary, Debate, Podcast Clip, Reaction Video and Vlog.

---

# Dataset

The project uses a manually annotated dataset of approximately **150 YouTube news videos**.

Each record contains:

- YouTube video URL
- Production label
- Source Type label
- Primary Language label
- Geography label
- Content Format label

The actual video files are **not included** in this repository.

The visual feature pipeline contains **146 usable video feature records** after video preprocessing and feature extraction.

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
