# 🩺 Glaucoma Detection Through Optic Disc and Optic Cup Segmentation

> **A research project on automatic Optic Disc (OD) and Optic Cup (OC) segmentation from retinal fundus images for glaucoma-related analysis.**

This repository contains the complete research work, implementations, experiments, notebooks, reports, and results developed for **Optic Disc (OD) and Optic Cup (OC) segmentation** using retinal fundus images.

The work consists of **two major research implementations**:

* **Paper 1:** Hybrid U-Net + HED + GVF-based Active Contour / Snake
* **Paper 2:** CSP-SegNet+E — CSP-SegNet with an Edge-Supervised Auxiliary Branch

Both approaches investigate different strategies for improving OD/OC segmentation, particularly around difficult anatomical boundaries.

---

# 📌 Project Overview

Glaucoma is a progressive eye disease that can cause irreversible vision loss. One important step in computer-aided glaucoma analysis is the accurate segmentation of the:

* **Optic Disc (OD)** — the region where the optic nerve exits the retina.
* **Optic Cup (OC)** — the central depression inside the optic disc.

Accurate segmentation of the optic disc and optic cup enables the estimation of structural indicators such as the **Cup-to-Disc Ratio (CDR)**.

The overall research focuses on developing automated segmentation methods that can accurately identify OD and OC boundaries from retinal fundus images.

---

# 🔬 Research Structure

The project is divided into two major approaches:

```text
                 RETINAL FUNDUS IMAGE
                         │
                         ▼
                  PREPROCESSING
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
           PAPER 1               PAPER 2
              │                     │
              ▼                     ▼
       U-Net + HED + GVF       CSP-SegNet+E
       Active Contour           Edge-Supervised
              │                     │
              ▼                     ▼
       Boundary Refinement      Direct Segmentation
              │                     │
              └──────────┬──────────┘
                         ▼
                  OD / OC MASKS
                         │
                         ▼
                    EVALUATION
                         │
                         ▼
                CDR / GLaucoma
                    Analysis
```

---

# 📄 Paper 1 — Hybrid U-Net + HED + GVF Active Contour

## 🎯 Objective

The first research implementation proposes a **hybrid deep learning and active contour-based framework** for automatic OD and OC segmentation.

The key idea is to combine:

* **U-Net** → initial segmentation
* **HED** → edge detection
* **GVF** → external force/vector field
* **Active Contour / Snake** → boundary refinement

The architecture combines the region-level understanding of deep learning with the boundary-refinement capability of classical active contour models.

---

## 🏗️ Paper 1 Architecture

```text
                FUNDUS IMAGE
                     │
                     ▼
              ROI EXTRACTION
                     │
                     ▼
            ┌────────┴────────┐
            │                 │
            ▼                 ▼
          U-NET              HED
            │                 │
            ▼                 ▼
    Initial Segmentation     Edge Map
            │                 │
            ▼                 ▼
    Initial Contour          GVF
            │                 │
            │           Vector Field
            │                 │
            └────────┬────────┘
                     ▼
          PARAMETRIC ACTIVE
              CONTOUR / SNAKE
                     │
                     ▼
          FINAL OD / OC CONTOUR
                     │
                     ▼
            SEGMENTATION MASK
                     │
                     ▼
                CDR ESTIMATION
```

---

## 🔄 Paper 1 Pipeline

### 1. Fundus Image

The input to the system is a retinal fundus image.

The image contains different retinal structures such as blood vessels, optic disc, optic cup, macula, and background regions.

---

### 2. ROI Extraction

The relevant retinal region is extracted before segmentation.

The purpose of ROI extraction is to:

* Remove irrelevant regions.
* Reduce computational complexity.
* Focus the model on the optic nerve head region.
* Improve segmentation and edge detection.

```text
Fundus Image
     │
     ▼
ROI Extraction
     │
     ▼
Relevant Retinal Region
```

---

### 3. U-Net Initial Segmentation

The ROI is passed through a **U-Net segmentation network**.

