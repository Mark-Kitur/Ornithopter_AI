# 📑 AI/ML Documentation: Autonomous Object Detection & Avoidance System

### **Project Version: DRL-Vision-v1.0**
* **Code Repository:** `https://github.com/Mark-Kitur/Ornithopter_AI`

---

## 🧭 I. Explanation: System Overview & Architecture

### A. Project Goal

The primary goal of the AI/ML system is to enable the Ornithopter to perform **autonomous navigation** by safely detecting and identifying critical obstacles and dynamic objects in its flight path.

The target objects for detection are:
1.  **Quelea (Dynamic Obstacle)**
2.  **Trees (Static Obstacle)**
3.  **Houses (Static Obstacle/Boundary)**

The **Movement of the Ornithopter** is controlled by the **DRL Agent**, which processes the detection results to execute collision avoidance and optimal path planning actions.

### B. Hardware & Frameworks

| Category | Detail |
| :--- | :--- |
| **Onboard Compute Hardware** | **Raspberry Pi** |
| **Core ML Frameworks** | **PyTorch** (for Object Detection), **Gymnasium Framework** (for DRL environment API) |
| **Resource Constraints** | [***TBD: Specify required inference latency (e.g., need >5 FPS), memory footprint, and power budget.***] |

### C. Input/Output (I/O)

The system is split into two modules: perception (Vision) and decision (DRL).

| Module | Input Source | Output Format |
| :--- | :--- | :--- |
| **Vision Module** | Onboard Camera Feed (RGB video frames) | List of Bounding Boxes: `[(class_id, confidence, x_min, y_min, x_max, y_max), ...]` |
| **RL Agent** | **Observation Space:** Processed vision output (e.g., *Distance to closest obstacle*), plus platform state (e.g., *Current attitude, velocity*). | **Action Space:** Control signals for movement (e.g., *Pitch/Roll/Yaw adjustments*, *Throttle change*). |

---

## 🧠 II. Reference: Model Details

### A. Model Types

* **Vision Module:** [***TBD: Specify the exact object detection model architecture (e.g., MobileNet-SSD, YOLOv5/v8-Nano).***]
* **Decision-Making Model (RL Agent):** **Deep Reinforcement Learning (DRL)**. The training environment uses the **Gymnasium API standard**.

### B. Training Data

* **Data Source:** **RoboFlow**
* **Data Characteristics:** 10 GB (`The Dataset size`)

### C. Performance Metrics & Current Results

The following metrics are derived from the training of the object detection model (Epoch 10 results).

| Metric | Context | Current Result (Epoch 10) |
| :--- | :--- | :--- |
| **Training Loss (Train Loss)** | The error of the model on the training data. | $\approx 0.0742$ |
| **Validation Loss (Val Loss)** | The error of the model on unseen validation data. | $\approx 0.0160$ |
| **Validation IoU** | **Intersection over Union**. Measures the overlap quality of the predicted bounding boxes against the true boxes. | $\approx 0.3711$ |
| **Validation Accuracy** | The classification accuracy of the model on the validation set. | $1.0000$ |
| **Test IoU** | IoU performance on the final, un-trained test set. | $\approx 0.3398$ |
| **Test Accuracy** | Accuracy performance on the final test set. | $\approx 0.934$ |

> **Note:** An IoU of 0.3711 suggests bounding box localization could be improved, while the $1.0000$ Validation Accuracy indicates the classification task is likely either trivial or the validation set is very small/unrepresentative.

### D. Risks and Limitations

| Detail | Status/Placeholder |
| :--- | :--- |
| **Known Risks/Limitations** | Low inference FPS in low light, poor performance with very small/distant objects, thermal throttling |
| **Real-World Performance** | The system has not yet been deployed in the real world. Performance is based on simulation and lab testing only. |

---

## 🛠️ III. How-To Guide: MLOps and Maintenance

### A. Deployment Methods

The system supports a flexible approach to deployment:

* **Onboard:** The compiled model runs directly on the Ornithopter's **Raspberry Pi** for real-time inference and control, ensuring autonomy.
* **Offboard:** Model runs on a ground station PC with commands relayed wirelessly (using MAVLink) to the Ornithopter. (At the moment we haven't decided on the protocol to be used, MAVLink is the best alternative at the moment.)

### B. Retraining Trigger

The official condition to initiate a model update or full retraining is:

* **Poor Inference:** Any monitored degradation in real-time inference quality, such as:
    * A drop in measured **IoU** on a standardized test set below [***TBD: Threshold value***].
    * Consistent reports of missed detections or false positives during flight logs.
    * A significant drop in the DRL agent's episodic reward score in the simulation environment.

### C. Testing Methodology

The system requires rigorous testing before deployment to ensure both the perception (Vision Module) and decision-making (DRL Agent) components function reliably together, especially on the resource-constrained Raspberry Pi hardware.

| Step | Description | Purpose |
| :--- | :--- | :--- |
| **1. Unit Testing** | Standard software testing for individual components: <br> - **Vision I/O:** Testing image capture, pre-processing, and the output format of bounding boxes. <br> - **DRL Action Mapping:** Verifying that the DRL agent's output actions (e.g., NumPy array) are correctly mapped to physical actuator commands (e.g., PWM signals). | Ensures core code blocks and data transformations are correct and bug-free. |
| **2. Module Integration Testing (Simulated)** | Testing the complete software stack (Vision $\to$ DRL Agent $\to$ Actuator Command) using **simulated sensor inputs** (images and state data) and logging the resulting commands without involving physical hardware. | Verifies seamless data flow and logical connections between the two AI modules. |
| **3. Hardware-in-the-Loop (HIL) Simulation** | The **Raspberry Pi** runs the actual inference code and flight controller, while being connected to a **physics simulator** (e.g., Gazebo, AirSim). The simulator sends virtual sensor data, and the RPi sends control commands back to the simulator. | Tests the system's real-time performance and robustness *under hardware constraints* (latency, memory) before actual flight. |
| **4. Stress & Edge Case Testing** | Running the HIL simulation through challenging scenarios, including low-light conditions, fast-moving objects (Quelea), close-range obstacles, and unexpected sensor noise. | Identifies failure modes and confirms the system's stability at the boundaries of its operating envelope. |
### D. Code Location

* **Model Weights Location:** `https://drive.google.com/drive/folders/1gZvWHtuy1BcPmhduW4v2LNhbg89RJEPR?usp=drive_link`
* **Deployed Version:** a0b955819354d6fee8f475899b5d5424f08731a8