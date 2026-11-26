# CNN Visualization & Critical Analysis

## Results Used for the Analysis

* **Dataset:** CIFAR-10.
* **Selected image:** Automobile (RGB image, $32 \times 32 \times 3$).
* The five filters produced the four required stages: raw convolution, ReLU, max pooling, and average pooling.
* Stage A and Stage B feature maps have dimensions $32 \times 32$; Stage C and Stage D have dimensions $16 \times 16$.
* **Model A** (two $3 \times 3$ convolution layers): 710 trainable parameters.
* **Model B** (two $5 \times 5$ convolution layers): 1,750 trainable parameters.
* The framework parameter counts matched the manual calculations.

## Generated $5 \times 4$ Visualization Grid

![CNN Feature Visualization: Five Filters and Four Stages](figure_1.png)

*Figure 1. Feature maps generated from the selected CIFAR-10 automobile image.*

---

## Question 1 — The ReLU “Blackout” Effect & Activation Alternatives

In the Horizontal Edge row (Row 1) and Vertical Edge row (Row 2), Stage A contains both positive and negative responses. In Stage B, the negative responses become zero and appear black or very dark on the viridis color scale. In the horizontal-edge map, the blue/negative regions around the detected horizontal boundaries disappear. In the vertical-edge map, the blue/negative regions around vertical boundaries also disappear. The positive edge responses remain visible.

These negative values occur because the Sobel filters subtract intensity values on one side of an edge from values on the other side. The sign indicates the direction or polarity of the intensity change. ReLU is defined as $\max(0, x)$, so every negative response becomes zero.

With Leaky ReLU ($\alpha = 0.1$), a negative value would be multiplied by $0.1$ instead of being set to zero, so faint negative responses would remain visible. Sigmoid would map every value to a value between $0$ and $1$; negative responses would become values below $0.5$ rather than zero, and strong positive and negative values would be compressed toward $1$ and $0$ respectively.

**Evidence from the grid:** Compare Stage A and Stage B in Rows 1 and 2. The blue regions in Stage A are removed in Stage B, while the bright positive edge responses remain.

---

## Question 2 — Max Pooling vs. Average Pooling Information Retention

In the Sharpen row (Row 3), Stage C (Max Pooling) retains strong local activations around the automobile’s outlines and sharper boundaries. Stage D (Average Pooling) looks smoother because each $2 \times 2$ region is represented by its average, which reduces the effect of a single strong response when neighbouring values are smaller. A similar difference can be seen in the Horizontal Edge and Vertical Edge rows (Rows 1 and 2): the strongest edge responses are more prominent in Stage C, while Stage D is smoother.

For dark ink text on a white background, assuming the feature map gives dark strokes strong positive activations, Max Pooling would generally preserve thin strokes better because it selects the strongest response in each $2 \times 2$ region. Average Pooling can weaken a thin stroke by averaging it with surrounding background values. If the map instead represents dark pixels as low raw intensity values, a minimum-based operation may be more suitable; the result depends on how the text is encoded.

**Evidence from the grid:** Compare Columns C and D in Row 3. The sharpened outlines remain more locally pronounced after Max Pooling, whereas Average Pooling produces a smoother map.

---

## Question 3 — Parameter Explosion & Receptive Field Equivalence

The total parameter count rises from $710$ in Model A to $1,750$ in Model B. The percentage increase is:

$$\frac{1,750 - 710}{710} \times 100 = 146.48\%$$

Two consecutive $3 \times 3$ convolution layers with stride $1$ and no dilation have an effective receptive field of $5 \times 5$. This follows because the second layer combines neighbouring outputs from the first layer: $3 + (3 - 1) = 5$. Therefore, two stacked $3 \times 3$ layers are required.

For a concrete parameter comparison using the second convolution’s channel sizes ($5$ input channels and $10$ output channels), one $5 \times 5$ convolution has:

$$(5 \times 5 \times 5 + 1) \times 10 = 1,260 \text{ parameters}$$

Replacing it with two $3 \times 3$ layers using $5$ intermediate channels gives:

$$(3 \times 3 \times 5 + 1) \times 5 = 230$$
$$(3 \times 3 \times 5 + 1) \times 10 = 460$$

This results in $690$ parameters total, saving $1,260 - 690 = 570$ parameters (about $45.24\%$) for that block. This comparison assumes the stated $5$-channel intermediate layer and includes biases.

> **Note:** The receptive field is equivalent in spatial size, but two $3 \times 3$ layers are not mathematically identical to every possible single $5 \times 5$ filter; the stacked layers also introduce an intermediate representation and usually an activation.

---

## Question 4 — Operation Commutativity (Order Invariance)

The outputs are identical when Max Pooling and ReLU are exchanged, provided they operate on the same $2 \times 2$ patch. ReLU is monotonic, so the maximum of the ReLU-transformed values equals ReLU applied to the maximum value.

**Example patch:**

| | |
| :---: | :---: |
| $-1$ | $2$ |
| $-3$ | $4$ |

* **$\text{Conv} \rightarrow \text{ReLU} \rightarrow \text{MaxPool}$:** ReLU gives `[[0, 2], [0, 4]]`, and the maximum is $4$.
* **$\text{Conv} \rightarrow \text{MaxPool} \rightarrow \text{ReLU}$:** The maximum of the original patch is $4$, and $\text{ReLU}(4) = 4$. Both orders therefore produce $4$.

**Efficiency implication:** If pooling is performed first, ReLU is applied to fewer values because the $2 \times 2$ pooling operation reduces the spatial dimensions. This can reduce the number of activation operations. The numerical result remains the same under the conditions above.

---

## Question 5 — Diagnosing Filter Redundancy (Debugging Scenario)

1. **Identical or highly similar initialization:** If all filters start with the same weights, they can learn similar features and produce nearly identical feature maps. 
   * **Fix:** Use an independent random initialization method such as He initialization for ReLU networks, so each filter starts with different weights.
2. **Dead ReLU units or unsuitable optimization settings:** Filters that repeatedly receive negative inputs can output zero after ReLU and receive little or no useful gradient. An unsuitable learning rate can also prevent filters from learning useful, distinct features. 
   * **Fix:** Try Leaky ReLU to retain a gradient for negative inputs and tune the learning rate using a validation set.

---

## Parameter Audit Summary

| Model | Conv1 | Conv2 | Total parameters |
| :--- | :---: | :---: | :---: |
| **Model A ($3 \times 3$)** | 140 | 460 | 710 |
| **Model B ($5 \times 5$)** | 380 | 1,260 | 1,750 |

The parameter audit in the notebook confirms that the manual counts and TensorFlow framework count match for both models.