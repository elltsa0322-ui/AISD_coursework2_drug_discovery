
---

## 📌 Project Overview

This project examines how well a Chemprop-based antimalarial drug discovery model generalizes across two biologically distinct disease contexts:

1. **Dengue NS3 Protease** – a low-data, early-stage neglected disease target  
2. **EGFR kinase (Lung Cancer)** – a mature oncology target with abundant SAR data  

The goal is to understand how **biological readiness**, **dataset maturity**, and **chemical diversity** shape AI model performance, and what this reveals about the equitable use of AI in global health (SDGs).

---

## 🧪 1. Baseline: Antimalarial Drug Discovery (Open Source Malaria Series 4)

The baseline pipeline reproduces the OSM Series 4 classifier using Chemprop MPNN:

- **Train Predictor (Chemprop)**
  - 315 molecules  
  - Graph neural network predicts IC₅₀-based activity  

- **Generative Agent (REINVENT + classifier)**
  - RL agent learns SMILES grammar  
  - Reward based on Chemprop predictions  
  - Generates candidate analogues  

### **Reproduced Performance (within ±5% of paper)**

| Metric | Reported | Reproduced |
|--------|----------|------------|
| ROC-AUC | 0.785 | 0.746 |
| PRC-AUC | 0.595 | 0.636 |

The full REINVENT workflow is not fully reproducible within the constraints of this coursework;  
instead, a simplified contextualized generative demonstration is included.

---

## 🇹🇼 2. Taiwan Contextualization

### **Dengue**
- Taiwan experiences recurrent outbreaks.  
- Very limited antiviral options → significant public health burden.  
- Dataset small, noisy, and early-stage → weak SAR signal.

### **Lung Cancer (EGFR)**
- Leading cancer mortality in Taiwan.  
- EGFR mutations highly prevalent in East Asia.  
- Rich medicinal chemistry history → ideal for precision-medicine modeling.

---

## ⚙️ 3. Adaptations for Model Architecture

### **3.1 Activity Thresholds (IC₅₀ → binary labels)**

| Dataset | Therapeutic Rationale | IC₅₀ Cutoff | Active % |
|---------|------------------------|-------------|----------|
| Malaria | Moderate potency | ≤ 2.5 μM | 66% |
| Dengue | Early-stage hit ID | ≤ 10 μM | 33% |
| EGFR   | Targeted therapy | ≤ 0.1 μM | 55% |

### **3.2 Hyperparameter Adaptation**

- Dengue ≈ Malaria → similar dataset size and diversity  
- EGFR dataset is **30× larger**, broader chemical diversity → stronger regularization required  
  - Larger batch size  
  - More epochs  
  - Added dropout  

---

## 📊 4. Dataset Insights

### **Dataset Sizes**

| Malaria | Dengue | EGFR |
|---------|--------|-------|
| 314 | 353 | 10,466 |

### **Observations**
- Dengue and Malaria cluster closely → similar chemotypes  
- EGFR spans a far broader chemical space  
- Potency profiles differ:
  - Dengue → few actives  
  - Malaria → moderate actives  
  - EGFR → many strong inhibitors  

Dataset maturity strongly influences model performance.

---

## 📈 5. Model Performance & Evaluation

### **5.1 Dengue (NS3 Protease)**

**Overall ROC-AUC: 0.616  
PRC-AUC: ~0.21  
Folds: highly unstable**

**Why?**
1. **Early-stage biology → noisy assays**  
2. **Sparse SAR → weak learning signal**  
3. **Severe class imbalance**  
4. **Label consistency issues across serotypes**

---

### **5.2 Lung Cancer (EGFR)**

**ROC-AUC ≈ 0.88 (std ≈ 0.018)**  
**PRC-AUC ≈ 0.93**

Strong, stable performance due to:
- Rich SAR landscape  
- Mature medicinal chemistry  
- High-quality, validated assay data  

**Limitation:**  
The dataset is dominated by **congeneric scaffolds**, so performance largely reflects **interpolation**.  
Novel or diverse chemotypes may be neglected, limiting true discovery potential.

---

## 🌍 6. Discussion and Critical Reflections on SDGs

### **Dengue – Scientific Readiness and Equity Challenges (SDG 3.b, SDG 17)**

Dengue’s weak modeling outcomes highlight that progress still depends on the quality and maturity of scientific data.  
This shows that scientists remain essential in advancing early-stage discovery for less-studied diseases, where meaningful progress ultimately requires **cross-disciplinary collaboration** and **open data sharing**.

### **EGFR – Precision Medicine & Innovation Ecosystems (SDG 3.4, SDG 9.5)**

EGFR provides a contrasting example: mature biology and well-validated chemistry enable strong modeling outcomes.  
This not only supports precision-medicine development but also demonstrates how AI integration can strengthen **local biotech competitiveness** and **resilient innovation ecosystems**.

Together, these findings reveal how AI can either reinforce existing inequities or accelerate innovation depending on underlying biological readiness.

---


