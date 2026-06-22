# Intelligent Autonomous Crack Inspection Robot with LLM-Based Reasoning

## Project Overview

This project presents an intelligent robotic inspection system that combines computer vision, autonomous navigation, and Large Language Model (LLM) reasoning to automate pavement crack assessment in industrial environments.

Built on the TurtleBot3 Waffle Pi platform using ROS Noetic and Gazebo, the system performs real-time crack detection through a YOLOv8 vision pipeline and leverages Google's Gemini LLM to provide semantic interpretation of detected defects. Beyond identifying cracks, the robot can understand natural language instructions, classify defect severity, generate inspection insights, and support human-robot interaction during inspection tasks.

By integrating perception, reasoning, and control into a unified architecture, the project transforms a traditional detection robot into an intelligent inspection assistant capable of supporting infrastructure monitoring and maintenance operations.

---

## Key Features

### Computer Vision-Based Crack Detection

- Real-time pavement crack detection using YOLOv8
- Bounding box visualization with confidence scores
- Live image processing from TurtleBot3 camera streams

### LLM-Powered Inspection Reasoning

- Google Gemini integration for contextual defect analysis
- Severity assessment categorized as:
  - Minor
  - Moderate
  - Severe
- Human-readable inspection explanations

### Natural Language Robot Control

- Command robot movements using simple text instructions
- Supports directional navigation and emergency stop commands
- Enables intuitive human-robot interaction

### Autonomous Inspection Behaviour

- Robot automatically pauses upon crack detection
- Captures inspection evidence for analysis
- Publishes structured findings through ROS topics

### Modular ROS Architecture

- Independent perception and reasoning nodes
- Real-time communication through ROS publishers and subscribers
- Easily extendable for additional sensors and inspection tasks

---

## System Architecture

```text
User Command
      │
      ▼
 Gemini LLM Interface
      │
      ▼
 Motion Controller
      │
      ▼
 TurtleBot3 Navigation
      │
      ▼
 RGB Camera Feed
      │
      ▼
 YOLOv8 Crack Detection
      │
      ▼
 Crack Region Extraction
      │
      ▼
 Gemini Severity Analysis
      │
      ▼
 Inspection Report Generation
      │
      ▼
 ROS Topic Publication
```

---

## Technology Stack

| Component | Technology |
|------------|------------|
| Robot Platform | TurtleBot3 Waffle Pi |
| Middleware | ROS Noetic |
| Simulation Environment | Gazebo 11 |
| Programming Language | Python |
| Object Detection | YOLOv8 |
| AI Reasoning | Google Gemini |
| Computer Vision | OpenCV |
| Communication | ROS Topics |

---

## Project Structure

```text
turtle_crack_ws2/
│
├── build/
├── devel/
│
├── src/
│   ├── crack_detection/
│   │   ├── launch/
│   │   │   └── crack_inspection.launch
│   │   │
│   │   ├── scripts/
│   │   │   ├── yolo_detector.py
│   │   │   └── llm_decision_node.py
│   │   │
│   │   ├── models/
│   │   │   └── best.pt
│   │   │
│   │   ├── config/
│   │   │   └── params.yaml
│   │   │
│   │   ├── CMakeLists.txt
│   │   └── package.xml
│   │
│   └── CMakeLists.txt
│
└── .catkin_workspace
```

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Ubuntu | 20.04 LTS |
| ROS | Noetic Ninjemys |
| Python | 3.8+ |
| Gazebo | 11 |
| TurtleBot3 Packages | Latest (Noetic) |
| YOLOv8 | ≥ 8.0 |
| Google Generative AI SDK | Latest |
| OpenCV | Latest |

---

## Installation

### 1. Clone the Repository

```bash
git clone <repository-url> ~/turtle_crack_ws2
cd ~/turtle_crack_ws2
```

### 2. Install ROS Dependencies

```bash
sudo apt update

sudo apt install ros-noetic-turtlebot3 \
                 ros-noetic-turtlebot3-gazebo \
                 ros-noetic-turtlebot3-simulations \
                 python3-catkin-tools
```

### 3. Install Python Dependencies

```bash
pip install ultralytics
pip install google-generativeai
pip install opencv-python
pip install numpy
```

### 4. Build the Workspace

```bash
cd ~/turtle_crack_ws2

catkin_make

source devel/setup.bash
```

### 5. Configure TurtleBot3 Model

```bash
echo "export TURTLEBOT3_MODEL=waffle_pi" >> ~/.bashrc
source ~/.bashrc
```

---

## Configuration

### Gemini API Key

Generate an API key from Google AI Studio and export it as an environment variable:

```bash
export GEMINI_API_KEY="your_api_key_here"
```

To make it persistent:

```bash
echo 'export GEMINI_API_KEY="your_api_key_here"' >> ~/.bashrc
source ~/.bashrc
```