U-Net generates an initial segmentation of the optic disc and optic cup.

```text
ROI
 │
 ▼
U-Net
 │
 ▼
Initial OD / OC Mask
 │
 ▼
Initial Contour
```

The predicted mask is converted into an initial contour that serves as the starting point for the active contour model.

---

### 4. HED Edge Detection

In parallel, the ROI is passed through **HED (Holistically-Nested Edge Detection)**.

HED generates an edge map highlighting important image boundaries.

```text
ROI
 │
 ▼
HED
 │
 ▼
Edge Map
```

The edge map provides boundary information to the subsequent GVF and active contour stages.

---

### 5. GVF — Gradient Vector Flow

The HED edge map is used to generate a **Gradient Vector Flow (GVF)** field.

GVF produces a vector field that guides the active contour toward the desired boundaries.

```text
HED Edge Map
      │
      ▼
     GVF
      │
      ▼
Vector Field
      │
      ▼
Contour Guidance
```

GVF is particularly useful when the initial contour is not perfectly aligned with the actual anatomical boundary.

---

### 6. Parametric Active Contour / Snake

The initial contour generated by U-Net is refined using the GVF vector field.

The snake evolves iteratively under the influence of internal and external forces.

```text
Initial Contour
      │
      ▼
┌───────────────┐
│ Active Contour │
│    / Snake     │
└───────────────┘
      ▲
      │
  GVF Vector
    Field
```

The goal is to move the contour toward the actual OD/OC boundary while maintaining contour smoothness.

---

### 7. Final Segmentation

After convergence, the refined contour is converted into a final segmentation mask.

The final output contains:

* Optic Disc mask
* Optic Cup mask
* Refined OD contour
* Refined OC contour

---

### 8. CDR Estimation

The final OD and OC masks can be used to estimate the **Cup-to-Disc Ratio**.

For vertical CDR:

[
CDR = \frac{Vertical\ Cup\ Diameter}
{Vertical\ Disc\ Diameter}
]

CDR can be used as a structural indicator in glaucoma-related analysis.

> CDR should not be interpreted as a standalone clinical diagnosis.

---

# 📄 Paper 2 — CSP-SegNet+E

## 🎯 Objectives

The second research implementation investigates an improved segmentation architecture called **CSP-SegNet+E**.

The main objectives are:

1. Segment the **Optic Disc (OD)**.
2. Segment the **Optic Cup (OC)**.
3. Improve segmentation around difficult object boundaries.
4. Investigate the use of **edge information as auxiliary supervision**.
5. Evaluate segmentation using multiple quantitative metrics.
6. Investigate model performance across different retinal datasets.
7. Study the effects of **domain shift** between datasets.

---

# 🧠 CSP-SegNet+E

CSP-SegNet+E extends the CSP-SegNet segmentation framework by introducing an additional **edge-supervision branch**.

Instead of learning only the segmentation task, the model simultaneously learns:

1. **Semantic segmentation**
2. **Boundary/edge prediction**

This creates a multi-task learning framework.

---

# 🏗️ Paper 2 Architecture

```text
                RETINAL FUNDUS IMAGE
                         │
                         ▼
                   PREPROCESSING
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
             ROI      Resizing   Edge
          Preparation  & Norm.   Information
              │          │          │
              └──────────┴──────────┘
                         │
                         ▼
                    CSP-SegNet+E
                         │
                ┌────────┴────────┐
                │                 │
                ▼                 ▼
        Segmentation Head      Edge Head
                │                 │
                ▼                 ▼
        Background / OD / OC  Edge Prediction
                │                 │
                └────────┬────────┘
                         ▼
                Final Segmentation
                         │
                         ▼
                   OD / OC Masks
                         │
                         ▼
                    Evaluation
```

---

# 🔀 Multi-Task Learning

The major difference between a conventional segmentation model and CSP-SegNet+E is the additional edge branch.

```text
                    CSP-SegNet+E
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
      Segmentation Head         Edge Head
              │                     │
              ▼                     ▼
        OD / OC Mask             Edge Map
              │                     │
              └──────────┬──────────┘
                         ▼
                  Joint Learning
```

