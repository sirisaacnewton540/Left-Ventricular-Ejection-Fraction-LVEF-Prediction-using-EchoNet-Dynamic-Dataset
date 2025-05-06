# 🫀 Left Ventricular Ejection Fraction (LVEF) Prediction using EchoNet-Dynamic Dataset

## 🔍 Background & Motivation

LVEF is a cornerstone metric in diagnosing heart failure and evaluating cardiac function. Traditionally measured using Simpson’s biplane method in echocardiography, it requires precise delineation of the endocardial border at end-diastolic and end-systolic phases — a skill-intensive and time-consuming process prone to inter-operator variability.

With the rise of AI in cardiology, deep learning approaches promise faster and more reproducible assessments. However, most prior work focuses on full-sequence models or black-box video-to-score mappings without considering the **biomechanical phases** of cardiac motion. Our approach departs from this trend by:

- **Deconstructing the cardiac cycle** into clinically interpretable key frames (ED, ES, Mid),
- **Extracting high-level spatial features** using CNN backbones,
- **Combining classification and regression** paradigms to support both clinical decision thresholds and continuous estimation.

We aim to answer: _Can a frame-aware, interpretable pipeline outperform end-to-end models in LVEF prediction — and offer clinical transparency without sacrificing performance?_

---

## 🎯 Research Objectives

1. **Model cardiac function** using echocardiographic video data in a clinically meaningful and explainable way.
2. **Compare the predictive utility** of End-Diastolic (EDF), End-Systolic (ESF), and Mid-Frame (MF) across tasks.
3. **Quantify model performance** in both binary risk classification and continuous regression.
4. **Bridge clinical significance and ML performance**, highlighting where predictions deviate and why.

---

## 📁 Dataset: [EchoNet-Dynamic](https://www.kaggle.com/datasets/mahnurrahman/echonet-dynamic)

**Source**: Stanford ML Group  
**Modality**: Apical 4-chamber echocardiographic videos  
**Size**: ~10,000 videos (~7.9 GB total)  
**Label Source**: VolumeTracings.csv (Simpson’s rule)

### Key Attributes:
- 112x112 grayscale MP4 videos (25 fps)
- Ground truth LVEF values computed from ED/ES tracings
- Metadata includes frame IDs for ESF and EDF per video
- High intra-patient variability in imaging angle and quality

![__results___5_3](https://github.com/user-attachments/assets/161eb0c3-c969-4691-886d-cbda8b8f2965)

---

## 🧪 Methodology Overview

Our pipeline is structured as a **modular research framework**, enabling experimentation and reproducibility.

### 1. Frame Extraction and Preprocessing

- Extract EDF, ESF, and MF from each video using `FileList.csv` and midpoint heuristics.
- Resize frames to 112x112, normalize intensity, and remove artifacts.
- Justification: Cardiac function is biomechanically phase-dependent. Focusing on distinct frames allows us to isolate clinically meaningful variation across patients.

---

### 2. Deep Feature Extraction

- Backbone: **ResNet-50** pretrained on ImageNet.
- Output: 2048-dim feature vector from penultimate layer per frame.
- Justification: CNNs pretrained on natural images retain spatial priors useful for cardiac chamber structure (see Ouyang et al., *Nature*, 2020). Transfer learning enables robust representation learning from relatively few samples.

---

### 3. Classification Module

- Task: Predict whether LVEF is **≤40% (reduced)** or **>40% (normal)**.
- Architecture: MLP classifier with batch norm + dropout.
- Loss: Cross-entropy
- Justification: Binary thresholds align with clinical guidelines for heart failure with reduced ejection fraction (HFrEF).

---

### 4. Regression Module

- Task: Predict continuous LVEF score (%)
- Methods:
  - **MLP Regression**
  - **Gaussian Process Regression (GPR)**
- Justification: GPR offers uncertainty estimates and preserves functional smoothness, enabling confidence-aware clinical usage.

---

### 5. Evaluation Metrics

- Classification: Accuracy, F1, Precision, Recall, ROC-AUC
- Regression: MAE, RMSE, R² Score, Residual Analysis

---

## 📈 Results

### 🔵 Classification

| Frame | Accuracy | F1-Score | AUC |
|-------|----------|----------|-----|
| EDF   | 87.2%    | 0.85     | 0.89 |
| ESF   | 88.5%    | 0.86     | 0.91 |
| MF    | 86.9%    | 0.84     | 0.88 |

![__results___12_0](https://github.com/user-attachments/assets/74bce127-3a1e-4753-8594-3850997ce0a1)

![image](https://github.com/user-attachments/assets/919181b0-102e-4c5d-969e-df1015d7e134)

![image](https://github.com/user-attachments/assets/874d50a5-55b2-496b-bf75-8101def84b37)

![image](https://github.com/user-attachments/assets/1b033d34-cb54-42d3-899d-c1cdc4c3ad46)

![image](https://github.com/user-attachments/assets/30702c24-f69b-4f62-93d7-f97869826f9a)

- **Interpretation**: ESF slightly outperforms other frames — confirming its stronger association with ventricular contraction phases.
- Misclassifications concentrated in borderline LVEF zones (35–45%).

---

### 🔴 Regression

| Model         | MAE   | RMSE  | R²    |
|---------------|-------|-------|-------|
| MLP (EDF)     | 3.98  | 5.24  | 0.86  |
| GPR (MF)      | 3.24  | 4.56  | 0.91  |
| Ensemble Avg. | 2.97  | 4.19  | 0.93  |

- **Interpretation**: Mid-frame GPR performs best among single-frame methods. Ensemble averaging across EDF, ESF, and MF improves robustness and generalization.

---

## 🔍 Clinical Interpretability

- Frame-level heatmaps reveal spatial focus on **mitral valve**, **interventricular septum**, and **LV endocardium**.
- GPR models capture variance in ambiguous cases, flagging potential human-AI review zones.

> 📂 See `results/__results___15_9.png` and others for qualitative overlays.

---

## 💡 Key Contributions

- Novel evaluation of LVEF predictability across biomechanically distinct frames.
- Demonstrated viability of low-frame-count, interpretable pipelines for cardiac AI.
- Quantified trade-offs between deep model complexity and clinical transparency.

---

