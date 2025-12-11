
---

## 📌 Project Overview

This project examines how well a Chemprop-based antimalarial drug discovery model generalizes across two biologically distinct disease contexts:

1. **Dengue NS3 Protease** – a low-data, early-stage neglected disease target  
2. **EGFR kinase (Lung Cancer)** – a mature oncology target with abundant SAR data  

The goal is to understand how **biological readiness**, **dataset maturity**, and **chemical diversity** shape AI model performance, and what this reveals about the equitable use of AI in global health (SDGs).

---

## 🧪 1. Baseline: Antimalarial Drug Discovery (Open Source Malaria Series 4)


I reproduced the Chemprop binary classification baseline (D-MPNN classifier ) for the OSM Series 4 dataset as the foundation for all later comparisons. Chemprop was selected because the original OSM/Ersilia study also used it to train model. 

The reported Chemprop performance is: mean AUC = 0.7856 (SD 0.0596).
My reproduced results achieve mean AUC = 0.7481, which is within ~5% of the published value and shows a similar fold-to-fold pattern. This confirms that the data curation, featurisation, and training setup were faithfully replicated.

| Metric | Reported | Reproduced |
|--------|----------|------------|
| ROC-AUC | 0.785 | 0.746 |
| PRC-AUC | 0.595 | 0.636 |


  - Generates candidate analogues  


The full REINVENT workflow is not fully reproducible within the constraints of this coursework;  
instead, a simplified contextualized generative demonstration is included.

---

## 🇹🇼 2. Taiwan Contextualization and Goals

To evaluate whether the malaria-trained workflow can generalise across disease domains in Taiwan, I contextualised the baseline architecture to two biologically and socially relevant targets with contrasting data characteristics:

- **Dengue - Taiwan’s recurring public-health threat**  


Dengue fever is a mosquito-borne viral infection similar to malaria in transmission dynamics.
Taiwan’s warming climate and expanding mosquito habitats lead to recurrent seasonal outbreaks, especially in southern regions.
Lack of effective antivirals keeps dengue a persistent public-health burden.

- **EGFR-positive lung cancer**  

Lung cancer is the leading cause of cancer mortality in Taiwan.
EGFR mutations are especially prevalent in East-Asian non-smokers, creating strong biomedical demand for targeted therapies.
EGFR therefore represents a high-impact, clinically validated domain for model testing.
More evaluation on two chosen context:

**Further Reasoning behind chosen two Contextx**

    **Dataset Insight**

    | Malaria | Dengue |  EGFR  | 
    |---------|--------|--------| 
    |   314   |   353  | 10,466 |

Dengue and malaria dataset are similar in size.
Dengue and malaria occupy similar chemotype space (molecule structure diversity), while EGFR is much chemotype space broader and richer. 
Potency distributions differ strongly:
    - Dengue → very few actives
    - Malaria → moderate actives
    - EGFR → many strong inhibitors
    - Dataset maturity is therefore expected to be a major determinant of model performance.
    
Dengue and EGFR were selected because they represent **opposite ends of data maturity**, providing a controlled way to test whether a malaria-trained workflow can generalise across Taiwan-relevant health challenges.


---


## ⚙️ 3. Adaptations for Model Architecture

Different disease areas have different therapeutic potency expectations, which directly influence the **IC₅₀ cutoff used for binary classification**.  
As a result, each dataset in this project uses a threshold aligned with its **biological maturity, clinical standards, and discovery stage**, leading to different proportions of “active” compounds.

### Malaria (OSM Series 4)
- Mid-stage hit optimisation → moderate potency expectation  
- **IC₅₀ cutoff: 2.5 µM** (standard in OSM literature)  
- **Active (%)**: ~27%  
- Rationale: reflects the potency typically achievable during antimalarial hit development.

### Dengue NS3 Protease
- Early-stage, weak-signal discovery → much lower potency observed  
- **IC₅₀ cutoff: 10 µM**  
- **Active (%)**: ~14%  
- Rationale: early dengue inhibitors rarely reach sub-micromolar potency; a permissive cutoff prevents discarding all meaningful SAR signal.

### EGFR Kinase (Lung Cancer)
- Clinically validated target with potent inhibitors  
- **IC₅₀ cutoff: 0.1 µM**  
- **Active (%)**: ~32%  
- Rationale: reflects precision-oncology potency requirements and literature standards for EGFR inhibitor performance.


---

## 📈 5. Model Performance & Evaluation

## **5.1 Dengue (NS3 Protease)**