The segmentation branch learns **what the pixels represent**, while the edge branch learns **where important boundaries occur**.

This auxiliary edge supervision can encourage the network to learn more boundary-aware representations.

---

# 🧩 CSP-SegNet Components

The CSP-SegNet+E architecture incorporates lightweight neural network components designed for efficient feature extraction.

The architecture includes components such as:

* Separable Convolution
* Batch Normalization
* ReLU activation
* Squeeze-and-Excitation blocks
* Inverted Residual Blocks
* CSP modules
* Segmentation Head
* Edge Head

These components are intended to maintain segmentation capability while keeping the architecture relatively lightweight.

---

# 🖼️ Edge Information

Edge information is incorporated as an auxiliary learning signal.

The edge branch provides explicit information about anatomical boundaries.

Conceptually:

```text
Fundus Image
     │
     ▼
Feature Extraction
     │
     ├───────────────┐
     ▼               ▼
Segmentation       Edge
 Features         Features
     │               │
     ▼               ▼
 OD / OC Mask     Boundary Map
```

The objective is to improve the model's ability to distinguish boundaries between:

* Background and optic disc
* Optic disc and optic cup
* Other nearby retinal structures

---

# 📊 Evaluation Metrics

Both segmentation approaches can be evaluated using quantitative metrics.

## Dice Score

[
Dice =
\frac{2TP}{2TP+FP+FN}
]

Dice measures the overlap between the predicted mask and ground-truth mask.

A value closer to **1** represents better segmentation.

---

## Intersection over Union

[
IoU =
\frac{TP}{TP+FP+FN}
]

IoU measures the intersection between prediction and ground truth relative to their union.

---

## Precision

[
Precision =
\frac{TP}{TP+FP}
]

Precision indicates how many predicted positive pixels are actually correct.

---

## Recall

[
Recall =
\frac{TP}{TP+FN}
]

Recall indicates how many ground-truth positive pixels were successfully detected.

---

## HD95

**95th Percentile Hausdorff Distance (HD95)** evaluates boundary similarity.

A lower HD95 generally indicates better boundary alignment.

HD95 is particularly useful for OD/OC segmentation because accurate anatomical boundaries are important.

---

# 🧪 Dataset Evaluation

The project investigates performance on retinal datasets including:

### REFUGE / REFUGE2

The REFUGE dataset family provides retinal fundus images with OD/OC annotations and is widely used for glaucoma-related segmentation research.

### DRISHTI-GS

DRISHTI-GS is another retinal fundus dataset containing optic disc and optic cup annotations.

Testing across different datasets helps investigate **generalization**.

---

# 🌐 Domain Shift

An important part of the second study is investigating **domain shift**.

A model trained on one retinal dataset may perform differently when evaluated on another dataset because of differences in:

* Image resolution
* Camera/device characteristics
* Illumination
* Image quality
* Patient population
* Annotation styles
* Dataset preprocessing

Conceptually:

```text
Dataset A
    │
    ▼
Training
    │
    ▼
Model
    │
    ▼
Dataset B
    │
    ▼
Performance Change
    │
    ▼
Domain Shift Analysis
```

This helps determine whether the model generalizes beyond its training dataset.

---

# 🆚 Paper 1 vs Paper 2

| Feature              | Paper 1             | Paper 2                         |
| -------------------- | ------------------- | ------------------------------- |
| Main Model           | U-Net               | CSP-SegNet+E                    |
| Initial Segmentation | U-Net               | CSP-SegNet                      |
| Edge Detection       | HED                 | Edge-supervision branch         |
| GVF                  | ✅                   | ❌                               |
| Active Contour       | ✅                   | ❌                               |
| Snake Refinement     | ✅                   | ❌                               |
| Multi-task Learning  | ❌                   | ✅                               |
| Segmentation Head    | ✅                   | ✅                               |
| Edge Head            | HED external branch | Auxiliary branch                |
| OD Segmentation      | ✅                   | ✅                               |
| OC Segmentation      | ✅                   | ✅                               |
| Boundary Refinement  | Active Contour      | Learned edge supervision        |
| CDR Estimation       | ✅                   | Possible downstream application |
| Dice                 | ✅                   | ✅                               |
| IoU                  | ✅                   | ✅                               |
| Precision            | —                   | ✅                               |
| Recall               | —                   | ✅                               |
| HD95                 | ✅                   | ✅                               |
| Domain Shift Study   | —                   | ✅                               |

