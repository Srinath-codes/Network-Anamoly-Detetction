# Network Anomaly Detection Dashboard

A Flask dashboard for classifying network traffic as normal or as one of four broad attack groups: DoS, Probe, R2L, and U2R. The application combines Random Forest, XGBoost, LightGBM, and CatBoost probabilities and provides manual, CSV batch, demo, and packet-capture workflows.

## What it does

- Classifies connection features using a soft-voting ensemble.
- Shows confidence, per-model votes, and suggested defensive follow-up.
- Supports batch CSV analysis and synthetic sample traffic.
- Provides a live-capture interface using Scapy where packet capture is available.
- Keeps analysis history in local JSON files created at runtime.

## How the model is trained

`train_model.py` downloads the NSL-KDD 20% training file, groups its labels into five categories, trains four classifiers, and writes the model and preprocessing artifacts expected by the dashboard. The training data and generated model files are intentionally not committed.

```bash
python -m venv .venv
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
# macOS/Linux
# source .venv/bin/activate
python -m pip install -r requirements.txt
python train_model.py
python app.py
```

Open http://127.0.0.1:5000 after the server starts.

## Batch input

Use `sample_bulk_traffic.csv` as a small example of the expected feature columns. The exact feature list is saved as `feature_names.joblib` during training. `generate_sample.py` can also create a randomized CSV after training.

## Important limitations

- The labels and model come from NSL-KDD, an older benchmark dataset. Results on this dataset do not establish effectiveness against current network traffic.
- Live packet features are approximations derived from individual packets and short rolling windows; they are not a faithful reconstruction of all NSL-KDD connection features.
- Predictions and remediation suggestions are demonstrations. Do not use them as a production intrusion detection or incident response system.
- Packet capture may require administrator/root privileges, a supported capture driver, and authorization for the network being monitored. Use only on networks you are allowed to inspect.
- The training script evaluates on a random split of the dataset; it does not report a held-out time-based or cross-dataset evaluation.

## Project files

- `app.py` — Flask routes, model loading, prediction and dashboard workflows
- `sniffer.py` — packet capture and approximate feature extraction
- `train_model.py` — dataset download, preprocessing and model training
- `generate_sample.py` — randomized batch CSV generation
- `templates/`, `static/` — dashboard interface
- `backend_architecture.html` — architecture overview

## Runtime files

Model artifacts and analysis histories are generated locally. They are excluded from version control by `.gitignore`.

