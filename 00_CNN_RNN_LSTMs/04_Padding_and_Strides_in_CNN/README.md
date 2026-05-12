[00:00:00]  
This video is an instructional session by Sumit on advanced convolutional neural network (CNN) operations, continuing from the previous video that covered convolution operations. The focus here is on two additional critical operations: **padding (referred to as "wedding")** and **strides**. These operations are crucial for controlling feature map sizes and maintaining spatial information before applying convolution filters.

---

[00:00:33]  
The presenter illustrates the problem of convolution operations reducing the size of the feature map relative to the input image. For example, an input image of size $5 \times 5$ when convolved may produce a much smaller feature map (e.g., $3 \times 3$). This leads to two key issues:

- **Problem 1: Resolution Loss** — Repeated convolutions reduce the image size, causing loss of spatial information.
- **Problem 2: Border Pixel Neglect** — Pixels at the edges of the image have fewer convolutional overlaps and hence contribute less to the feature map, reducing the importance of edge information.

These problems motivate the need for padding to preserve spatial dimensions.

---

[00:02:33]  
Padding ("wedding") is introduced as the solution to the above problems. The concept is to add extra pixels (typically zeros) around the border of the input image before applying convolution. This allows:

- The output feature map to maintain the same spatial dimensions as the input.
- Border pixels to be treated equally to central pixels during convolution.
  
The presenter references the **"zero padding"** technique commonly used in CNNs and explains it helps avoid loss of information at the edges.

---

[00:03:19]  
A detailed explanation of padding calculation formulas is given. For an input size $N$ and filter size $F$, without padding, the output size is:

$$
\text{Output size} = N - F + 1
$$

When padding $P$ is applied, the formula becomes:

$$
\text{Output size} = N + 2P - F + 1
$$

This formula shows how padding $P$ can compensate for the shrinkage caused by the filter size $F$. The presenter uses graphical illustrations to explain how adding rows and columns of padding keeps the output shape stable.

---

[00:04:42]  
The presenter demonstrates the practical effect of padding by applying zero padding of size 1 around a $7 \times 7$ input image. This results in the output feature map retaining the original dimensions after convolution with a $3 \times 3$ filter. This confirms that padding solves the shrinking problem.

---

[00:05:34]  
The difference between convolving with and without padding is shown:

- **Without padding:** Feature maps shrink after each convolution layer.
- **With padding:** Feature maps maintain spatial dimensions, preserving more information.

A formula for output size with padding is emphasized as:

$$
\text{Output size} = N + 2P - F + 1
$$

where $P$ is padding, $F$ is filter size, and $N$ is input size.

---

[00:07:09]  
The presenter transitions to the next major topic: **strides**. Strides define how the convolutional filter moves across the input image. Instead of moving one pixel at a time (stride = 1), a filter can move multiple pixels (stride > 1), which reduces the spatial size of the output feature map.

- Stride controls the **step size** of the filter movement.
- Increasing stride decreases feature map size but speeds up computation.
- Padding and stride together govern the output feature map dimensions.

---

[00:10:35]  
An explanation of the stride operation is provided with movement examples. For stride $S$, the feature map size is calculated using:

$$
\text{Output size} = \left\lfloor \frac{N + 2P - F}{S} \right\rfloor + 1
$$

where $\lfloor \cdot \rfloor$ denotes the floor function (rounding down). This formula accounts for both padding and stride in determining the output map size.

---

[00:13:30]  
The presenter elaborates on the effect of stride on feature map size:

- Larger stride values result in smaller output dimensions.
- Small strides preserve more spatial detail but increase computational cost.
- Stride is a hyperparameter balancing resolution and computational efficiency.

---

[00:15:23]  
A special case with stride greater than 1 is discussed, highlighting the potential issue of **incomplete coverage** of the input image:

- Certain pixels may be skipped if stride steps do not perfectly tile the input.
- This can lead to missing information and unrepresented image regions.
- Proper padding and stride selection is crucial to avoid such gaps.

---

[00:17:54]  
The presenter offers a practical tip: when the division in the output size formula leads to a decimal (non-integer), apply the floor operation to ensure an integer output size. This addresses the problem of partial steps in stride movement.

---

[00:19:23]  
The **importance of stride rate** is discussed in the context of feature extraction:

- **Low stride values** capture fine-grained, low-level features.
- **High stride values** capture coarse, high-level features and reduce data volume.
- Choosing stride depends on the task—fine details require small strides; large-scale features allow larger strides.

---

[00:20:04]  
Two main reasons for using strides are outlined:

1. **Feature hierarchy control:** Stride modulates the level of detail captured in feature maps.
2. **Computational efficiency:** Larger strides reduce feature map size, accelerating training and inference.

With modern computational power, strides are often set to balance resolution and speed depending on the application.

---

[00:21:29]  
The presenter demonstrates stride implementation via code snippets, showing how to parameterize stride values in convolution layers. The example uses a $28 \times 28$ input image with stride settings controlling horizontal and vertical movements.

---

[00:22:46]  
Summary of learned concepts is presented:

| Concept             | Formula / Explanation                                       |
|---------------------|-------------------------------------------------------------|
| Output size (no stride, no padding) | $N - F + 1$                                      |
| Output size (with padding $P$)      | $N + 2P - F + 1$                                  |
| Output size (with padding $P$ and stride $S$) | $\left\lfloor \frac{N + 2P - F}{S} \right\rfloor + 1$ |

The model example shows how applying strides and padding changes the spatial size of feature maps while balancing information retention and computational cost.

---

[00:23:39]  
The presenter encourages viewers to experiment with 3D images and stride-padding combinations to gain deeper intuition. They also hint at upcoming videos that will cover **dilation operations** and how these techniques integrate into full CNN architectures.

---

[00:24:16]  
The video concludes with a call to action for viewers to like and subscribe, promising more detailed content on CNN operations and architectures in future videos.

---

### Key Insights

- **Padding** is essential to prevent feature map shrinkage and preserve border pixel information during convolutions.
- The output feature map size depends on input size $N$, filter size $F$, padding $P$, and stride $S$ via the formula:

  $$
  \text{Output size} = \left\lfloor \frac{N + 2P - F}{S} \right\rfloor + 1
  $$

- **Strides** control the step size of convolution filters, trading off spatial resolution against computational efficiency.
- Improper stride and padding combinations can cause information loss or incomplete coverage of the input image.
- Choosing stride and padding depends on task requirements: fine details vs. high-level features and computational constraints.
- Modern CNN architectures rely heavily on these operations to balance feature extraction depth and model efficiency.

---

### Summary Table: Effects of Padding and Strides on Feature Map Size

| Parameter        | Description                                    | Effect on Feature Map Size                  |
|------------------|------------------------------------------------|---------------------------------------------|
| $N$              | Input image dimension                          | Base size                                   |
| $F$              | Filter size                                    | Larger $F$ reduces output size              |
| $P$              | Padding (zero-padding pixels around input)    | Increases output size by $2P$                |
| $S$              | Stride (step size of filter movement)         | Larger $S$ decreases output size (downsampling) |

---

This video provides a comprehensive breakdown of padding and stride operations in CNNs, explaining their mathematical foundations, practical implications, and coding implementations, critical for designing effective convolutional architectures.