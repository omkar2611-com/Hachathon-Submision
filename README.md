Detects traffic anomalies from Los Angeles loop-detector speeds and exports a modified CSV with:

anomaly_score_0_100 – calibrated 0–100 anomaly score

is_anomaly – final alert (1/0) tuned for high precision

top_contributing_features – 3 sensor IDs that most influenced the alert

decision_threshold, target_precision – for transparency & reproducibility

Why precision? The data is extremely imbalanced. I optimize for few false alarms; accuracy is not reported because it’s misleading in rare-event settings.

Dataset:

Name: METR-LA traffic speed dataset

Source: Kaggle (public mirror)

Format used: .h5 (time series) + .pkl (graph/metadata) → processed into a Pandas DataFrame → CSV with anomaly scores

Each column = sensor ID, each row = timestamped snapshot of speeds (mph).

Method:

Load & clean raw METR-LA (fill missing, handle inf/NaN).

Normalize features across sensors.

Unsupervised pseudo-labels: IsolationForest with very low contamination (~0.5%) to mark rare outliers.

Supervised detector: Train XGBoost on pseudo-labels, with scale_pos_weight for class imbalance.

Threshold for precision: Pick a probability cutoff that achieves a target precision (e.g., 0.98) on validation.

Explain & export: Compute per-row z-scores and save the 3 biggest contributors; export CSV with all original columns + new fields above.
