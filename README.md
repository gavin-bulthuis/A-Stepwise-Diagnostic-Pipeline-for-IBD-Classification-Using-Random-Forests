# DSCI 4093 Capstone: A Stepwise Diagnostic Pipeline for IBD Classification

This repository contains the source code for a multi-stage machine learning framework designed to optimize the diagnosis of Inflammatory Bowel Disease (IBD).

Traditional IBD diagnosis is often inefficient, requiring patients to undergo a litany of tests before receiving a definitive endoscopy. To address this, this project implements a **tiered Random Forest classifier** that progressively integrates groupings of features—from non-invasive risk factors to invasive procedural results—to predict disease status. This mirrors real-world clinical decision-making, balancing diagnostic accuracy with cost and patient burden.

## CRITICAL USAGE WARNING

**Do not click "Run All" or "Knit" without manual configuration.**

This RMarkdown file (`dsci4093_final_model.Rmd`) is designed for **interactive, stepwise execution**. It is NOT a fully automated pipeline.

To prevent overwriting saved models with incorrect data, you **MUST** manually configure the Feature Selection and Output Filename for the specific tier you intend to train before running the training chunks.

---

## How to Train a Specific Model

Follow these steps to train and save a model for a specific clinical tier (e.g., `t1t2` - Risk Factors + Symptoms).

### 1. Configure Feature Selection

In the **Model Building** section of the RMarkdown file, locate the chunk that creates the `data` object. You must manually edit the `select()` function to include the specific feature sets you want to test.

```r
# Example: Training Tier 1 (Risk Factors) + Tier 2 (Symptoms)
data <- ibd_data %>%
  select(all_of(t1_features), all_of(t2_features)) 
  # ^ EDIT THIS LINE to change feature combinations (e.g., remove t1_features)

```

### 2. Configure Output Filename

In the **Store results of individual models** section (immediately following the training loop), you **MUST** update the `current_prefix` variable to match your selection above.

**If you forget this step, you will overwrite an existing `.rds` model file with the wrong data.**

```r
# !!! CHANGE THIS PREFIX FOR EACH MODEL !!!
current_prefix <- "t1t2"  # Change this string to match your features (e.g., "t1", "t2t3")

saveRDS(rf_model_final, file = paste0(current_prefix, ".rds"))

```

### 3. Execution Strategy

**Sequential Setup:** Run the "Setup," "Data Prep," and "Tier Definition" chunks first to load libraries and process the raw data .


* **Train One by One:** Configure the code for a specific tier (e.g., `t1`), run the training chunks, and save the model. Then repeat the process for the next tier (e.g., `t1t2`).
* **Simulations:** The "Simulation" sections rely on pre-trained `.rds` files. Ensure you have trained and saved all necessary models (`t1.rds`, `t1t2.rds`, etc.) *before* running any simulation chunks.



---

## Clinical Tiers & Features

The features are grouped into four tiers of increasing complexity and cost.

**Tier 1: Risk Factors (Demographics & History)** 


* *Examples:* BMI, Smoking Status, Antibiotic Use, Family History.
* *Goal:* Establish baseline susceptibility.


**Tier 2: Patient-Reported Symptoms** 


* *Examples:* Stool frequency, Abdominal pain, Fatigue, Well-being.
* *Goal:* Capture active signs of disease via low-cost office visits.

 
**Tier 3: Biomarkers** 


* *Examples:* Fecal Calprotectin, C-Reactive Protein (CRP).
* *Goal:* Provide objective measurements of inflammation without invasive procedures.


**Tier 4: Endoscopy & Histology** 


* *Examples:* Dysbiosis Score, Macroscopic Inflammation, Ulcerations.
* *Goal:* Definitive diagnosis ("Ground Truth").



## Key Results

**Symptom Utility:** Tier 2 (Symptoms) and Tier 3 (Biomarkers) combined offer the most cost-efficient diagnostic path, achieving high accuracy without requiring colonoscopy for all patients.


**Feature Importance:** Patient-reported symptoms like "recent diarrhea" and biomarkers like "fecal calprotectin" were consistently the most predictive features.


**Simulation Performance:** In a resource-constrained simulation (only 15% of patients scoped), the model correctly diagnosed 96% of patients by prioritizing high-risk cases for endoscopy.

## Project Guidance

This project was guided by Julian Wolfson, PhD from the University of Minnesota's School of Public Health.

## Data Privacy & Ethics

**This repository contains code only.**

The analysis utilizes data from the IBD Multi'omics Database. While the data is de-identified, it contains sensitive health information.


## Dependencies

This project uses **R** and the following key packages:

* `ranger` (Random Forest implementation)
* `caret` (Training and cross-validation)
* `fastshap` & `shapviz` (Model interpretability)
* `tidyverse` (Data manipulation)
* `pROC` (AUC metrics)
