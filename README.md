# Process Mining for Dwelling Time Analysis and SOP Optimization

This repository contains the Jupyter Notebook and supporting python scripts for analyzing cargo/container **Dwelling Time** using Process Mining techniques. By leveraging event logs and applying process discovery algorithms, the project aims to identify operational bottlenecks, evaluate process model quality, and formulate data-driven Standard Operating Procedure (SOP) recommendations.

---

## 📌 Project Overview

**Dwelling Time** (the duration between cargo being unloaded and leaving the terminal) is a key performance indicator (KPI) in logistics and port operations. This project analyzes an event log to:
1. **Measure Dwelling Time:** Calculate the duration between `DISCHARGE` and `TRUCK_OUT` activities for each case.
2. **SOP Segregation:** Group cases into two categories based on a **3-day KPI threshold**:
   * **Compliant Cases (≤ 3 Days):** Representing the target optimal path (frequent behavior).
   * **Delayed Cases (> 3 Days):** Representing path deviations and bottleneck sources (infrequent/delayed behavior).
3. **Process Discovery:** Discover and visualize process structures using **Inductive Miner (IM)**, **Heuristic Miner (HM)**, and **Alpha Miner (AM)**.
4. **Model Evaluation:** Evaluate the discovered Petri Nets based on **Fitness, Precision, Generalization, Simplicity**, and soundness using **WOFLAN**.
5. **Bottleneck Analysis:** Highlight specific activities (e.g., `CUSTOMS_DEL`) causing operational delays and generate recommendations.

---

## ⚙️ Analysis Workflow & Implementation

### 1. Data Loading & Understanding
* Mount Google Drive (if run on Colab) and load the XES event log: `green_log_no_plp.xes (2).gz`.
* Convert the log into a Pandas DataFrame to inspect schemas and retrieve unique case variants.
* Discover start and end activities and calculate the **Mean Case Duration** across the entire log.

### 2. Data Preparation & KPI-based Filtering
* Calculate dwelling time for each case (defined as the elapsed time from the `DISCHARGE` event to the `TRUCK_OUT` event).
* Segregate cases:
  * **Compliant Log (≤ 3 Days):** Cases meeting the dwelling time KPI.
  * **Delayed Log (> 3 Days):** Cases exceeding the 3-day threshold.
* Analyze process variants and frequencies for both logs to capture normal vs. anomalous behavior.

### 3. Process Discovery Algorithms
Three miners are executed and compared to find the most representative model:
* **Inductive Miner (IM):** Guarantees sound models, handles noise thresholds, and discovers block-structured process models.
* **Heuristic Miner (HM):** Considers frequency of activities and relations to filter out low-frequency paths (noise).
* **Alpha Miner (AM):** Classic process discovery algorithm mapping event ordering directly into Petri Nets.

### 4. Model Evaluation & Comparison
For each discovered Petri Net, the notebook automatically assesses:
* **Fitness:** How well the model can replay the log traces.
* **Precision:** How much behavior allowed by the model is actually observed in the log.
* **Generalization:** How well the model explains unseen cases.
* **Simplicity:** How easy the Petri Net is to read and interpret.
* **Soundness:** Soundness validation using the WOFLAN engine.

---

## 📊 Key Findings & SOP Recommendations

Based on the performance metrics and process graphs discovered:

### 1. SOP Segmentation
* **Core SOP (≤ 3 Days Model):** 
  Reflects the normative, standard flow. Cases meeting the KPI are streamlined with minimal noise, fewer loop deviations, and efficient administrative tasks.
* **Exception SOP (> 3 Days Model):**
  Identifies rare or complex pathways that require special handling, escalations, or specific process exceptions.
* **Comprehensive Reference (Original Log Model):**
  Serves as the general overview for audit trials, training, and overall performance evaluations.

### 2. Operational Bottlenecks
* **`CUSTOMS_DEL` (Customs Delivery/Release) Delay:**
  The **Over 3 Days** and **Original Log** models reveal that `CUSTOMS_DEL` is the major bottleneck. When this activity is delayed, the overall dwelling time spikes past the 3-day limit. Optimizing the turnaround time for this activity is the highest priority for improving KPI compliance.

### 3. Activity Sequencing Rule
* **Credit Check Sequence:**
  Analysis shows that in the compliant flow (SOP Utama/frequent log), the **`check credit`** activity must be completed *before* running `calculate capacity` and `check system`. Following this specific sequence correlates with faster case resolutions.

---

*This process mining research is designed to align business processes with terminal operation performance indicators.*
