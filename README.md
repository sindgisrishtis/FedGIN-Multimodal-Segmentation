# FedGIN-Multimodal-Segmentation
Federated Learning Framework using Dynamic Global Non-linear Augmentation for Multi-modal Organ Segmentation

# FedGIN Multi-modal Organ Segmentation

## 📌 Project Overview
This project focuses on **multi-organ segmentation using Federated Learning concepts**, inspired by the FedGIN framework.

The goal is to handle **data heterogeneity across medical datasets** and improve generalization using advanced augmentation strategies.

---

## 🎯 Current Progress (Completed)

### ✅ 1. Dataset Selection
- Dataset: **TotalSegmentator (Small Subset)**
- ~102 CT scans
- Real-world clinical data

---

### ✅ 2. Organ Selection
We selected **5 important abdominal organs**:

- Liver  
- Spleen  
- Pancreas  
- Kidneys (Left + Right combined)  
- Gallbladder  

---

### ✅ 3. Preprocessing Pipeline

Steps performed:

1. Loaded CT scans (`.nii.gz`)
2. Extracted selected organ masks
3. Combined left & right kidneys
4. Normalized CT images
5. Saved processed data as:
*_ct.npy
*_mask.npy

---

### ✅ 4. 3D → 2D Slicing

To enable efficient training:

- Converted 3D volumes into 2D slices
- Removed empty slices (no organs)
- Final dataset contains **~21,000+ slices**

---

### 📂 Final Data Format

Each sample:

- **CT slice:** `(H, W)`
- **Mask:** `(5, H, W)`

---

### 📊 Example

- CT Shape: `(207, 207)`
- Mask Shape: `(5, 207, 207)`
- Mask Values: `{0, 1}`

---
### 🧠 Model Training & Segmentation

---

### ✅ 5. Model Architecture

We implemented a **U-Net inspired convolutional neural network** for multi-organ segmentation on CT images.

**Architecture Details:**

* **Input:** 1-channel CT slice
* **Output:** 5-class segmentation mask
* **Encoder:**

  * Conv2D (1 → 16) + ReLU
  * Conv2D (16 → 32) + ReLU
* **Decoder:**

  * Upsampling + Conv layers
* **Final Layer:** Conv2D (32 → 5)
* **Activation:** ReLU

---

### ✅ 6. Training Pipeline

The model was trained using an optimized PyTorch pipeline with GPU acceleration.

**Configuration:**

* **Loss Function:** CrossEntropyLoss (with class weighting to handle imbalance)
* **Optimizer:** Adam
* **Batch Size:** 16
* **Epochs:** 10
* **Mixed Precision:** Enabled (AMP) for faster training

**Workflow:**

* Load preprocessed CT slices (`.npy` format)
* Custom PyTorch Dataset for paired image-mask loading
* DataLoader with parallel workers for efficient batching
* Forward pass through UNet model
* Compute loss with class balancing
* Backpropagation using gradient scaling (AMP)
* Update model weights

---

### 📊 Training Performance

* Loss decreased consistently across epochs
* Final loss stabilized around **~0.07 – 0.1**
* Training time: ~2–3 minutes per epoch (GPU accelerated)

---

### 💾 Model Checkpoint

Final trained model saved as:

```
unet_final.pth
```

---

### 📈 7. Evaluation (Dice Score)

We evaluated segmentation performance using the **Dice Coefficient**, a standard metric for medical image segmentation.

**Final Dice Score:**

```
0.966
```

**Interpretation:**

* Indicates **high overlap** between predicted and ground truth masks
* Demonstrates strong segmentation capability

---

### 🖼️ 8. Visualization

We generated visual comparisons between:

* Input CT Image
* Ground Truth Mask (overlay)
* Model Prediction (overlay)

**Observations:**

<<<<<<< HEAD
Model captures structural features and boundaries
Full segmentation not perfect due to:
limited training
small dataset
class imbalance

---

### 🧠 Learning Type

This implementation uses **Centralized Learning** for model training.
=======
* Model successfully captures major anatomical structures
* Predictions closely align with ground truth regions
* Minor noise present in small regions (common in segmentation tasks)

---

### 🎯 Key Improvements Over Initial Version

* Fixed class imbalance using weighted loss
* Increased training epochs for better convergence
* Enabled GPU acceleration and mixed precision
* Improved data pipeline efficiency
* Achieved significant boost in Dice Score (**from low → 0.96**)
* Enhanced visualization using overlay techniques
>>>>>>> 432628c (Added trained UNet model file)

---

# 🧠 Federated Learning for Multi-Organ Segmentation

## 📌 Overview

This part of the project implements **Federated Learning** on a pre-trained Attention U-Net model for multi-organ segmentation using CT scan data.

---
Centralized Training (Baseline)
🔹 Model Architecture
Attention U-Net for multi-organ segmentation
Input: 1-channel CT slice
Output: 5-class segmentation mask
🔹 Training Configuration
Loss: Dice + BCE Loss
Optimizer: AdamW
Mixed Precision: Enabled (AMP)
Scheduler: Cosine Annealing
Training: Full dataset (no partitioning)
📊 Centralized Performance
Mean Dice Score: 0.9069

Per-organ Dice:

Liver → 0.9205
Spleen → 0.9249
Left Kidney → 0.8302
Right Kidney → 0.9329
Pancreas → 0.9263
🎯 Observation
High accuracy due to full data access
Serves as upper-bound benchmark
🌐 Federated Learning (FedAvg)
🔹 Client Simulation
3 clients with non-IID data (Dirichlet α = 2.0)

Client distribution:

Client 1 → 1267 samples
Client 2 → 3248 samples
Client 3 → 4693 samples
🔹 Training Setup
Model initialized from centralized weights
Rounds: 15
Local epochs: 1
Aggregation: FedAvg
📊 Federated Performance
Mean Dice: ~0.81 – 0.82
⚠️ Observation
Performance drop due to:
Data heterogeneity
Client drift
Limited local data
🚀 FedGDA (Proposed Method)
💡 Motivation

Standard FedAvg does not handle distribution mismatch across clients.
FedGDA introduces server-guided data alignment to improve performance.

🔹 Core Idea

Instead of ignoring client differences,
the server helps clients adapt their data distributions

🔹 Step 1: Client Statistics

Each client computes:

Mean intensity
Standard deviation
🔹 Step 2: Server Aggregation

Server computes:

Global mean
Global standard deviation
🔹 Step 3: Data Alignment
Applied inside dataset (__getitem__)
Images adjusted using global statistics
🔹 Key Enhancement: Alpha Blending
Prevents over-normalization
Controls strength of transformation

Balances:

🌍 Global alignment
🧩 Local feature preservation
📊 Final Results
Method	Mean Dice
Centralized	0.9069
FedAvg	~0.8165
FedGDA	0.8467
🎯 Key Observations
🚀 FedGDA improves performance by ~3–4% over FedAvg
🔻 Reduces gap between centralized and federated models
💪 Strong performance for large organs (liver, pancreas)
⚠️ Slight drop for smaller organs (e.g., spleen)
🧠 Summary

This project evolves through:

🧠 Centralized Learning → High performance baseline
🌐 Federated Learning (FedAvg) → Real-world simulation with performance drop
🚀 FedGDA → Improved federated learning using server-guided augmentation