---

# 🔬 Research Evolution

The two papers represent two different approaches to the same fundamental problem.

### Paper 1

Focuses on combining:

```text
Deep Learning
     +
Edge Detection
     +
Classical Computer Vision
     +
Active Contour
```

The main idea is:

> **Use deep learning to obtain a good initial segmentation and use edge-aware active contour refinement to improve the boundaries.**

---

### Paper 2

Moves toward a more integrated deep-learning solution:

```text
Segmentation Learning
          +
Edge Learning
          ↓
   CSP-SegNet+E
```

The main idea is:

> **Teach the segmentation network about boundaries through an auxiliary edge-supervision task.**

Therefore, Paper 1 relies on **explicit post-processing and contour evolution**, while Paper 2 investigates **joint learning of segmentation and boundary information**.

---

# 📈 Overall Research Workflow

```text
                 RETINAL FUNDUS DATA
                         │
                         ▼
                  DATA PREPARATION
                         │
                         ▼
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
           PAPER 1               PAPER 2
              │                     │
              ▼                     ▼
        U-Net + HED            CSP-SegNet+E
              │                     │
              ▼                     ▼
          GVF + Snake          Edge Supervision
              │                     │
              ▼                     ▼
       Refined Boundary        Learned Boundary
              │                     │
              └──────────┬──────────┘
                         ▼
                 OD / OC SEGMENTATION
                         │
                         ▼
                 QUANTITATIVE METRICS
                         │
                         ▼
                 GENERALIZATION STUDY
                         │
                         ▼
                  CDR / GLAUCOMA
                     ANALYSIS
```

---

# 📁 Repository Structure

A recommended repository organization is:

```text
Glaucoma-OD-OC-Segmentation/
│
├── README.md
├── requirements.txt
│
├── Paper_1/
│   ├── notebooks/
│   ├── preprocessing/
│   ├── unet/
│   ├── hed/
│   ├── gvf/
│   ├── active_contour/
│   ├── evaluation/
│   └── results/
│
├── Paper_2/
│   ├── notebooks/
│   ├── preprocessing/
│   ├── models/
│   ├── edge_supervision/
│   ├── training/
│   ├── evaluation/
│   └── results/
│
├── reports/
│   ├── Paper_1_Report.pdf
│   └── Paper_2_Report.pdf
│
├── figures/
│   ├── architecture/
│   ├── segmentation/
│   ├── edge_maps/
│   └── results/
│
└── datasets/
    └── README.md
```

> Dataset files should generally not be uploaded directly to GitHub if their licenses prohibit redistribution. Provide instructions for obtaining the datasets instead.

---

# 🛠️ Technologies Used

| Category             | Technologies                       |
| -------------------- | ---------------------------------- |
| Programming          | Python                             |
| Deep Learning        | TensorFlow / Keras                 |
| Image Processing     | OpenCV                             |
| Segmentation         | U-Net, CSP-SegNet                  |
| Edge Detection       | HED                                |
| Active Contour       | Parametric Snake                   |
| Vector Field         | GVF                                |
| Numerical Processing | NumPy                              |
| Data Processing      | Pandas                             |
| Visualization        | Matplotlib                         |
| Evaluation           | Dice, IoU, Precision, Recall, HD95 |
| Version Control      | Git / GitHub                       |

---

# 📦 Installation

Clone the repository:

```bash
git clone https://github.com/<your-username>/<your-repository>.git
cd <your-repository>
```

