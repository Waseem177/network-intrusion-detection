# Explainable LSTM-Based Network Intrusion Detection System
**Rule-Based vs LSTM vs Cross-Attack Generalisation with SHAP**

**Institution:** Sathyabama Institute of Science and Technology  
**Department:** Computer Science and Engineering  
**Author:** Mohamed Waseem  
**Dataset:** CICIDS2017 — Canadian Institute for Cybersecurity  

---

## What This Project Does

This project builds and compares two approaches to detecting network attacks, 
then investigates whether an AI model trained on one attack type can detect 
attacks it has never seen — explained using SHAP.

- **Phase 1 — Rule-Based Detection:** 5 detection rules mapped to MITRE ATT&CK
- **Phase 2 — LSTM Neural Network:** Deep learning trained on DDoS traffic
- **Phase 3 — SHAP Explainability:** Opens the black box for SOC analysts
- **Phase 4 — Cross-Attack Generalisation:** Zero-day detection experiment

---

## Results

### Phase 1 vs Phase 2

| Metric | Rule-Based | LSTM | Improvement |
|---|---|---|---|
| Accuracy | 37.0% | 99.75% | +62.75% |
| Precision | 36.0% | 100.0% | +64.0% |
| Recall | 41.0% | 99.5% | +58.5% |
| F1-Score | 34.0% | 100.0% | +66.0% |
| AUC | ~0.50 | 0.9999 | +0.4999 |

Both tested on the same 225,745 real network flows — a direct, fair comparison.

### Phase 4 — Cross-Attack Generalisation (Zero-Day Experiment)

Model trained on DDoS only. Tested on 4 unseen attack types:

| Attack Type | Accuracy | AUC | F1 | Generalises? |
|---|---|---|---|---|
| DoS (Wednesday) | 77.0% | 0.70 | 0.54 | ✅ Partially |
| Brute Force (Tuesday) | 96.8% | 0.45 | 0.00 | ❌ No |
| Web Attacks (Thursday) | 98.6% | 0.37 | 0.00 | ❌ No |
| Port Scan (Friday) | 44.5% | 0.55 | 0.00 | ❌ No |

---

## Key Finding

SHAP analysis reveals the model over-relies on **ACK Flag Count** — a 
DDoS-specific signature. DoS is partially detected due to volumetric 
similarity. Application-layer attacks (brute force, web attacks) share 
no overlapping feature signatures with DDoS, causing complete detection 
failure. SHAP makes this failure mode visible and actionable for security teams.

---

## Dataset

**CICIDS2017** — Canadian Institute for Cybersecurity  
Download: https://www.kaggle.com/datasets/cicdataset/cicids2017

| File | Attack Type | Rows |
|---|---|---|
| Friday-Afternoon-DDos | DDoS (train) | 225,745 |
| Tuesday | Brute Force | 445,909 |
| Wednesday | DoS | 692,703 |
| Thursday Morning | Web Attacks | 170,366 |
| Friday Afternoon PortScan | Port Scan | 286,467 |

---

## Detection Rules — Phase 1

| Rule | Condition | MITRE ATT&CK |
|---|---|---|
| HIGH_PACKET_RATE | Flow Packets/s > 1,000 | T1498 |
| HIGH_BYTE_RATE | Flow Bytes/s > 100,000 | T1498.001 |
| SHORT_BURST_FLOOD | Duration < 5000µs & Fwd Packets > 50 | T1499 |
| SYN_FLOOD | SYN > 1 & ACK = 0 | T1498.001 |
| ONE_WAY_FLOOD | No backward packets & Fwd > 20 | T1498 |

---

## LSTM Architecture — Phase 2

```
Input Layer  → 78 scaled network features (MinMaxScaler)
LSTM Layer   → 64 units, sequence learning
Dropout      → 30% (prevents overfitting)
Dense Layer  → 32 units, ReLU activation
Output Layer → 1 unit, Sigmoid (Attack / Benign)

Parameters:  38,721
Train:       180,596 samples
Test:        45,149 samples
Epochs:      10
```

---

## SHAP Top Features — Phase 3 & 4

| Attack Type | #1 Feature | #2 Feature | #3 Feature |
|---|---|---|---|
| DDoS (trained) | ACK Flag Count | Destination Port | Avg Packet Size |
| DoS | ACK Flag Count | Fwd IAT Std | Flow IAT Std |
| Brute Force | min_seg_size_forward | ACK Flag Count | Init_Win_bytes_forward |
| Web Attacks | Init_Win_bytes_backward | ACK Flag Count | Init_Win_bytes_forward |
| Port Scan | ACK Flag Count | Init_Win_bytes_forward | Destination Port |

---

## Files

| File | Description |
|---|---|
| `LSTM_Intrusion_Detection.ipynb` | Main notebook — all 4 phases |
| `lstm_clean_model.keras` | Trained LSTM model |
| `shap_cross_attack_comparison.png` | Cross-attack SHAP chart (key result) |
| `shap_feature_importance_clean.png` | DDoS SHAP bar chart |
| `shap_summary_clean.png` | SHAP summary dot plot |
| `roc_curve.png` | ROC curve (AUC 0.9999) |
| `comparison_chart.png` | Rule-based vs LSTM comparison |

---

## How to Run

**Requirements:**
```bash
pip install pandas matplotlib seaborn scikit-learn tensorflow shap jupyter
```

1. Download CICIDS2017 CSVs from Kaggle and place in project folder
2. Open `LSTM_Intrusion_Detection.ipynb` in Jupyter
3. Run all cells in order (Phases 1 → 4)

---

## Relation to Security Frameworks

- **MITRE ATT&CK:** Rules mapped to T1498 and T1499
- **ISC2 CC Domain 4:** Network Security — anomaly monitoring
- **ISC2 CC Domain 5:** Security Operations — incident detection

---

## Technologies

Python 3.11 · TensorFlow/Keras · SHAP · Scikit-learn · Pandas · Matplotlib · Jupyter
