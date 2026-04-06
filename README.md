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

## 🚀 Advanced Model Training (Improved Work)

To improve segmentation performance, we implemented an advanced deep learning approach.

### 🧠 Model Architecture

* **Attention U-Net**
* Encoder-decoder structure with attention gates
* Helps focus on relevant regions in medical images

---

### ⚙️ Training Configuration

* Loss Function: **Dice Loss + Binary Cross Entropy**
* Optimizer: **AdamW**
* Scheduler: **Cosine Annealing**
* GPU: **NVIDIA A100**
* Mixed Precision (AMP): Enabled
* Epochs: 10

---

### 📊 Results

* **Best Validation Dice Score: 0.89 🔥**

#### 🧠 Per-Organ Dice Scores

* Liver: 0.89
* Spleen: 0.92
* Left Kidney: 0.81
* Right Kidney: 0.92
* Pancreas: 0.90

---

### 📈 Training Visualization

![Training Curves](training_curves.png)

---

### 🖼️ Prediction Results

* Model predictions closely match ground truth
* High accuracy across all organs
* Significant improvement over baseline model

---

### 🎯 Key Improvement

| Model                 | Dice Score  |
| --------------------- | ----------- |
| Attention U-Net       | **0.89 🔥** |

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

## 🚀 What was done 

### 🔹 1. Non-IID Data Partitioning

To simulate real-world data heterogeneity:

*Dataset split into 3 clients
*Used Dirichlet distribution (α = 1.0)
*Each client receives uneven and skewed data

Client Distribution:

* Client 1: 1389 samples
* Client 2: 4599 samples
* Client 3: 3220 samples

### 🔹 2. Client-side Training

* Each client trains locally on its own dataset
* Uses **Dice + BCE loss**
* Local training performed for each round

---

### 🔹 3. Federated Averaging (FedAvg)

*Each client trains locally
*Model weights are sent to server
*Aggregated using weighted averaging
*Larger datasets contribute more to global model

---

### 🔹 4. Federated Training

*Global Model: Attention U-Net (pretrained baseline)
*Clients: 3 simulated hospitals
*Local Training: 1 epoch per round
*Communication Rounds: 5
*Loss Function: Dice + BCE Loss
*Optimizer: AdamW

---

📈 Per-Organ Performance (Federated)
*Liver: 0.8432
*Spleen: 0.7614
*Left Kidney: 0.5754
*Right Kidney: 0.8388
*Pancreas: 0.6250


## 📊 Results

| Model       | Dice Score |
| ----------- | ---------- |
| Centralized | 0.8967     |
| Federated   | 0.7288     |


---

## 📷 Output

![Output](federated_vs_baseline.png)

---