**Overall ROC-AUC: 0.616  
PRC-AUC: ~0.21  
Fold behaviour: highly unstable**

The Dengue model shows **weak predictive ability and major instability across folds**, reflecting fundamental limitations in the underlying dataset.

### 🔍 **Why performance is weak**
1. **Early-stage biology → highly noisy assays**  
   - IC₅₀ values are inconsistent across sources.  
   - Some compounds are measured only once, reducing reliability.  
   - Low potency ranges compress the dynamic range of labels.

2. **Sparse SAR → weak learning signal**  
   - Unlike malaria or EGFR, Dengue has few coherent chemical series.  
   - Chemprop struggles to extract patterns from fragmented SAR.

3. **Severe class imbalance**  
   - Very few compounds meet the “active” threshold (10 µM).  
   - PRC-AUC drops sharply because the model cannot identify rare actives.

4. **Label inconsistency across serotypes (Type 1/3/4)**  
   - The merged dataset treats activity as if it were uniform across serotypes.  
   - Biological differences introduce noise that the model interprets as overlapping classes.

5. **Data scarcity → high variance in CV splits**  
   - With ~353 compounds, each fold loses too much information when held out.  
   - Fold-to-fold ROC-AUC varies widely, confirming model brittleness.

### 📌 **Interpretation**
The Dengue results illustrate a core challenge in **neglected disease drug discovery**:  
**data quantity and quality—not architecture—are the primary bottleneck**.

Machine learning is limited not by Chemprop, but by:
- assay variability,  
- low-potency chemical matter,  
- inconsistent SAR,  
- and insufficient sample size.

These findings emphasise why global-health modelling requires **robust uncertainty estimation, data augmentation, and transfer learning**, not just model scaling.

---

## **5.2 Lung Cancer (EGFR)**

**ROC-AUC ≈ 0.88 (std ≈ 0.018)  
PRC-AUC ≈ 0.93  
Performance: strong, consistent, reproducible**

In contrast to Dengue, the EGFR model demonstrates **high accuracy and excellent fold stability**.

### ⭐ **Why performance is strong**
1. **Rich and coherent SAR landscape**  
   - EGFR inhibitors form well-defined medicinal-chemistry series.  
   - Clear structure–activity patterns improve model discriminability.

2. **Large dataset → robust generalisation**  
   - >10,000 compounds across multiple scaffolds.  
   - Each cross-validation fold still contains thousands of training points.

3. **High-quality, validated assays**  
   - EGFR is a clinically studied target with consistent assay protocols.  
   - Label reliability is significantly higher than in Dengue.

4. **Potent chemical matter**  
   - Many sub-nanomolar to sub-micromolar inhibitors create a strong signal.  
   - Clear separation between active and inactive classes boosts ROC & PRC.

### ⚠️ **Key limitation: scaffold bias**
Although performance is high, it **does not imply model creativity**.

- The dataset is dominated by congeneric kinase-like scaffolds (e.g., quinazolines).  
- The model primarily performs **interpolation**, not **extrapolation**.  
- Novel chemotypes—crucial for overcoming resistance—may be deprioritised.  

This highlights the gap between **predictive accuracy** and **actual medicinal-chemistry value**.

### 📌 **Interpretation**
The EGFR results demonstrate how Chemprop excels when:
- SAR is dense and coherent,  
- assay quality is high,  
- and potency distributions are favourable.

But they also show why **high AUC ≠ real innovation**:  
models trained on homogeneous datasets tend to stay inside the known chemical space.

---

## **5.3 Comparative View (Dengue → Malaria → EGFR)**

| Dataset | Size | Potency Strength | SAR Structure | Expected ML Performance |
|--------|------|------------------|---------------|--------------------------|
| **Dengue** | Very small | Weak | Fragmented | **Low, unstable** |
| **Malaria** | Moderate | Moderate | Coherent | **Moderate, stable** |
| **EGFR** | Very large | Strong | Well-defined | **High, consistent** |

This gradient demonstrates a core conclusion of the project:

> **Dataset maturity—not model choice—is the dominant determinant of predictive success.**

---

## **5.4 What This Means for Taiwan**

- **Dengue modelling** highlights the difficulty of using AI for neglected diseases with limited data.  
  → Suggests future directions: active learning, transfer learning, and uncertainty quantification.

- **EGFR modelling** supports Taiwan’s precision-medicine needs and biotech innovation.  
  → Shows where AI can provide immediate practical value.

Together, these results illustrate how AI tools must be **matched to the data reality of each disease**, especially in contexts where resources differ dramatically.

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


