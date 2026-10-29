# Lab Mid Exam Solution: HOG vs SIFT on Blood-Cell Images

**Name:** M. Abdullah Zaka  
**Registration Number:** FA23-BAI-017  

---

## Personal Settings & Fixed Configuration

| Setting | Rule | Your Value |
| :--- | :--- | :--- |
| **Random seed** | Last two digits of your registration number | 17 |
| **HOG cell size** | Last digit even: $8 \times 8$. Last digit odd: $16 \times 16$ | $16 \times 16$ (last digit is 7, which is odd) |
| **SIFT grid size** | Last digit 0 to 4: $4 \times 4$ grid (16 keypoints). Last digit 5 to 9: $6 \times 6$ grid (36 keypoints) | $6 \times 6 = 36$ keypoints (last digit is 7) |

- **Dataset:** BloodMNIST ($\text{size}=64$), resized to $128 \times 128$ grayscale (`uint8`)
- **Kept Classes:** eosinophil (1), monocyte (5), neutrophil (6), platelet (7), mapped to class IDs 0, 1, 2, 3
- **Subset Sizes:** 100 train, 30 validation, and 30 test images per class

---

## Q1. Data Setup (3 Marks)

### 1. Full Dataset Split Shapes
- **Train full split:** $(11959, 64, 64, 3)$, labels: $(11959, 1)$
- **Validation full split:** $(1712, 64, 64, 3)$, labels: $(1712, 1)$
- **Test full split:** $(3421, 64, 64, 3)$, labels: $(3421, 1)$

### 2. Filtered & Preprocessed Subset Shapes and Class Counts
- **Train Split:** $X \text{ shape} = (400, 128, 128)$, $\text{dtype}=\text{uint8}$, $y \text{ shape} = (400,)$  
  *Class counts:* `{'eosinophil': 100, 'monocyte': 100, 'neutrophil': 100, 'platelet': 100}`
- **Validation Split:** $X \text{ shape} = (120, 128, 128)$, $\text{dtype}=\text{uint8}$, $y \text{ shape} = (120,)$  
  *Class counts:* `{'eosinophil': 30, 'monocyte': 30, 'neutrophil': 30, 'platelet': 30}`
- **Test Split:** $X \text{ shape} = (120, 128, 128)$, $\text{dtype}=\text{uint8}$, $y \text{ shape} = (120,)$  
  *Class counts:* `{'eosinophil': 30, 'monocyte': 30, 'neutrophil': 30, 'platelet': 30}`

### 3. Example Image Grid
A $4 \times 4$ grid showing 4 training examples per class in $128 \times 128$ grayscale.

---

## Q2. HOG + SVM (6 Marks)

### (1) HOG Feature Length Calculation by Hand
- **Image size:** $128 \times 128$ pixels
- **Cell size:** $16 \times 16$ pixels (registration number ends in 7 $\rightarrow$ odd)
- **Cells per image:** $\frac{128}{16} = 8 \rightarrow 8 \times 8$ cells
- **Block size:** $2 \times 2$ cells per block
- **Blocks per side:** $8 - 2 + 1 = 7$
- **Total blocks:** $7 \times 7 = 49$ blocks
- **Features per block:** $4 \text{ cells/block} \times 9 \text{ orientations} = 36 \text{ values per block}$
- **Total HOG Feature Vector Length:** $49 \text{ blocks} \times 36 \text{ values/block} = 1764 \text{ features}$

### (2) HOG Visualization
*(Included in experimental logs)*

### (3) SVM Training and Validation Results
- **Best $C$ chosen on validation set:** $0.1$
- **Validation Accuracy:** $0.7583$
- **Validation Macro-F1 Score:** $0.7561$

**Confusion Matrix:**
$$
\begin{bmatrix}
19 & 8 & 3 & 0 \\
4 & 22 & 3 & 1 \\
6 & 3 & 20 & 1 \\
0 & 0 & 0 & 30
\end{bmatrix}
$$

---

## Q3. SIFT + SVM (7 Marks)

### (1) Normal SIFT Detection
- **Average keypoints per image:** $72.00$
- **Minimum keypoints per image:** $11$
- **Maximum keypoints per image:** $184$
- **Images with zero keypoints:** $0$ of $400$

> **Why a different number of keypoints per image is a problem for an SVM:**  
> An SVM classifier needs every input image to be represented by a fixed-length feature vector of the exact same size, but normal SIFT finds a different number of keypoints in each image, which creates feature vectors of unequal lengths that cannot be passed directly into an SVM.

### (2) Fixed Grid SIFT Feature Length Calculation by Hand
- **Grid size:** $6 \times 6 = 36 \text{ keypoints}$ (last digit is 7, between 5 and 9)
- **Descriptor size per keypoint:** $128 \text{ values}$
- **Total SIFT Feature Vector Length:** $36 \text{ grid points} \times 128 = 4608 \text{ features}$

