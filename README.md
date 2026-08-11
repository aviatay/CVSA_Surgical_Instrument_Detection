# 🔬 Surgical Instrument Detection with YOLOv8

This repository contains inference implementation, trained model, documentation of the training methodology, fine-tuning, and evaluating YOLOv8 models to detect surgical instruments (such as tweezers and needle drivers) in surgical videos and images. It includes scripts for training, predictions, environment setup, and usage instructions.

---

## 📁 Project Structure

```
📦 surgical_yolo_project
├── README.md                   # This file
├── requirements.txt            # Python dependencies
├── predict.py                  # Run prediction on a single image
├── video.py                    # Run prediction on a full video
├── weights/                    # Folder for trained model weights
│   └── yolov8_best.pt          # Also can downloaded - see below
└── output/                     # Output videos/images saved during inference
```

---

## 💾 Model Weights
Final model weights (YOLOv8) can be downloaded from the following link:
[Download yolov8_best.pt](https://github.com/aviatay/cvsa-hw1/releases/download/v1.0/yolov8_best.pt)

> **Note**: If released a newer version of the model in the future, update this link to match the new release tag (e.g., `v1.1`, `v2.0`, etc.).

---

## 🛠️ Requirements
Ensure Python 3.8+ is installed.

Install all dependencies:
```bash
pip install -r requirements.txt
```

**requirements.txt includes:**
- `ultralytics==8.0.20`
- `opencv-python`
- `numpy`
- `torch>=1.9`

---

## 🖼️ Run Inference on Images
Use the script below to predict bounding boxes on a single image:
```bash
python predict.py path/to/input.jpg
```
This saves the annotated image to a subfolder and prints the path.

---

## 🎥 Run Inference on Videos
Run bounding box detection on full videos with:
```bash
python video.py path/to/input.mp4
```
The annotated output is saved to `output/annotated_video.mp4`

---

## 📊 Model Training Overview

Training involved multiple phases to progressively improve detection performance:

### 1. Initial Supervised Training
- 50 manually labeled surgical frames were used.
- Model trained with YOLOv8l at image size 640x640.
- Augmentations: mosaic, scale, brightness, horizontal flip, and color jitter.
- Trained for 100 epochs with batch size 8.
- Regularization: dropout (0.3), learning rate (0.001), early stopping.

### 2. Semi-Supervised Training with ID Videos
- Pseudo-labels were generated using model weights from Step 1.
- 2000+ ID video frames were labeled automatically.
- Pseudo-labeled frames + original labeled images = combined training set.
- Trained for 50 epochs with batch size 16.
- Dataset split: 80% train, 20% val.

### 3. Domain Adaptation with OOD Videos
- Pseudo-labels generated for OOD video using model from Step 2.
- Cleaned labels and fine-tuned on this new data for 15 epochs.
- Goal: Adapt to new domain appearance (e.g., lighting, angle, instruments).

### 4. Final Inference
- Final weights used to predict surgical instruments on new, unseen videos.

---

## 🧪 Classes Detected
The model is trained to detect the following surgical instruments:
- `0`: Empty (no tool)
- `1`: Tweezers
- `2`: Needle_driver

---

## 🧱 Data Format & YAML
Training datasets were formatted in the YOLO standard:
```
dataset/
├── images/
│   ├── train/
│   └── val/
├── labels/
│   ├── train/
│   └── val/
```
Each label `.txt` file contains lines in the format:
```
class x_center y_center width height confidence
```
All coordinates are normalized between 0 and 1.

---

## 📓 Notes
- The training was performed on a V100 GPU.
- All augmentations and model parameters are configured in the training script.
- Outputs are saved to automatically created directories inside `output/`.
