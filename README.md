# Multi-Scenario Adaptive Traffic Light Crossing Violation Detection System

An adaptive computer vision-based traffic light violation detection system designed to gracefully handle varying road infrastructure conditions, such as faded road markings, absence of traffic lights, and camera shake. This system automatically assesses real-time visual environmental conditions and routes processing through one of three optimal adaptive detection scenarios.

## 🌟 Key Features

* **Quick Scan Module**: Automatically analyzes the first 30 frames to evaluate infrastructure availability by counting traffic lights and zebra crossings, and assesses camera stability using the Lucas-Kanade Optical Flow method.
* **Adaptive Multi-Scenario Architecture**:
    * **Scenario 1 (Complete Infrastructure)**: Performs spatial-based trajectory mapping when the infrastructure is complete and clearly visible.
    * **Scenario 2 (Partial Infrastructure/Dynamic Camera)**: Uses virtual zone inference to deduce traffic light status based on the ratio of stopped versus moving vehicles when infrastructure is incomplete or the camera is unstable.
    * **Scenario 3 (Pure Behavioral Consensus)**: Operates entirely without physical infrastructure by relying on collective vehicle behavior consensus to form a persistent virtual anchor line for violation detection.
* **Vehicle Coordinate Stabilization**: Applies an Exponential Moving Average (EMA) filter with a 0.7 coefficient to eliminate bounding box jitter from YOLO detections, enabling highly accurate velocity calculations.
* **Automated Evidence Export**: Automatically captures violation frames complete with a red bounding box visualization on the violator, and archives them in a `.zip` format complete with vehicle ID and timestamp.

## 🛠️ Technology Stack

* **Object Detection**: Dual YOLOv8 models, consisting of a standard YOLOv8m for classifying vehicles and traffic lights, and a Custom YOLOv8s for classifying zebra crossings and stop lines.
* **Multi-Object Tracking (MOT)**: ByteTrack for managing trajectories and dynamic ID assignments without heavy computational overhead.
* **Color Classification**: HSV Color Space Segmentation, selected for its robustness to lighting variations.
* **Core Libraries**: Python 3.x, Ultralytics, PyTorch, OpenCV, Numpy, and Roboflow.

## 📦 Prerequisites & Installation

By default, this system's development environment utilizes Kaggle GPU infrastructure (Tesla P100/T4) to achieve a stable 24-30 fps. However, the inference stage can also be smoothly executed in a capable local environment, such as a device with 16GB RAM.

1. Clone this repository:
```bash
   git clone https://github.com/cvl-kelompok-3/traffic-light-crossing-violation.git
   cd traffic-light-crossing-violation
   ```

2. Install all required dependencies:
```bash
   pip install ultralytics roboflow opencv-python-headless numpy torch
   ```

## 🚀 Usage Guide

This system is designed to be fully automated. You only need to run the Master Code script to detect violations adaptively.

1. Ensure the model files are in the correct directory:
   * `yolov8m.pt` (Can be downloaded automatically via the ultralytics library)
   * `best.pt` (Result of fine-tuning on a specific road infrastructure dataset)
2. Change the `INPUT_VIDEO_PATH` variable in the execution file or Jupyter Notebook to point to the target `.mp4` file location.
3. Run the Master Code execution cell.
4. **Output**:
   * The system will display the chosen scenario decision in the terminal/console.
   * The processed video with metric overlays will be saved in the working directory.
   * All captured violation photos will be extracted into the `violations/` folder and compressed into a `.zip` archive file.

## 🔬 Evaluation & Current Limitations

Based on testing, the Quick Scan module's adaptation accuracy reached 97.9%. However, the current system still has several areas for future improvement:

* **Tailgating Violator Problem (Scenario 3)**: Vehicles following very closely behind the main violator sometimes inherit inertia memory values through the queue immunity mechanism, causing false negatives. Future development will include Kinematic Profile Analysis (jerk derivative) to distinguish between legitimate acceleration and violation pursuit.
* **Y-Coordinate Crossing Bypass**: In low fps videos (<15 fps) or with very high-speed vehicles, temporal resolution can fail. Future solutions will focus on sub-frame trajectory interpolation.
* **Inference Errors in Heavy Congestion (Scenario 2)**: Stop-and-go movement patterns during severe congestion can trigger a RED light false positive detection.

## 📂 Full Input and Output Data

Due to GitHub file size limitations, the complete experimental files are provided through external cloud storage.

The full resources include:

* Input traffic videos
* Output detection videos
* Violation capture results
* Experimental outputs

## 🔗 Download Links

### Input and Output Videos

[https://drive.google.com/drive/folders/1HWTXJNhX1vs4BY64GP-5-eaR-rGgyHot?usp=drive_link]

## 📌 Notes

Please download the required files before running the project locally.

The repository only contains:

* Source code
* Trained YOLO models
* Documentation
* IEEE report
* Configuration files

Large files are stored externally to maintain repository efficiency and comply with GitHub storage limitations.
