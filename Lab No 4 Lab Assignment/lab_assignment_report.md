# Lab Assignment: Image Processing and Skin Lesion Boundary Detection

## 1. Required Visualization

| Image | Best Filter | Edge Method | Area | Perimeters |
| :--- | :--- | :--- | :--- | :--- |
| **Image 1** | Gaussian | Canny (100–200) | 5563.5 | 1342.95 |
| **Image 2** | Gaussian | Canny (100–200) | 0.0 | 0.0 |
| **Image 3** | Gaussian | Canny (100–200) | 814.5 | 212.63 |
| **Image 4** | Gaussian | Canny (100–200) | 318.5 | 83.15 |
| **Image 5** | Gaussian | Canny (100–200) | 0.0 | 0.0 |

---

## 2. Final Comparison

| Method | Noise Handling | Edge Quality | Boundary Detection | Overall Performance |
| :--- | :--- | :--- | :--- | :--- |
| **Original + Sobel** | Low | Moderate | Moderate | Moderate |
| **Original + Canny** | Low | Good | Good | Good |
| **Average + Sobel** | Moderate | Moderate | Moderate | Moderate |
| **Average + Canny** | Moderate | Good | Good | Good |
| **Gaussian + Sobel** | Good | Good | Good | Good |
| **Gaussian + Canny** | Good | Very Good | Very Good | Very Good |
| **Median + Sobel** | Very Good | Good | Good | Good |
| **Median + Canny** | Very Good | Very Good | Very Good | Very Good |

---

## 3. Questions to Answer

### Q1: Why is Gaussian filtering applied before Canny detection?
Canny edge detection is highly sensitive to high-frequency noise (such as skin pores, hair, and minor pigmentation changes). Gaussian filtering smooths the image, reducing this noise so that the algorithm only detects the dominant, prominent edges representing the actual lesion boundary.

### Q2: How did the three Canny threshold settings affect the result?
* **Low (50–100):** Produced too many edges, capturing excessive background noise like skin texture and fine hair.
* **Medium (100–200):** Provided a balanced edge map, filtering out minor skin textures while preserving the structural outline of the lesion.
* **High (150–250):** Missed significant portions of the boundary, leading to broken or completely absent contours, especially in low-contrast areas.

### Q3: Which threshold produced the best lesion boundary?
The **100–200** threshold generally produces the best results. It is strict enough to ignore minor skin imperfections but sensitive enough to capture the continuous perimeter of the lesion.

### Q4: Why are edges useful for detecting skin lesions?
Skin lesions typically have a distinct color and intensity contrast compared to the surrounding healthy skin. This sudden change in pixel intensity translates to a strong gradient, making edge detection an effective mathematical way to separate the region of interest from the background.

### Q5: What problems did you observe in detecting the lesion boundary?
Hair overlapping the lesion creates false edges that disrupt the contour. Additionally, lesions with fuzzy, fading, or irregular borders (low contrast with surrounding skin) often result in broken or incomplete boundary loops, making it difficult for the `findContours` algorithm to close the shape.

### Q6: How could your method be improved?
The method could be improved by using morphological operations (like closing or dilation) to bridge gaps in broken edge maps. Additionally, converting the image to different color spaces (such as HSV or LAB) before processing, or applying masking techniques like Otsu's Thresholding or Active Contours (Snakes), would yield more robust segmentation than standard Canny detection alone.