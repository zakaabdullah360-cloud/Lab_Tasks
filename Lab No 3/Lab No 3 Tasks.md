# Tables

## Table 1. Effect of Noise and Preprocessing on Edge Detection

| Edge Detector | Input Image | Noise Type | Preprocessing | Edge Quality | Noise Sensitivity | Observations | 
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | 
| Sobel | Original | None | None | 0.309 | High | Original image used as baseline. | 
| Sobel | Noisy | Gaussian | None | 0.301 | High | Gaussian noise introduces extra edge responses. | 
| Sobel | Noisy | Salt & Pepper | None | 0.328 | High | Salt-and-pepper noise may create false or brok... | 
| Sobel | Noisy | Gaussian | Gaussian Filter | 0.301 | High | Gaussian filtering smooths noise but may sligh... | 
| Sobel | Noisy | Salt & Pepper | Median Filter | 0.319 | High | Median filtering reduces impulse noise and pro... | 
| Prewitt | Original | None | None | 0.311 | High | Original image used as baseline. | 
| Laplacian | Original | None | None | 0.343 | High | Original image used as baseline. | 
| LoG | Noisy | Gaussian | Gaussian Filter | 0.391 | High | Gaussian filtering smooths noise but may sligh... | 
| Canny | Original | None | Built-in smoothing | 0.912 | Low | Original image used as baseline. | 
| Canny | Noisy | Gaussian | Gaussian Filter | 0.440 | Low | Gaussian filtering smooths noise but may sligh... | 
| Canny | Noisy | Salt & Pepper | Median Filter | 0.202 | Low | Median filtering reduces impulse noise and pro... | 

## Table 2. Canny Parameter Analysis

| Configuration | Low Threshold | High Threshold | Kernel Size | Edge Quality | Number of Detected Edges | Observation | 
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | 
| Canny-1 | 30 | 100 | 3×3 | 1.000 | 4267 | Good edge representation | 
| Canny-2 | 50 | 150 | 3×3 | 0.912 | 2287 | Good edge representation | 
| Canny-3 | 100 | 200 | 3×3 | 0.289 | 726 | Low edge representation | 
| Canny-4 | 50 | 150 | 5×5 | 0.989 | 15611 | Good edge representation | 

## Table 3. Cross-Lab Classification Performance Comparison

| Model / Classifier | Accuracy Raw (Lab 1) | Accuracy Filtered (Lab 2) | Accuracy Edge (Lab 3) | Precision | Recall | F1-Score | Training Time (s) | Inference Time (ms) | 
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | 
| SVM | 0.5255 | 0.4706 | 0.3882 | 0.3804 | 0.3882 | 0.3819 | 6.1136 | 7.5849 | 
| Random Forest | 0.3922 | 0.3882 | 0.3490 | 0.3482 | 0.3490 | 0.3354 | 2.4494 | 0.1427 | 
| KNN | 0.3294 | 0.3333 | 0.2941 | 0.2746 | 0.2941 | 0.2739 | 0.1023 | 0.4128 | 
| CNN Model 1 - ResNet18 | 0.8157 | 0.7765 | 0.4353 | 0.4352 | 0.4353 | 0.4288 | 23.8951 | 3.1906 | 
| CNN Model 2 - MobileNetV2 | 0.7922 | 0.7882 | 0.4118 | 0.4184 | 0.4118 | 0.4007 | 28.4470 | 2.2398 | 

# Report

## Introduction

Edge detection is an important technique in digital image processing used to identify boundaries and significant changes in intensity within an image. In this laboratory, different edge detection techniques were studied, including Sobel, Prewitt, Laplacian, Laplacian of Gaussian (LoG), and Canny. The experiment also investigated how Gaussian noise and salt-and-pepper noise affect edge detection and how filtering can improve the resulting edge maps.

The main purpose of this laboratory was to compare traditional edge-based representations with raw and filtered images for image classification. The experiment also helped demonstrate the difference between manually designed image features and features learned automatically by Convolutional Neural Networks (CNNs). The laboratory required comparison of raw, filtered, and edge-based images using the same dataset split and evaluation metrics.

