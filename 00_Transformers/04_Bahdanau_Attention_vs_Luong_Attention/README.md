### Summary

This video provides a detailed explanation of **attention mechanisms** in neural machine translation (NMT), focusing on two primary types: **Bahdanau Attention (Additive Attention)** and **Luong Attention (Multiplicative Attention)**. The content is foundational for understanding **transformers** and their core concept of **self-attention**.

---

### Core Concepts

- **Neural Machine Translation (NMT)** involves translating a sentence from one language (e.g., English) to another (e.g., Hindi) using deep learning models.
- Initially, NMT used a **simple encoder-decoder architecture** with **LSTM** units:
  - The **encoder** processes the input sentence word-by-word and generates a single **context vector** (a fixed-size numeric summary).
  - The **decoder** uses this context vector to produce the output sentence word-by-word.
- This architecture struggles with **long sentences** (more than ~30 words) because compressing the entire sentence into one fixed-size context vector leads to the **bottleneck problem**, degrading translation quality.

---

### Attention Mechanism: Motivation and Overview

- Attention addresses the bottleneck by allowing the decoder to dynamically focus on different parts of the input sequence during each output word generation.
- Instead of relying on a single fixed context vector, the decoder uses a **set of context vectors** ($c_1, c_2, ..., c_T$), each corresponding to different input positions.
- Each context vector $c_i$ is computed as a **weighted sum** of encoder hidden states ($h_j$), where weights ($\alpha_{ij}$) represent the relevance or **alignment scores** between the decoder step $i$ and encoder positions $j$.
  
  $$ c_i = \sum_{j} \alpha_{ij} h_j $$

- The weights $\alpha_{ij}$ are calculated using **alignment scores** ($e_{ij}$), normalized by a softmax function:

  $$ \alpha_{ij} = \frac{\exp(e_{ij})}{\sum_k \exp(e_{ik})} $$

---

### Bahdanau Attention (Additive Attention)

- Introduced to solve the bottleneck issue by computing alignment scores using a **feed-forward neural network**.
- The score $e_{ij}$ is computed as:

  $$ e_{ij} = \text{v}^T \tanh(\text{W}_s s_{i-1} + \text{W}_h h_j) $$

  - $s_{i-1}$: decoder's previous hidden state (time step $i-1$)
  - $h_j$: encoder hidden state at position $j$
  - $\text{W}_s, \text{W}_h, \text{v}$: learnable parameters (weights)
- The feed-forward network **approximates** this function, leveraging neural networks’ universal approximation capability.
- This approach is also called **additive attention**.
- Key attributes:
  - Uses the **previous decoder hidden state**.
  - More parameters due to the feed-forward network.
  - Can model complex relationships, but slower and more computationally expensive.

---

### Luong Attention (Multiplicative Attention)

- Proposed as an improvement over Bahdanau Attention.
- Computes alignment scores using the **dot product** between the **current decoder hidden state** ($s_i$) and encoder hidden states ($h_j$):

  $$ e_{ij} = s_i^T h_j $$

- Key differences from Bahdanau Attention:
  - Uses the **current decoder hidden state** instead of the previous one, providing more up-to-date information for alignment.
  - Eliminates the feed-forward neural network, reducing parameters and computational overhead.
  - Faster to compute and empirically performs better on larger datasets with longer sentences.
- Known as **multiplicative attention** due to dot product calculation.
- Alignment scores are normalized by softmax as before.

---

### Detailed Architecture and Calculations

- Encoder produces a set of hidden states: $h_1, h_2, ..., h_T$.
- For each decoder time step $i$, the attention mechanism calculates weights $\alpha_{ij}$, which determine the contribution of each encoder hidden state $h_j$.
- Weighted sum of encoder states forms $c_i$, the context vector for decoding step $i$.
- Decoder updates its hidden state $s_i$ based on $c_i$ and previous outputs.
- Model parameters (weights in feed-forward network or dot product) are trained end-to-end using backpropagation and cross-entropy loss.
- Bahdanau attention uses a **neural network alignment model**, while Luong attention uses a **simpler dot product similarity**.

---

### Comparative Table: Bahdanau vs. Luong Attention

| Feature                      | Bahdanau Attention (Additive)               | Luong Attention (Multiplicative)            |
|------------------------------|---------------------------------------------|---------------------------------------------|
| Alignment score computation   | Feed-forward neural network                  | Dot product                                 |
| Decoder hidden state used     | Previous time step ($s_{i-1}$)               | Current time step ($s_i$)                   |
| Computational complexity     | Higher (due to NN)                           | Lower (simple dot product)                   |
| Number of parameters          | More (due to additional NN weights)          | Fewer                                       |
| Performance                  | Good, but slower                             | Faster, empirically better for longer inputs|
| Context vector usage          | Input to decoder at each step                 | Concatenated with decoder output            |
| Other names                  | Additive attention                            | Multiplicative attention                     |

---

### Key Insights

- **Attention mechanisms alleviate the fixed-size context vector bottleneck in sequence-to-sequence models**, improving translation quality especially for long sentences.
- **Bahdanau attention** models alignment with a neural network, allowing complex relationships but at the cost of speed and complexity.
- **Luong attention** simplifies the computation by using dot product similarity with the current decoder state, leading to improved speed and empirical performance.
- Both mechanisms are foundational to understanding **transformer models**, where self-attention is a critical component.
- The alignment scores ($\alpha_{ij}$) represent **word-to-word similarity or importance**, guiding the decoder on where to "attend" in the input sequence dynamically.
- The process involves iterative computation of alignments and context vectors for each output time step, enabling more flexible and accurate translation.

---

### Mathematical Highlights

- Context vector:

  $$ c_i = \sum_j \alpha_{ij} h_j $$

- Alignment weights (softmax normalized):

  $$ \alpha_{ij} = \frac{\exp(e_{ij})}{\sum_k \exp(e_{ik})} $$

- Bahdanau alignment score:

  $$ e_{ij} = \text{v}^T \tanh(\text{W}_s s_{i-1} + \text{W}_h h_j) $$

- Luong alignment score:

  $$ e_{ij} = s_i^T h_j $$

---

### Conclusion

This video thoroughly explains the **mechanics of attention in NMT**, highlighting the evolution from fixed context vectors to dynamic attention mechanisms. Understanding **Bahdanau and Luong attention** provides a critical foundation for grasping **transformers and self-attention**, crucial for modern NLP systems. The comparative insights and detailed formulas clarify how alignment scores and context vectors are computed and used to improve sequence modeling, especially in translation tasks.

---

### Keywords

- Neural Machine Translation (NMT)
- Encoder-Decoder Architecture
- LSTM
- Attention Mechanism
- Bahdanau Attention (Additive Attention)
- Luong Attention (Multiplicative Attention)
- Alignment Scores ($\alpha_{ij}$)
- Context Vector ($c_i$)
- Feed-forward Neural Network
- Dot Product Similarity
- Softmax Normalization
- Hidden States ($h_j$, $s_i$)
- Universal Function Approximation
- Backpropagation
- Transformer Models
- Self-Attention