# 🧠 Glioma-AI

**AI-based Image Classification for Glioma Diagnosis, IDH Genotype Prediction, and Prognostic Modeling**

> An end-to-end, region-aware AI pipeline integrating whole-slide histopathology images with clinical, radiological, and molecular data to support glioma diagnosis, grading, molecular prediction, and prognosis modeling.

---

## 📌 Project Overview

Glioma-AI is a research project that develops and validates a **multi-modal AI framework** using **H&E whole-slide images (WSIs)** combined with **clinical, radiological, and molecular data** to support:

* Glioma subtype classification
* WHO grade prediction
* IDH1 genotype prediction
* Prognostic risk and survival modeling

The project is built on the **GamaPath–OMERO digital pathology platform**, enabling scalable analysis of glioma WSIs curated at **Dr. Sardjito Hospital, Yogyakarta**.

---

## 🎯 Research Objectives

This project aims to:

1. Develop a **reproducible AI pipeline** for glioma analysis integrating:

   * Histopathology WSIs
   * Clinical data
   * Radiological findings
   * Molecular status

2. Predict the following outputs:

   * Tumor type (astrocytoma vs oligodendroglioma)
   * Glioma grade
   * Binary IDH1 mutation status (mutant vs wild-type)
   * Individualized prognosis:

     * Risk scores
     * Survival probability at 1–2 years
     * Time-to-event estimates

3. Perform **comparative benchmarking** of different modeling strategies to identify accurate and efficient methodologies.

---

## 🧠 Scientific Background

Gliomas are the most common primary tumors of the central nervous system (CNS) in adults. Accurate grading and molecular characterization—particularly **IDH1 mutation status**—are critical for treatment planning and prognosis.

In Indonesia, limited access to neuropathology expertise and molecular diagnostics often leads to diagnostic delays. The availability of a large, well-curated WSI archive through **GamaPath–OMERO** provides a unique opportunity to develop AI-based diagnostic support systems that leverage routinely available histopathology slides.

While prior studies have demonstrated high accuracy in glioma grading and IDH prediction using CNN-based approaches, most existing workflows:

* Focus on single tasks (grading *or* mutation prediction),
* Lack interpretability,
* Do not integrate prognosis modeling.

This project addresses these gaps by providing a **unified, region-aware, and clinically grounded pipeline**.

---

## 🧩 Technical Workflow

![Glioma-AI Research Workflow](figure/Research%20Workflow.jpg)

### **Step 1 — Data Acquisition & Preprocessing**

**Input**

* 100 H&E WSIs (.svs) of astrocytoma and oligodendroglioma
* Matched clinical, radiological, and molecular metadata
* Pretrained segmentation model (GBM_WSSM)

**Process**

* WSI quality control (tissue detection, sharpness assessment)
* Color normalization (Macenko)
* Patch extraction (256×256 px, stride 128)
* Patient-level data splitting
* Metadata harmonization

**Output**

> Curated dataset of QC-passed WSIs with structured metadata

---

### **Step 2 — Semi-Automated Region Annotation**

**Process**

* Apply pretrained GBM_WSSM to generate provisional **8-class tissue masks**:

  * Infiltrating Tumor (IT)
  * Leading Edge (LE)
  * Cellular Tumor (CT)
  * Perinecrotic Zones (CTpnz)
  * Pseudopalisading around Necrosis (CTpan)
  * Microvascular Proliferation (CTmvp)
  * Tumor Necrosis (CTne)
  * Background (BG)

* Automatically flag outlier regions based on confidence and area statistics

* Pathologist review of flagged regions using QuPath

**Output**

> Expert-verified pixel-level region masks for all WSIs

---

### **Step 3 — Region-Based Feature Extraction**

**Tools**

* OpenCV
* scikit-image
* PyRadiomics
* Custom feature scripts

**Features (per region)**

* CTmvp: vessel density, wall thickness
* CTne: area proportion, spatial clustering, boundary fractal dimension
* IT / LE: infiltration depth, texture entropy
* CTpan: nuclear alignment regularity, radial symmetry

**Deep Features**

* CNN backbone: ResNet50 (ImageNet pretrained)
* Global Max Pooling
* Feature vector length: 2048

**Aggregation**

* Per-slide summary statistics (mean, median, 10th/90th percentile)

**Output**

> Slide-level region-aware feature matrix

---

### **Step 4 — Glioma Grading Model**

**Process**

* Class imbalance handling using SMOTE
* Feature selection via ANOVA F-score
* Classifier: XGBoost with inner cross-validation

**Output**

> Trained glioma grade classifier with performance metrics
> (accuracy, precision, recall, F1-score, AUROC)

---

### **Step 5 — Hybrid IDH1 Genotype Prediction**

**Pipeline**

1. Patch extraction from tumor regions only
2. MIL-CNN branch:

   * ResNet50 backbone
   * Attention-based pooling
3. Region-based morphology model
4. Hybrid fusion using a meta-classifier

**Interpretability**

* Attention heatmaps from MIL
* Feature importance from region-based model

**Output**

> IDH1 mutation prediction model with confidence estimates and evaluation metrics

---

### **Step 6 — Prognostic Modeling**

**Process**

* Compute region-level descriptors
* Cox proportional hazards modeling integrating:

  * Clinical variables (age, gender, KPS)
  * Molecular data (IDH, MGMT, 1p/19q)
  * Morphological features
* Kaplan–Meier survival analysis

**Output**

> Hazard ratios with 95% CI
> Survival curves and risk stratification

---

### **Step 7 — Validation & Deployment**

**Validation**

* Nested cross-validation
* External benchmarking on TCGA / CPTAC datasets

**Deployment**

* Dockerized inference pipeline
* QuPath plugin for visualization and AI-assisted interpretation

**Output**

> AI-assisted glioma diagnosis and prognosis research prototype

---

## 🧠 Key Contributions

* Unified AI framework for **diagnosis, molecular prediction, and prognosis**
* Region-aware modeling aligned with glioma histopathology
* Integration of Indonesian WSI cohorts with survival follow-up
* Built on an open, extensible digital pathology platform (GamaPath–OMERO)

---

## 👤 Author

**Muhammad Nur Fahmi, MD**
Physician–Researcher

---
