
# Data Dictionary — Challenger Insight EEG × Engagement (5,000 rows)

**File:** `challenger_insight_eeg_segments_5000.csv` (exactly 5,000 segment-level rows)

Each row is a ~10s EEG segment with learner context, survey anchors, band powers, derived ratios, and labels for engagement/disengagement modeling.

## Identifiers & Time
- `subject_id` (str): synthetic learner ID.
- `session_id` (str): synthetic session ID.
- `segment_idx` (int): index of the ~10s window within a session.
- `timestamp` (datetime, naive): segment wall-clock time.

## Learner Context
- `role` (cat): job role (e.g., Software Engineer, Data Scientist).
- `team` (cat): team label (e.g., Platform, AI/ML).
- `modality` (cat): learning delivery mode.
- `stimulus_type` (cat): activity type (Lecture, Code-Along, etc.).
- `task_difficulty` (cat): Easy/Moderate/Hard.
- `prior_knowledge_lvl` (1–5): self-rated baseline.

## Survey Anchors (session-level, copied per segment)
- `self_report_engagement_5pt` (1–5)
- `self_report_fatigue_5pt` (1–5)

## EEG Features (arbitrary power units, consistent scale)
- `delta_power`, `theta_power`, `alpha_power`, `beta_power`, `gamma_power` (float)
- `theta_alpha_ratio`, `beta_alpha_ratio`, `theta_beta_ratio` (float)
- `spectral_entropy` (0–1, normalized)

## Artifacts
- `eye_blink_artifact`, `muscle_artifact`, `motion_artifact` (0/1)

## Targets / Labels
- `cognitive_load_score` (0–1): engineered continuous target.
- `engaged_label` (0/1): Bernoulli draw from engagement probability.
- `disengagement_risk` (0–1): complement-with-artifacts proxy.

## Recommendation
- `recommended_trigger` (0=None, 1=Micro-break, 2=Switch modality, 3=Adaptive difficulty)

---

**File:** `challenger_insight_session_summary.csv`

Per-session aggregates: start/end time, number of segments, mean cognitive load, % engaged, and mean disengagement risk.

---

### Modeling Starters
- **Dimensionality reduction:** PCA/t-SNE on standardized EEG bands + ratios.
- **Classification:** Predict `engaged_label` using Logistic Regression / RF; evaluate Accuracy, Precision, Recall, F1, ROC/AUC.
- **Early-warning:** Calibrate threshold on `disengagement_risk` for trigger recommendations.
- **Fairness & Ethics:** Check distributions by role/team/modality; document consent & privacy assumptions in your report.

### Data Notes
- Band-power relationships and artifact effects are engineered to be plausible but synthetic.
- Distributions are scaled for **<200 MB** workflows and Google Colab friendliness.
- Timestamps are monotonic within sessions; segments are ~10s windows.

