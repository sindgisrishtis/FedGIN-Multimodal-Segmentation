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

🧠 Step 1: Centralized Training (Baseline)

🔹 Model Architecture

Used Attention U-Net for multi-organ segmentation
Input: 1-channel CT slice
Output: 5-class segmentation mask

🔹 Training Configuration

Loss Function: Dice + BCE Loss
Optimizer: AdamW
Mixed Precision: Enabled (AMP)
Scheduler: Cosine Annealing
Training: Full dataset (no partitioning)

📊 Centralized Performance

Mean Dice Score: 0.9069

Per-organ performance:

Liver: 0.9205
Spleen: 0.9249
Left Kidney: 0.8302
Right Kidney: 0.9329
Pancreas: 0.9263

🎯 Observation

High segmentation accuracy due to full data access
Acts as upper bound benchmark
🌐 Step 2: Federated Learning (FedAvg)

🔹 Client Simulation

Dataset split into 3 clients
Non-IID distribution using Dirichlet (α = 2.0)

Client Distribution:

Client 1: 1267 samples
Client 2: 3248 samples
Client 3: 4693 samples

🔹 Training Setup

Model: Attention U-Net (initialized from centralized model)
Rounds: 15 communication rounds
Local Epochs: 1
Aggregation: Federated Averaging (FedAvg)

📊 Federated Performance (FedAvg)

Mean Dice Score: ~0.81 – 0.82

⚠️ Observation

Performance degradation compared to centralized model
Caused by:
Data heterogeneity
Client drift
Limited local data
🚀 Step 3: FedGDA (Proposed Method)

💡 Motivation

Standard FedAvg does not address distribution mismatch between clients
FedGDA introduces server-guided data alignment

🔹 Core Idea

Instead of ignoring client differences
The server helps clients adapt their data distributions

🔹 Step 3.1: Client Statistics
Each client computes:

Mean intensity
Standard deviation

🔹 Step 3.2: Server Aggregation
Server computes:

Global mean
Global standard deviation

🔹 Step 3.3: Data Alignment (Dataset Level)

Implemented inside dataset pipeline
Each image is adjusted using global statistics
Applied during data loading (__getitem__)

🔹 Step 3.4: Key Enhancement (Alpha Blending)

Prevents over-normalization
Introduces alpha-controlled transformation

Balances:

🌍 Global alignment
🧩 Local feature preservation
📊 Step 4: Final Results
Method	Mean Dice
Centralized	0.9069
FedAvg	~0.8165
FedGDA	0.8467
🎯 Step 5: Key Observations
🚀 FedGDA improves performance by ~3–4%
🔻 Reduces gap between centralized and federated learning
💪 Strong performance for large organs (liver, pancreas)
⚠️ Slight drop for smaller organs (spleen)
🧠 Step 6: Summary
Step 1: Centralized Learning → High performance baseline
Step 2: Federated Learning (FedAvg) → Performance drop due to non-IID data
Step 3: FedGDA → Improved federated learning using server-guided augmentation

👉 Final Outcome:
Improved segmentation performance in federated settings using data distribution alignment
