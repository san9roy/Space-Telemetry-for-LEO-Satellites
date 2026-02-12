# Space-Telemetry-for-LEO-Satellites - Satellite Telemetry Simulation & Anomaly Detection Framework
This project presents a physics-inspired spacecraft telemetry simulation and anomaly detection framework that models power, thermal, and communication subsystems in Low Earth Orbit. It combines engineering-based thresholds and machine learning to analyze synthetic telemetry and study fault detection and spacecraft health monitoring.

## Introduction

This project presents a physics-inspired spacecraft telemetry simulation and anomaly detection framework. It models realistic Low Earth Orbit (LEO) housekeeping telemetry for power, thermal, and communication subsystems, and evaluates both engineering-based and machine learning approaches for fault detection.

Modern spacecraft continuously generate telemetry to monitor subsystem health. Detecting anomalies in this telemetry is essential for mission reliability, fault diagnosis, and autonomous operations. This project integrates spacecraft systems modeling with data-driven analysis techniques.

---

# Aim of the Project

The objectives of this project are:

- To simulate physically realistic spacecraft telemetry using simplified but scientifically grounded subsystem models.
- To inject interpretable fault scenarios with known ground-truth labels.
- To implement and compare rule-based and machine learning anomaly detection methods.
- To quantitatively evaluate detection performance.
- To investigate the impact of operational context (eclipse vs sunlit, in-view vs out-of-view) on anomaly interpretation.

---

# Problems Addressed

The framework addresses several spacecraft engineering challenges:

1. **Power Subsystem Degradation**
   - Battery internal resistance increase
   - Voltage sag under load

2. **Thermal Control Anomalies**
   - Heater malfunction
   - Abnormal temperature excursions

3. **Communication Degradation**
   - Reduced link quality during ground passes
   - Packet loss due to fading or interference

4. **Sensor Corruption**
   - Random measurement spikes

5. **Incomplete Telemetry**
   - Packet loss during contact windows
   - Handling missing data during analysis

---

# Methodology

The project follows a structured modeling and analysis workflow.

## 1. Orbital & Illumination Modeling

A ~90-minute LEO orbit is simulated with configurable eclipse fraction. A `sunlit` flag represents illumination conditions.

## 2. Power System Simulation

An 8S Li-ion battery pack model includes:

- State of Charge (SoC) updated via coulomb counting
- Open-Circuit Voltage (OCV) vs SoC relationship
- Internal resistance voltage drop
- Charge in sunlight and discharge in eclipse
- Payload duty cycling

Outputs:
- `soc_true`
- `current_A_true`
- `voltage_true`

## 3. Thermal System Modeling

A first-order thermal model is implemented:

dT/dt = (T_env − T)/τ + P_diss/C_th

Where:
- T_env varies with orbital position
- τ is the thermal time constant
- P_diss depends on electrical load

Output:
- `temp_true`

## 4. Communications Modeling

The communications subsystem includes:

- Periodic ground station contact windows (`in_view`)
- Link quality (SNR-like dB proxy)
- Packet success threshold logic

Packet loss is applied only during contact windows to reflect realistic downlink behavior.

Outputs:
- `linkq_true_db`
- `comm_ok`
- `in_view`

## 5. Fault Injection

Realistic subsystem faults are injected:

- Battery degradation (voltage sag)
- Heater malfunction (temperature increase)
- RF degradation (link-quality reduction)
- Random sensor glitches

Ground truth anomaly labels are stored in `label_gt`.

## 6. Measurement Noise & Missing Data

Gaussian measurement noise is added to all telemetry channels. Packet loss during in-view periods results in missing values that require interpolation.

## 7. Signal Processing

Telemetry is processed using:

- Rolling median filtering for spike suppression
- 1D Kalman smoothing for noise reduction

Filtered channels are used for anomaly detection.

## 8. Anomaly Detection

Two approaches are implemented:

### Rule-Based Detection
- Engineering thresholds
- Jump detection using rolling medians
- Context-aware link anomaly logic

### Isolation Forest
- Feature scaling
- Unsupervised anomaly detection
- Tuned contamination parameter

## 9. Evaluation

Performance metrics include:

- True Positives (TP)
- False Positives (FP)
- False Negatives (FN)
- Precision
- Recall
- F1 score

Evaluation is performed across the full timeline and during in-view periods only.

---

# Tools Used

- Python
- NumPy
- Pandas
- Matplotlib
- Scikit-learn (IsolationForest, StandardScaler, Pipeline)

Conceptual foundations include spacecraft power systems modeling, thermal system dynamics, signal filtering, and unsupervised anomaly detection.

---

# Analysis

The simulated telemetry exhibits realistic subsystem behavior:

- Voltage shows eclipse cycling and degradation events.
- Temperature demonstrates orbital sinusoidal modulation with injected thermal faults.
- Link quality peaks during contact windows and degrades during RF fault periods.
- Packet loss rates during in-view periods remain low and realistic.

Rule-based detection achieves high recall but low precision, reflecting conservative FDIR behavior. Isolation Forest demonstrates moderate performance and highlights challenges of applying unsupervised ML to structured periodic spacecraft signals.

---

# Findings

1. Rule-based detection is sensitive but prone to false positives.
2. Machine learning performance depends strongly on feature scaling and contamination assumptions.
3. Operational context significantly affects anomaly interpretation.
4. Structured orbital signals can reduce unsupervised ML effectiveness if not explicitly modeled.

---

# Interpretation

The project highlights real spacecraft operations challenges:

- Many anomalies are context-dependent.
- Threshold tuning is mission-specific.
- Per-sample anomaly classification can overestimate false positives.
- Event-based detection may provide more operationally meaningful results.

The framework demonstrates how combining physical system modeling with data-driven analysis leads to more interpretable anomaly detection strategies.

---

# Conclusion

This project integrates spacecraft subsystem simulation with anomaly detection methodologies. It provides a foundation for:

- Autonomous spacecraft health monitoring
- Event-based anomaly clustering
- Physics-informed machine learning
- Advanced Fault Detection, Isolation, and Recovery (FDIR) systems

---

# Future Work

Potential extensions include:

- Event-based anomaly clustering
- Solar array current modeling
- Reaction wheel telemetry simulation
- Residual-based model-informed detection
- Sequence-based (LSTM) anomaly models
- Adaptive threshold optimization