### (3) SVM Training and Validation Results
- **Best $C$ chosen on validation set:** $0.1$
- **Validation Accuracy:** $0.7083$
- **Validation Macro-F1 Score:** $0.7013$

**Confusion Matrix:**
$$
\begin{bmatrix}
16 & 9 & 4 & 1 \\
4 & 24 & 1 & 1 \\
8 & 7 & 15 & 0 \\
0 & 0 & 0 & 30
\end{bmatrix}
$$

---

## Q4. Robustness (6 Marks)

### (1) Prediction Before Running Test Evaluation
- **Which model will lose more accuracy under rotation ($20^\circ$):**  
  I predict **HOG** will lose more accuracy under rotation because HOG's $20^\circ$-wide orientation bins and fixed cell layout shift when the image turns.
- **Which model will lose more accuracy under noise:**  
  I predict **HOG** will lose more accuracy under noise because HOG takes gradients directly from raw pixels, so noise adds random gradient energy in every cell.

### (2) Test Set Evaluation Table

| Model | Clean Accuracy | Rotated Accuracy ($20^\circ$) | Noisy Accuracy ($\sigma=0.05$) |
| :--- | :---: | :---: | :---: |
| **HOG + SVM** | 0.7333 | 0.6917 | 0.2500 |
| **SIFT + SVM** | 0.7167 | 0.7500 | 0.7083 |

### (3) Was Your Prediction Correct?
Under rotation, HOG fell by 4.2 points and SIFT increased by 3.3 points ($-3.3$ difference), so HOG lost more. Under noise, HOG fell by 48.3 points and SIFT fell by only 0.8 points, so HOG lost significantly more. 

Both my rotation prediction (HOG) and noise prediction (HOG) were correct. A likely reason is that HOG's $20^\circ$-wide orientation bins and fixed cell layout shift when the image turns, and for noise, HOG computes gradients directly from raw pixels, meaning noise adds random gradient energy into every cell.

---

## Q5. Short Answers and AI Log (3 Marks)

### (1) Short Conceptual Questions

#### (a) Why does HOG use block normalisation?
HOG uses block normalisation to group nearby cells together ($2 \times 2$ cells per block) and divide their gradient values by the overall magnitude of the block. This makes the features much less sensitive to changes in lighting, shadows, or image contrast.

#### (b) Why is it wrong to choose $C$ using the test set?
If we use the test set to pick the best hyperparameter $C$, we are leaking test information into our model selection process and overfitting to the test data. The validation set is meant for tuning hyperparameters like $C$, while the test set should only be evaluated once at the end to evaluate final performance.

### (2) Most Confused Classes and Effect of Grayscale
- **Most confused classes:** Looking at the confusion matrices, both models mostly confuse **eosinophil** and **monocyte** (25 errors between them).
- **Why grayscale makes this harder:** In the original colour images, different white blood cells have very clear dye/stain colours—for example, eosinophils have bright pink/orange cytoplasm granules. When converted to grayscale, all color information is removed, leaving only cell size and nucleus/lobe shapes, which look very similar at $128 \times 128$ resolution.

### (3) AI Log Table

| Prompt (One Line) | What I Changed | How I Checked the Result Was Correct |
| :--- | :--- | :--- |
| `"Write Python code to filter BloodMNIST for labels [1, 5, 6, 7], sample 100/30/30 per class with seed, and resize to 128x128 grayscale."` | Set `REG_NO = "017"` (`SEED = 17`), mapped old labels `[1, 5, 6, 7]` to `0..3`, and used `cv2.resize` with `IMG = 128`. | Printed the $X$ and $y$ shapes ($(400, 128, 128)$, $(120, 128, 128)$) and class counts per split, and plotted a $4 \times 4$ image grid. |
| `"How to extract HOG features in scikit-image and tune LinearSVC with C in [0.1, 1, 10] on validation set"` | Changed `pixels_per_cell` to `(16, 16)` to match my odd registration number rule and added `StandardScaler` in `make_pipeline`. | Checked that the printed HOG feature vector length matched my hand calculation (1764) and plotted the HOG images for 4 classes. |
| `"Create a fixed 6x6 grid of OpenCV KeyPoints on a 128x128 image with size 32 and compute SIFT descriptors"` | Adjusted the grid step spacing so all 36 keypoints sit evenly inside the $128 \times 128$ image and flattened the output array. | Printed the output descriptor shape per image to verify it was $36 \times 128 = 4608$ and checked that no `None` descriptors were returned. |
| `"Write functions to rotate a 128x128 grayscale image by 20 degrees and add Gaussian noise with sigma 0.05"` | Scaled the image to $[0, 1]$ before adding noise ($\sigma = 0.05$), clipped values back to $[0, 1]$, and converted back to `uint8` $[0, 255]$. | Plotted sample clean, rotated, and noisy test images side by side and checked `dtype=uint8` before extracting HOG and SIFT features. |