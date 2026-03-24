# Automated Network Intrusion Detection System
### Rule-Based Detection vs LSTM Neural Network

**Dataset:** CICIDS2017 — Friday Afternoon DDoS  
**Institution:** Sathyabama Institute of Science and Technology  
**Department:** Computer Science and Engineering

---

## What This Project Does

This project builds and compares two approaches to detecting DDoS attacks in real network traffic:

- **Phase 1 — Rule-Based Detection:** A Python script that analyses network flows and flags suspicious traffic using 5 detection rules mapped to MITRE ATT&CK techniques.
- **Phase 2 — LSTM Neural Network:** A deep learning model trained on the same data to learn attack patterns automatically — without any hardcoded rules.

The goal was to prove, with real data, that AI-based detection significantly outperforms rule-based detection.

---

## Results

| Metric | Rule-Based | LSTM | Improvement |
|--------|-----------|------|-------------|
| Accuracy | 37.0% | 99.79% | +62.79% |
| Precision | 36.0% | 100.0% | +64.0% |
| Recall | 41.0% | 99.5% | +58.5% |
| F1-Score | 34.0% | 100.0% | +66.0% |
| AUC Score | ~0.50 | 0.9999 | +0.4999 |

Both phases were tested on the **same 225,745 real network flows** — making this a direct, fair comparison.

---

## Dataset

**CICIDS2017** — Canadian Institute for Cybersecurity  
File used: `Friday-WorkingHours-Afternoon-DDos.pcap_ISCX.csv`  
Download: https://www.unb.ca/cic/datasets/ids-2017.html

- 225,745 network flows
- 128,027 DDoS attack flows (56.7%)
- 97,718 benign flows (43.3%)
- 78 network features per flow

---

## Detection Rules (Phase 1)

| Rule | Condition | MITRE ATT&CK |
|------|-----------|--------------|
| HIGH_PACKET_RATE | Flow Packets/s > 1,000 | T1498 |
| HIGH_BYTE_RATE | Flow Bytes/s > 100,000 | T1498.001 |
| SHORT_BURST_FLOOD | Duration < 5000µs & Fwd Packets > 50 | T1499 |
| SYN_FLOOD | SYN > 1 & ACK = 0 | T1498.001 |
| ONE_WAY_FLOOD | No backward packets & Fwd > 20 | T1498 |

---

## LSTM Architecture (Phase 2)

```
Input Layer     → 78 scaled network features (MinMaxScaler)
LSTM Layer      → 64 units, sequence learning
Dropout Layer   → 30% dropout (prevents overfitting)
Dense Layer     → 32 units, ReLU activation
Output Layer    → 1 unit, Sigmoid (Binary: Attack / Benign)

Total parameters: 38,721
Training samples: 180,596
Test samples:     45,149
Epochs:           10
```

---

## Files

```
network_project/
│
├── Network_Threat_Profiling.ipynb     # Phase 1 — Rule-based detection
├── LSTM_Intrusion_Detection.ipynb     # Phase 2 — LSTM model
│
├── security_dashboard.png             # 4-panel detection charts
├── security_heatmap.png               # Feature correlation heatmap
├── roc_curve.png                      # ROC-AUC curve (AUC = 0.9999)
├── threat_alerts.csv                  # 45,514 flagged flows
├── lstm_intrusion_detection.keras     # Saved LSTM model
└── findings_report.txt                # Detection findings report
```

---

## How to Run

**Requirements:**
```bash
pip install pandas matplotlib seaborn scikit-learn tensorflow jupyter
```

**Step 1 — Download dataset:**  
Download `Friday-WorkingHours-Afternoon-DDos.pcap_ISCX.csv` from CICIDS2017 and place it in the project folder.

**Step 2 — Run Phase 1 (Rule-Based):**  
Open `Network_Threat_Profiling.ipynb` in Jupyter and run all cells.

**Step 3 — Run Phase 2 (LSTM):**  
Open `LSTM_Intrusion_Detection.ipynb` in Jupyter and run all cells.

---

## Key Findings

1. Rule-based detection only caught **37%** of attacks — fixed thresholds cannot adapt to real attack variability.
2. The LSTM model achieved **99.79% accuracy** and **AUC 0.9999** — detecting virtually every attack with near-zero false alarms.
3. The **62.79% accuracy improvement** on identical data proves AI-based NIDS is practically superior to rule-based approaches.
4. The ROC curve hugging the top-left corner confirms the model almost never raises a false alarm and almost never misses a real attack.

---

## Technologies Used

- Python 3.11
- Pandas, NumPy — data processing
- Matplotlib, Seaborn — visualisation
- Scikit-learn — preprocessing and evaluation
- TensorFlow / Keras — LSTM model
- Jupyter Notebook — development environment

---

## Relation to Security Frameworks

- **MITRE ATT&CK:** Detection rules mapped to T1498 and T1499
- **ISC2 CC Domain 4:** Network Security — monitoring for anomalies
- **ISC2 CC Domain 5:** Security Operations — incident detection and response

---

## Author

**Mohamed Waseem**  
Sathyabama Institute of Science and Technology  
Department of Computer Science and Engineering