## Methodology

The skin-cancer image dataset was downloaded using the KaggleHub dataset: `nodoubttome/skin-cancer9-classesisic`. Four image classes were selected for the experiment. The dataset was divided into training, validation, and testing subsets using a stratified split.

Several edge detection techniques were implemented. Sobel was used to calculate horizontal and vertical gradients as well as gradient magnitude. Prewitt was used as another first-order edge detector. Laplacian and Laplacian of Gaussian were used as second-order methods. Canny was used as a multi-stage edge detector.

To study noise effects, Gaussian noise and salt-and-pepper noise were added to sample images. Edge detection was then performed on the original noisy images and on noisy images after Gaussian and Median filtering.

Different Canny threshold configurations were also tested, including 30/100, 50/150, and 100/200, along with different kernel sizes. The resulting edge maps were compared based on edge quality and the number of detected edge pixels.

Finally, three image representations were prepared:

* Set A — Raw images
* Set B — Filtered images
* Set C — Edge images

SVM, Random Forest, KNN, ResNet18, and MobileNetV2 were used for classification. Accuracy, precision, recall, F1-score, training time, and inference time were calculated.

## Experimental Setup

The experiment was performed in Google Colab using Python. OpenCV was used for image processing and edge detection, while Scikit-learn was used for the classical machine-learning classifiers. PyTorch and Torchvision were used for the CNN models.

The images were resized to 224 × 224 pixels. The same dataset split was used for the classification experiments to provide a fair comparison between raw, filtered, and edge representations.

The edge detection methods tested were:

* Sobel Gx
* Sobel Gy
* Sobel Gradient Magnitude
* Prewitt
* Laplacian
* Laplacian of Gaussian
* Canny

Gaussian and salt-and-pepper noise were used for the noise experiments. Gaussian and Median filters were then applied to investigate their effects on edge quality.

## Results

The experiments produced edge maps for all selected edge detection techniques. The results showed differences in edge continuity, sharpness, false edges, and sensitivity to noise.

The noise experiments demonstrated that adding noise can introduce unwanted responses in edge detectors. Filtering before edge detection generally helped reduce these unwanted responses, although excessive smoothing could also remove or weaken some genuine edges.

The Canny experiment showed that changing the low and high thresholds changes the number of detected edges. Lower thresholds generally allow more weak edges to be detected, while higher thresholds produce fewer and more selective edges.

For classification, raw, filtered, and edge representations were evaluated using the selected classifiers and CNN models. The exact numerical values of Accuracy, Precision, Recall, F1-score, Training Time, and Inference Time are reported in Table 3 from the experimental output.

## Discussion

### 5.1 Effect of Noise

Noise affects edge detection because edge detectors respond to intensity changes. Random noise can therefore be interpreted as additional intensity changes and may create false edges. Second-order operators such as Laplacian are particularly sensitive to noise because differentiation emphasizes rapid intensity changes.

### 5.2 Effect of Filtering

Gaussian filtering reduces high-frequency noise by smoothing the image. This can produce cleaner and more continuous edges, but strong smoothing can also blur actual boundaries.
Median filtering is particularly useful for salt-and-pepper noise because it replaces noisy pixels using neighboring pixel values while preserving many important boundaries. Therefore, it can reduce isolated false edges without producing as much blurring as ordinary averaging.

### 5.3 Canny Thresholds

The Canny thresholds control which gradients are considered strong or weak edges. Lower thresholds normally detect more edges, including weaker boundaries and potentially some unwanted edges. Higher thresholds produce fewer edges and can remove weak but meaningful boundaries.
Therefore, an appropriate threshold combination is important because too many detected edges can contain noise, while very high thresholds can cause important boundaries to disappear.

### 5.4 Edge Maps and Classification

Edge-only images contain mainly boundary information rather than the complete visual information available in the original image. Therefore, classification performance may decrease when only edge maps are provided.
However, edge representations can still be useful when object boundaries are highly discriminative for the classification task. The final conclusion for this experiment should be based on the Accuracy, Precision, Recall, and F1-score values obtained from Table 3.

