# System Architecture: Solar-Thermal System Identification Framework

## 1. Introduction and Scope
This document defines the architectural separation of concerns for the Solar-Thermal System Identification framework. The system is designed to perform robust parameter identification of the physical heating system using Reinforcement Learning (RL), while also supporting high-level usage scenarios.

The architecture is divided into five primary subsystems, ensuring modularity, testability, and clear interface definitions.

## 2. Subsystems and Responsibilities

### 2.1. Plant Modeling Subsystem (The Physics Engine)
**Responsibility:** To accurately simulate the thermal dynamics of the physical solar-thermal heating system based on known physical laws. This subsystem provides the 'ground truth' model against which parameter identification and state estimation is performed.
**Key Components:**
*   **Collector Model:** Calculates solar energy absorption, considering irradiance, angle of incidence, and collector efficiency (which contains parameters like $\eta_{collector}$).
*   **Storage Tank Model:** Simulates thermal mass, stratification, and heat transfer within the storage tank.
*   **Heat Exchanger Model:** Models the thermal exchange efficiency between the collector loop and the storage tank.
*   **Control Logic Model:** Simulates the operation of actuators (pump, backup heater) based on defined control algorithms (e.g., deadband, setpoints).
**Inputs:** External data (Irradiance, Ambient Temperature), Control Actions (Pump ON/OFF).
**Outputs:** System State variables (Temperature profiles, flow rates, Energy inventory).

### 2.2. Plant Data Acquisition Subsystem (The Observer)
**Responsibility:** To collect, synchronize, preprocess, and validate raw data from the physical system sensors. It acts as the interface between the real world and the computational models.
**Key Components:**
*   **Sensor Interface Layer:** Handles communication protocols (e.g., reading from temperature sensors, flow meters).
*   **Data Synchronization & QA:** Ensures temporal alignment of data from disparate sources (e.g., synchronizing temperature and irradiance readings). Performs outlier detection and missing data imputation.
*   **Data Pipeline:** Buffers and formats data into a standardized format suitable for both RL training and real-time display.
**Inputs:** Raw sensor readings (Temperature, Flow, Irradiance).
**Outputs:** Cleaned, time-series data streams.

### 2.3. RL Parameter Identification Subsystem (The Learner)
**Responsibility:** To use real-world or simulated data to estimate and refine the unknown physical parameters of the Plant Model Subsystem (e.g., $U_{collector}, k_{demand}$). It utilizes RL principles to minimize the error between the model's prediction and the observed data.
**Key Components:**
*   **RL Agent:** The learning algorithm (e.g., Bayesian Inference, Gradient Descent variant) that suggests parameter adjustments.
*   **Reward/Loss Function:** Measures the discrepancy between the model's output and the actual measured state.
*   **Optimization Engine:** Manages the training loop, hyperparameter tuning, and convergence criteria.
**Inputs:** Cleaned System State Data (from Subsystem 2.2), Model Definition (from Subsystem 2.1).
**Outputs:** Optimized parameter set ($\Theta_{identified}$).

### 2.4. Model-Based State Estimation Subsystem (The Predictor)
**Responsibility:** To provide real-time estimates of internal, unmeasurable system states (e.g., instantaneous thermal mass, unmeasured heat loss) by running the Plant Model Subsystem in an estimation loop, often using techniques like Kalman Filtering or observer patterns.
**Key Components:**
*   **State Estimator:** Integrates current measurements with the dynamic model and the latest identified parameters ($\Theta_{identified}$).
*   **Prediction Model:** Utilizes the deterministic core of the Plant Model to project future states.
**Inputs:** Real-time Sensor Data, Identified Parameters ($\Theta_{identified}$).
**Outputs:** Estimated System State ($\hat{X}$).

### 2.5. Usage Subsystem (The Interface)
**Responsibility:** To translate complex system data and parameters into actionable insights for the end-user or for guiding higher-level control decisions.
**Interface Options:**
1.  **Web Client/Presentation:** Presents real-time data streams, parameter confidence intervals, and historical performance metrics via a responsive dashboard (utilizing SSE).
2.  **Higher-Level Controller (HLC):** Provides an API endpoint allowing an external application (e.g., a scheduling service) to query predictions and recommended control strategies (e.g., "Optimal pump run time for next 6 hours given forecasted demand and weather").
**Inputs:** Estimated State ($\hat{X}$), Identified Parameters ($\Theta_{identified}$), Demand Schedules (External input).
**Outputs:** User Display/API response; Optimal Control Signals to Subsystem 2.1.

## 3. Subsystem Interactions (Data Flow)

1.  **Data Flow (Observation $\to$ Learning):**
    `Subsystem 2.2 (Data Acquisition)` $\rightarrow$ `Subsystem 2.1 (Plant Model)` $\rightarrow$ `Subsystem 2.3 (RL Identification)` (for training/evaluation)
    `Subsystem 2.2 (Data Acquisition)` $\rightarrow$ `Subsystem 2.4 (State Estimation)` $\rightarrow$ `Subsystem 2.5 (Usage)` (for real-time display/control)

2.  **Control Flow (Usage $\to$ Plant):**
    `Subsystem 2.5 (Usage/HLC)` $\rightarrow$ `Subsystem 2.1 (Plant Model)` (sending optimal action commands).

3.  **Parameter Flow (Learning $\to$ Use):**
    `Subsystem 2.3 (RL Identification)` $\rightarrow$ `Subsystem 2.4 (State Estimation)` (providing the refined model parameters).

## 4. Technology Stack Recommendations
*   **Modeling/Simulation:** Python (NumPy, SciPy, Pandas).
*   **RL/Optimization:** TensorFlow/PyTorch, Scikit-learn.
*   **Data Handling:** Kafka/Redis (for streaming, if scaling beyond single machine).
*   **Web Interface:** Flask/FastAPI (backend), React/Vue (frontend) utilizing WebSockets/SSE.