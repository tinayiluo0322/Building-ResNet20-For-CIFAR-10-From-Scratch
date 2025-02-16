# **Building and Optimizing ResNet-20 For CIFAR-10 From Scratch**
Following the Original ["Deep Residual Learning for Image Recognition"](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf)

## **Step 1: Model Architecture**
### **ResNet-20 Architecture Details**
- **Total weighted layers:** 6n + 2 = **20**
- **n (blocks per stage):** 3
- **Total shortcut (skip) connections:** 3n = **9**

### **Layer Breakdown**
| **Output Map Size** | **32 x 32** | **16 x 16** | **8 x 8** |
|---------------------|------------|------------|-----------|
| **# Layers**       | 1 + 2 × n = 7 | 2 × n = 6 | 2 × n = 6 |
| **# Filters**      | 16         | 32         | 64        |
| **# Shortcuts**    | 3          | 3          | 3         |

### **Layer Details**
| **Layer Name**  | **Output Size**  | **Details** |
|----------------|----------------|-------------|
| **Input**      | 32 × 32 × 3     | Raw CIFAR-10 image |
| **Conv1**      | 32 × 32 × 16    | 3 × 3, 16 filters, stride 1 |
| **Conv2_x**    | 32 × 32 × 16    | 3 residual blocks, each with two 3 × 3 convolutions |
| **Conv3_x**    | 16 × 16 × 32    | 3 residual blocks, each with two 3 × 3 convolutions (first block subsamples with stride 2) |
| **Conv4_x**    | 8 × 8 × 64      | 3 residual blocks, each with two 3 × 3 convolutions (first block subsamples with stride 2) |
| **Global Avg Pool** | 1 × 1 × 64 | Pooling over entire feature map |
| **Fully Connected (FC)** | 10 | Output class predictions |

### Residual Blocks Details 
Reference: [DIVE INTO DEEP LEARNING](https://d2l.ai/chapter_convolutional-modern/resnet.html)
---

## **Step 2: Implement the Model**
- **Each residual block contains:**  
  - **Two 3×3 convolutional layers**  
  - **Batch Normalization after each convolution**  
  - **ReLU activation function**  
  - **Identity skip connection (or 1×1 projection for channel matching)**  
- **No dropout is used in the model.**
- **Weight initialization:** He (Kaiming) Initialization

---

## **Step 3: Data Preprocessing and Augmentation**
### **Data Augmentation Techniques**
| **Augmentation Type** | **Description** |
|----------------------|----------------|
| **Padding**         | 4 pixels on each side |
| **Random Cropping** | 32×32 crop from the padded image |
| **Horizontal Flip** | 50% probability |
| **Normalization**   | Mean: (0.4914, 0.4822, 0.4465), Std: (0.2023, 0.1994, 0.2010) |
| **Validation Augmentation** | No augmentation, only normalization |

---

## **Step 4: Dataset and Dataloader Setup**
| **Dataset** | **Number of Images** |
|------------|---------------------|
| **Training Set** | 45,000 |
| **Validation Set** | 5,000 |
| **Test Set (Held-out)** | 10,000 |

---

## **Step 5: Optimization & Loss Function**
| **Hyperparameter** | **Value** |
|-------------------|----------|
| **Loss Function** | Cross-Entropy Loss |
| **Optimizer** | SGD (Stochastic Gradient Descent) |
| **Initial Learning Rate** | 0.1 |
| **Momentum** | 0.9 |
| **Weight Decay (L2 Regularization)** | 0.0001 |
| **Learning Rate Decay** | ÷10 at 32k and 48k iterations |
| **Training Termination** | 64k iterations |
| **Weight Initialization** | He Initialization |
| **Batch Normalization** | Yes |
| **Dropout** | No |
| **Batch Size** | 128 |

---

## **Step 6: Training the Model**
- Model is trained using **SGD optimizer with momentum**.
- **Learning rate starts at 0.1**, decays at **32k and 48k iterations**.
- **Best validation accuracy achieved:** **> 90% within 200 epochs**.
- Model is **saved during training** if validation accuracy improves.

---

## **Step 7: Generating Test Set Predictions**
- **Test set is held out (no labels available).**
- **Predictions are stored in** `predictions.csv` **following the required format**:
  ```
  Id,Label
  0,3
  1,8
  2,1
  ...
  ```
---

## **Final Notes**
- **ResNet-20 was implemented from scratch** following Section 4.2 in the original paper.
- **No pre-trained weights were used**; all training was done on CIFAR-10 from initialization.
- **Proper model evaluation and test predictions** ensure **fair benchmarking**.