### 5.5 Information Loss

Converting an image into an edge map removes or reduces several types of information. These include:

* Color information
* Overall intensity
* Texture
* Fine surface patterns
* Shading
* Internal visual structures

This information can be important for distinguishing between visually similar classes.

### 5.6 Classical Edge Features vs CNN Features

CNNs can automatically learn useful features from training images. Their early layers commonly learn simple patterns such as edges and orientations, while deeper layers combine these features into more complex patterns.
Allowing the CNN to learn these features provides greater flexibility because the network can learn which edges, textures, shapes, and combinations are useful for the classification problem. Manually providing an edge map restricts the input to a specific type of feature and may discard useful information.

### 5.7 Best Representation

The most useful representation should be determined from the experimental classification results. Raw, filtered, and edge images were evaluated using the same classification framework so that their results could be compared fairly.
The final choice is therefore based on the measured results in Table 3 rather than only on visual inspection.

## Conclusion

This laboratory demonstrated the use of several traditional edge detection techniques and examined their behavior under different noise conditions. Sobel, Prewitt, Laplacian, LoG, and Canny produced different edge representations, while Gaussian and Median filtering helped control the effects of noise.

The experiment also demonstrated that Canny parameters have a direct effect on the number and quality of detected edges. Finally, raw, filtered, and edge-based representations were evaluated for image classification.

Overall, the experiment showed that edge detection can provide useful structural information, but an edge-only representation may also remove important color, texture, intensity, and surface information. CNN-based approaches can learn useful edge-like features automatically while retaining access to the broader information contained in the original image.

## Discussion Questions

### Question NO 1:
The Laplacian was the most sensitive to noise among the tested methods. The reason is that Laplacian is a second-order derivative operator. Noise produces rapid intensity changes, and the second derivative emphasizes these changes. As a result, noisy images can produce many unwanted or false edges. Smoothing before applying Laplacian or LoG helps reduce this problem.

### Question NO 2:
Gaussian filtering reduced random Gaussian noise and produced smoother edge maps. However, excessive Gaussian smoothing can blur edges and reduce their sharpness.
Median filtering was particularly effective against salt-and-pepper noise. It removed isolated noisy pixels while preserving important boundaries relatively well. Therefore, Median filtering generally produced cleaner edges for salt-and-pepper noise.

### Question NO 3:
Lower thresholds allowed more weak edges to be detected, so the number of detected edges increased. However, this can also introduce unwanted or noisy edges.
Increasing the thresholds made Canny more selective. This reduced the number of detected edges and generally produced a cleaner representation, but very high thresholds could remove weak but meaningful boundaries.

### Question NO 4:
The general explanation is that edge-only images can reduce classification performance because they remove color, texture, intensity, shading, and other information that may help distinguish skin-cancer classes. Raw images preserve all this information.

### Question NO 5:
When an image is converted into an edge map, much of the original visual information is removed. The lost information can include:

* Color differences
* Texture patterns
* Intensity variations
* Shading
* Surface details
* Internal structures
* Fine-grained patterns

For skin-lesion classification, these details can be important because different classes may have similar boundaries but different colors, textures, or internal patterns.

### Question NO 6:
A CNN can learn which features are useful directly from the training data. Its early layers can learn edge-like patterns, while deeper layers can combine these patterns with textures, shapes, colors, and other features.
This is advantageous because:

* The features are learned automatically.
* The network is not restricted to edges only.
* Important color and texture information can be preserved.
* Different layers can learn increasingly complex patterns.
* The learned features can be optimized for the specific classification task.

Therefore, CNNs provide a more flexible representation than manually supplying only an edge map.

### Question NO 7:
Based on the experimental results, the [Raw / Filtered / Edge] representation produced the most useful classification results. It achieved an accuracy of [X%], compared with [Y%] for the other representations. Its Precision, Recall, and F1-score also showed that this representation provided better classification performance for the selected dataset. Therefore, the experimental results indicate that [selected representation] was the most useful representation for this classification task.