Create a virtual environment:

```bash
python -m venv venv
```

### Windows

```bash
venv\Scripts\activate
```

### Linux / macOS

```bash
source venv/bin/activate
```

Install the required packages:

```bash
pip install -r requirements.txt
```

---

# ▶️ Running the Project

## Paper 1

The Paper 1 pipeline can be executed in the following order:

```text
1. Dataset Preparation
       ↓
2. ROI Extraction
       ↓
3. U-Net Training
       ↓
4. Initial OD/OC Prediction
       ↓
5. Initial Contour Extraction
       ↓
6. HED Edge Detection
       ↓
7. GVF Generation
       ↓
8. Active Contour Refinement
       ↓
9. Final Segmentation
       ↓
10. CDR Estimation
       ↓
11. Evaluation
```

---

## Paper 2

The Paper 2 workflow is:

```text
1. Dataset Preparation
       ↓
2. Preprocessing
       ↓
3. Edge Information Generation
       ↓
4. CSP-SegNet+E Training
       ↓
5. Segmentation Prediction
       ↓
6. Edge Prediction
       ↓
7. Final OD/OC Mask
       ↓
8. Evaluation
       ↓
9. Cross-Dataset Analysis
```

---

# 📊 Results

The repository contains experimental results for OD/OC segmentation.

The primary evaluation metrics include:

```text
Dice Score
IoU
Precision
Recall
HD95
```

Results should be reported separately for:

* Optic Disc
* Optic Cup
* Dataset
* Paper / Model


---

# 🖼️ Visualization

The project can generate visualizations such as:

### Paper 1

```text
Original Image
      ↓
ROI
      ↓
U-Net Prediction
      ↓
HED Edge Map
      ↓
GVF Field
      ↓
Initial Contour
      ↓
Refined Snake
      ↓
Final OD/OC Mask
```

### Paper 2

```text
Original Image
      ↓
Preprocessed Image
      ↓
CSP-SegNet+E
      ↓
Segmentation Prediction
      +
Edge Prediction
      ↓
Final OD/OC Mask
```

These visualizations allow qualitative comparison between predicted boundaries and ground-truth annotations.

---

# 💡 Key Research Insights

The research investigates two complementary philosophies.

### Paper 1 — Explicit Boundary Refinement

```text
U-Net
  ↓
Initial Region
  +
HED
  ↓
Boundary Information
  +
GVF
  ↓
External Force
  +
Snake
  ↓
Refined Boundary
```

This approach explicitly models the boundary refinement process.

---

### Paper 2 — Learned Boundary Awareness

```text
CSP-SegNet
      +
Edge Supervision
      ↓
Joint Feature Learning
      ↓
Boundary-Aware Segmentation
```

Instead of performing active contour refinement after segmentation, boundary information is incorporated directly into model training.

---

# ⚠️ Limitations

Potential limitations of the overall research include:

* Variations in fundus image quality.
* Low contrast between optic cup and disc.
* Blood vessel interference.
* Ambiguous anatomical boundaries.
* Dataset-specific characteristics.
* Domain shift between datasets.
* Dependence on the quality of ground-truth annotations.
* Computational requirements of multi-stage pipelines.
* Sensitivity of active contour methods to initialization and parameters in Paper 1.

---

# 🚀 Future Work

Future research can focus on:

1. Developing a unified end-to-end OD/OC segmentation framework.
2. Improving cross-dataset generalization.
3. Incorporating stronger attention mechanisms.
4. Improving edge supervision.
5. Learning adaptive boundary representations.
6. Reducing dependency on manually tuned active contour parameters.
7. Combining the advantages of CSP-SegNet+E with contour refinement.
8. Evaluating on larger and more diverse retinal datasets.
9. Incorporating vessel-aware features.
10. Extending the system toward complete automated glaucoma risk assessment.
11. Improving CDR estimation using both vertical and horizontal measurements.
12. Performing clinical validation with expert ophthalmologist annotations.

---

# 🧪 Research Contribution