### YOLOv8 Weights

Place the trained model file inside:

```text
src/crack_detection/models/best.pt
```

Update the path in `yolo_detector.py` if required:

```python
model = YOLO("/path/to/turtle_crack_ws2/src/crack_detection/models/best.pt")
```

---

## Running the System

### Terminal 1 – Launch Gazebo

```bash
source ~/turtle_crack_ws2/devel/setup.bash

roslaunch turtlebot3_gazebo turtlebot3_world.launch
```

### Terminal 2 – Start YOLO Detection

```bash
source ~/turtle_crack_ws2/devel/setup.bash

rosrun crack_detection yolo_detector.py
```

### Terminal 3 – Start Gemini Reasoning

```bash
source ~/turtle_crack_ws2/devel/setup.bash

rosrun crack_detection llm_decision_node.py
```

### Terminal 4 – Visualize Detection Results

```bash
rosrun rqt_image_view rqt_image_view
```

### Launch Everything Using a Single Launch File

```bash
source ~/turtle_crack_ws2/devel/setup.bash

roslaunch crack_detection crack_inspection.launch
```

---

## Robot Commands

The robot accepts natural language movement instructions through the LLM interface.

| Command | Action |
|----------|---------|
| forward | Move forward |
| backward | Move backward |
| left | Turn left |
| right | Turn right |
| stop | Stop immediately |

When a crack is detected, the robot automatically halts to perform inspection and severity assessment.

---

## ROS Topics

| Topic | Message Type | Description |
|---------|---------|-------------|
| `/camera/rgb/image_raw` | sensor_msgs/Image | Raw camera feed |
| `/camera/yolo_image` | sensor_msgs/Image | YOLO detection output |
| `/crack_detection/crack_image` | sensor_msgs/Image | Cropped crack region |
| `/crack_detection/severity` | std_msgs/String | Severity classification |
| `/cmd_vel` | geometry_msgs/Twist | Robot movement commands |

---

## Core Modules

### YOLO Detection Node (`yolo_detector.py`)

The perception module processes incoming camera frames and performs crack detection using a trained YOLOv8 model.

Responsibilities include:

- Real-time crack localization
- Bounding box generation
- Confidence score estimation
- Image annotation
- Crack region extraction

Detected crack regions are forwarded to the reasoning module for analysis.

---

### Gemini Reasoning Node (`llm_decision_node.py`)

The reasoning module acts as the intelligence layer of the system.

Upon receiving a detected crack image, Gemini evaluates the defect and provides a contextual severity assessment.

Functions include:

- Severity classification
- Context-aware reasoning
- Inspection report generation
- Robot stop command publication
- Detection event logging

---

## Experimental Outcomes

The integration of Gemini introduces a reasoning layer on top of traditional object detection. Rather than simply identifying defects, the robot can interpret inspection findings, classify severity levels, and communicate meaningful results to human operators.

### Demonstrated Capabilities

- Autonomous crack detection
- AI-assisted severity assessment
- Natural language robot interaction
- Automated inspection reporting
- Real-time ROS-based deployment

---

## Limitations

- Requires internet connectivity for Gemini API access
- Severity assessment depends on image quality
- Performance may degrade under poor lighting conditions
- Evaluation was primarily conducted in simulation environments

---

## Future Enhancements

Potential improvements include:

- Multi-crack tracking and prioritization
- GPS-assisted inspection logging
- Voice-based command interface
- On-device LLM deployment
- Maintenance scheduling integration
- Real-world industrial deployment

---

## Authors

| Name | Student ID |
|--------|------------|
| Nancy Anne | A202459 |
| Kavi Priya A/P Balasupramaniam | A202660 |

**Lecturer:** Dr. Anahita Ghazvini
**Course:** TTTC3413 – Robot Applications  
**Institution:** Universiti Kebangsaan Malaysia (UKM)

---

## References

- Ai, D., Jiang, G., Kei, L. S., & Li, C. (2018). Automatic pixel-level pavement crack detection using information of multi-scale neighborhoods. *IEEE Access*, 6, 24452–24463.
- Cuevas, D., & Manrique, R. (2025). Evaluating LLM-Based Autonomous Systems. *ICAI 2025*.
- Kim, Y., Kim, D., Choi, J., Park, J., Oh, N., & Park, D. (2024). A Survey on Integration of Large Language Models with Intelligent Robots. *Intelligent Service Robotics*, 17(5), 1091–1107.
- Marian, M., et al. (2020). A ROS-Based Control Application for a Robotic Platform Using the Gazebo 3D Simulator. *ICCC 2020*.
- Team Gemini et al. (2023). Gemini: A Family of Highly Capable Multimodal Models. *arXiv:2312.11805*.

---

### Built With

**ROS Noetic • TurtleBot3 • YOLOv8 • Google Gemini • OpenCV • Gazebo**
