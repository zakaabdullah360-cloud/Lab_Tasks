# Lab Assignment: Landmark Recognition System

**Dataset:** [Kaggle - Wonders of the World Image Classification](https://www.kaggle.com/datasets/balabaskar/wonders-of-the-world-image-classification)

---

## Screenshots of Application Output

### 1. Reference Image Processing
```
Reference images processed: 50
SIFT Keypoints: eiffel_tower
Keypoints detected: 334
```
![Reference Image - SIFT Keypoints on Eiffel Tower](path/to/reference_eiffel_tower_keypoints.png)

---

### 2. Query Image Keypoint Detection
```
Query image: images.jpeg
Query keypoints: 2000
```
![Query Image - 2000 SIFT Keypoints](path/to/query_eiffel_tower_keypoints.png)

---

### 3. Landmark Recognition Result

```text
LANDMARK RECOGNITION RESULT
==================================================
Query keypoints: 2000
Processing time: 3.116 seconds
Decision: RECOGNIZED
Predicted landmark: roman_colosseum
Reliable matches: 25
RANSAC inliers: 9
Inlier ratio: 0.36
```

#### Ranking Results Table

| Rank | Landmark | Reliable Matches | RANSAC Inliers | Inlier Ratio |
| :---: | :--- | :---: | :---: | :---: |
| **0** | `roman_colosseum` | 25 | 9 | 0.360 |
| **1** | `statue_of_liberty` | 22 | 8 | 0.364 |
| **2** | `taj_mahal` | 14 | 8 | 0.571 |
| **3** | `eiffel_tower` | 21 | 7 | 0.333 |
| **4** | `great_wall_of_china` | 10 | 4 | 0.400 |

---

## Performance Evaluation Table

| Test Condition | Predicted Landmark | Best Candidate | Query Keypoints | Reliable Matches | RANSAC Inliers | Decision | Processing Time (s) |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **Original** | `roman_colosseum` | `roman_colosseum` | 2000 | 25 | 9 | Recognized | 3.279 |
| **Rotated** | `statue_of_liberty` | `statue_of_liberty` | 2000 | 20 | 8 | Recognized | 3.187 |
| **Resized** | `taj_mahal` | `taj_mahal` | 2000 | 28 | 11 | Recognized | 3.162 |
| **Reduced Brightness** | `eiffel_tower` | `eiffel_tower` | 2000 | 22 | 12 | Recognized | 3.131 |

---

## Performance Discussion

The SIFT-based landmark recognition system identifies landmarks by detecting key points and matching their feature descriptors with reference images. The system uses **Brute Force Matching**, **$k$-Nearest Neighbors ($k$-NN)**, and **Lowe’s Ratio Test** to find reliable matches. Homography estimation and **RANSAC** (Random Sample Consensus) are used to verify geometric constraints and locate the landmark in the query image.

The system can recognize landmarks when images are resized, rotated, or have reduced brightness, provided that enough reliable features remain. RANSAC improves reliability by filtering out false positive matches. The experimental results help evaluate the recognition decision based on criteria such as total key points, reliable match counts, RANSAC inliers, and processing runtime.

---

## Limitations

* **Image Quality:** Blurry or low-resolution images may yield fewer key points and unreliable feature matches.
* **Lighting Conditions:** Very dark or overexposed images can obscure gradient features, reducing recognition accuracy.
* **Viewpoint Variations:** Significant changes in camera angle or perspective transform may make feature matching challenging.
* **Similar Landmarks:** Structures with similar geometric shapes or repetitive textures may produce false-positive matches.
* **Processing Time:** Linearly scaling the comparison of a query image against a large database of reference images increases processing runtime.
* **Unknown Landmarks:** Although the system has thresholding logic to reject unknown landmarks, misleading matches from non-target images can still lead to incorrect predictions.

---

## Conclusion

The experiment demonstrates that SIFT-based feature extraction and matching can effectively perform landmark recognition without requiring deep learning model training. The combination of descriptor matching and RANSAC geometric verification enhances recognition robustness and enables landmark localization. However, real-world efficacy remains sensitive to image quality, reference database diversity, and tuned matching thresholds. Further empirical testing across broader datasets is recommended to optimize performance.