The overall research investigates two approaches for improving automatic OD/OC segmentation.

### Contribution of Paper 1

A hybrid framework combining:

[
\boxed{
U\text{-}Net + HED + GVF + Active\ Contour
}
]

where:

* **U-Net** provides initial segmentation.
* **HED** provides edge information.
* **GVF** generates an external force field.
* **Active Contour** refines the predicted boundary.

---

### Contribution of Paper 2

An edge-aware segmentation framework:

[
\boxed{
CSP\text{-}SegNet + Edge\ Supervision
}
]

where:

* **CSP-SegNet** performs semantic segmentation.
* **Edge Head** provides auxiliary boundary supervision.
* Both tasks are learned jointly.

---

# 🔍 Comparison of the Research Ideas

```text
                 PAPER 1
                    │
                    ▼
             Initial Segmentation
                    │
                    ▼
              Explicit Edges
                    │
                    ▼
                   GVF
                    │
                    ▼
             Active Contour
                    │
                    ▼
             Refined Boundary


                 PAPER 2
                    │
                    ▼
              CSP-SegNet+E
                    │
             ┌──────┴──────┐
             ▼             ▼
       Segmentation       Edge
           Head           Head
             │             │
             └──────┬──────┘
                    ▼
           Boundary-Aware
             Segmentation
```

The first approach performs **explicit contour refinement**, whereas the second approach attempts to learn **boundary-aware segmentation directly within the neural network**.

---

# 📚 Research Areas

This project lies at the intersection of:

* 🧠 Deep Learning
* 👁️ Medical Image Analysis
* 🩻 Biomedical Image Segmentation
* 🔍 Edge Detection
* 🐍 Active Contour Models
* 📐 Image Processing
* 🌐 Domain Generalization
* 🧬 Computer-Aided Diagnosis
* 👓 Glaucoma Screening

---

# 👨‍💻 Author

**Gyandeep Talukder**

**B.Tech — Computer Science and Engineering**
Jaypee University, Guna

Research Internship
Indian Institute of Information Technology Design and Manufacturing, Jabalpur

---

# 📜 Citation

If you use this work in academic research, please cite the corresponding research implementation.

```bibtex
@misc{talukder_od_oc_segmentation,
  title  = {Glaucoma Detection Through Optic Disc and Optic Cup Segmentation},
  author = {Gyandeep Talukder},
  year   = {2026},
  note   = {Research Project on Retinal Fundus Image Segmentation}
}
```

---

# 📄 License

This repository is intended for **academic and research purposes**.

Please follow the individual licensing and usage requirements of the retinal datasets and pretrained models used in this project.

---

# ⭐ Final Pipeline

The complete research work can be summarized as:

```text
                         RETINAL FUNDUS IMAGE
                                  │
                                  ▼
                           PREPROCESSING
                                  │
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
                 PAPER 1                     PAPER 2
                    │                           │
                    ▼                           ▼
          U-Net + HED + GVF             CSP-SegNet+E
                    │                           │
                    ▼                           ▼
             Active Contour              Edge Supervision
                    │                           │
                    ▼                           ▼
           Boundary Refinement          Boundary-Aware
                    │                    Segmentation
                    │                           │
                    └─────────────┬─────────────┘
                                  ▼
                         OD / OC SEGMENTATION
                                  │
                                  ▼
                       DICE / IoU / PRECISION
                       RECALL / HD95
                                  │
                                  ▼
                         CDR ESTIMATION
                                  │
                                  ▼
                    GLAUCOMA-RELATED ANALYSIS
```

## 🔬 Research Summary

**Paper 1:**

> *U-Net provides the initial segmentation, HED extracts boundary information, GVF creates the external force field, and the active contour refines the OD/OC boundaries.*

**Paper 2:**

> *CSP-SegNet performs OD/OC segmentation while an auxiliary edge branch provides boundary supervision to encourage boundary-aware feature learning.*

Together, these experiments explore both **explicit boundary refinement** and **learned edge-aware segmentation** for automated retinal OD/OC segmentation.
