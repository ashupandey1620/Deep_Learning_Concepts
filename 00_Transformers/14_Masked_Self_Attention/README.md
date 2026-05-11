[00:00:00]  
The video is part of a deep learning playlist focused on Transformers architecture. The instructor, Nitesh, notes that the course has reached the midway point where the encoder architecture has been fully covered. The next focus is on the decoder architecture, specifically on a key component called **masked multi-head attention**, a variant of multi-head self-attention with subtle but crucial conceptual differences. This session aims to provide a deep intuition about why masked self-attention is necessary in the decoder.

[00:00:34]  
A recap is provided: the channel has uploaded 10 videos on Transformers covering the encoder and its building blocks such as self-attention, multi-head attention, positional encoding, and normalization. The encoder section is complete, and the decoder is more complex architecturally. The instructor promises a step-by-step approach to understanding decoder components before explaining the entire decoder architecture in a consolidated video. Many elements in the decoder like multi-head attention, positional encoding, add & norm layers, and feed-forward layers repeat from the encoder, but **new concepts like masked self-attention and cross-attention appear**.

[00:04:26]  
Two major building blocks in the decoder are introduced:  
- **Masked Self-Attention** (a flavor of self-attention that enforces causal constraints)  
- **Cross-Attention** (attention from decoder to encoder outputs)

The video is dedicated to explaining masked multi-head self-attention in detail to clear doubts and build a strong conceptual foundation.

[00:05:43]  
A foundational sentence is introduced:  
> "The Transformer decoder is **auto-regressive at inference time** and **non-auto-regressive at training time**."

The instructor acknowledges this may sound complex and proceeds to simplify the terms.

[00:06:22]  
Clarification of terms:  
- **Inference** means the prediction phase where the model generates outputs.  
- At inference, the decoder behaves as an **auto-regressive model**, generating one token sequentially based on previously generated tokens.  
- At training, the decoder behaves as a **non-auto-regressive model**, processing tokens in parallel without depending on previous predictions.

[00:07:13]  
**Auto-regressive models** are explained via analogy to time series forecasting:  
- They generate each data point in a sequence conditioned on previously generated points.  
- Example: Predicting stock prices day-by-day, where Friday’s price depends on Thursday’s and Wednesday’s predictions.  
- Formally, auto-regressive models generate data points sequentially, conditioning each new point on prior points.

[00:09:48]  
The instructor connects this concept to classic encoder-decoder architectures (e.g., LSTM-based sequence-to-sequence models for language translation). During decoding:  
- The decoder generates output tokens one at a time, each conditioned on previous outputs.  
- This sequential dependence is why such models are auto-regressive.  
- This property is inherent to **sequential data generation tasks** like language translation, text generation, and summarization.

[00:14:16]  
**Why are decoder models auto-regressive at inference but not at training?**  
- The sequential generation is a necessity at inference because the next token depends on the previous tokens generated so far.  
- But at training, the model knows the entire target output sequence, allowing parallel processing to speed up training.  
- This contradiction motivates the need for **masked self-attention**.

[00:16:12]  
The fundamental reason for auto-regression is that **future tokens cannot be generated before previous tokens** because each token depends on the preceding context.  
- Therefore, it is impossible to generate all tokens simultaneously during inference without sequential dependency.  
- This enforces the auto-regressive property during inference.

[00:16:52]  
However, during training, the decoder doesn’t behave auto-regressively.  
- This difference is puzzling but explained by the use of **masked self-attention**.  
- Masking ensures that during training, each token only attends to previous tokens, not future ones, effectively simulating auto-regression in a parallelizable way.

[00:18:21]  
To prove this, the instructor sets up a problem statement of building a Transformer model for English-to-Hindi translation, trained on a large dataset.

[00:20:58]  
At inference:  
- The encoder processes the entire input sentence in parallel using self-attention.  
- The decoder generates tokens one by one auto-regressively.  
- At the first timestep, the decoder receives the start token and encoder outputs to generate the first output token.  
- At subsequent timesteps, the decoder receives the encoder outputs and all previously generated tokens as input.

[00:24:01]  
If the decoder predicts an incorrect token at some timestep, it still proceeds to generate the next token conditioned on the previous outputs, characteristic of auto-regressive generation.

[00:25:29]  
At training time:  
- The ground truth output sequence is known.  
- The decoder receives the full target sequence shifted by one token (teacher forcing).  
- Regardless of the decoder’s predictions, it always receives the correct previous token from the dataset as input for the next timestep.  
- This enables parallelization because the entire target sequence is available.

[00:29:30]  
Training is still sequential (token by token) but uses **teacher forcing** where the correct previous token is fed instead of the decoder’s own prediction.  
- This makes training auto-regressive but allows for efficient gradient computation and optimization.

[00:31:06]  
However, pure auto-regressive training is slow because the decoder computations must be repeated for each token sequentially.  
- For long sequences, this can mean hundreds of sequential operations per training example, leading to slow training.

[00:32:32]  
At inference, sequential generation is unavoidable because the next token depends on previous tokens.  
- But at training, because the full target sequence is known, **parallelization is possible** by breaking the dependency on previous outputs.

[00:34:38]  
**Teacher forcing removes dependency on previous decoder outputs during training**, allowing all tokens to be processed simultaneously without waiting for previous token predictions.  
- This makes training **non-auto-regressive** and much faster.

[00:36:03]  
The focus shifts to understanding how the decoder implements this in practice via the **masked multi-head attention block**.  
- The decoder’s first block is masked multi-head self-attention.  
- Masking is key to preventing information leakage from future tokens during training.

[00:37:33]  
A reminder of self-attention operation:  
- Input tokens are first embedded into vectors.  
- Self-attention computes contextual embeddings for each token by attending to all other tokens’ embeddings in the sequence.

[00:39:21]  
Example: The word “bank” in sentences “river bank” vs. “money bank” has different contextual embeddings due to self-attention capturing different contexts.

[00:41:47]  
The self-attention mechanism produces **contextual embeddings** for each token by weighting embeddings of all tokens in the sequence based on attention scores.

[00:42:20]  
Mathematically, contextual embeddings are weighted sums of value vectors, where weights come from attention scores computed by queries and keys.

[00:44:02]  
A problem arises if the model attends to **future tokens during training**, which is not allowed during inference where future tokens are unknown.  
- Using future tokens in training introduces **data leakage** (cheating), causing the model to perform well in training but poorly at inference.

[00:46:48]  
This mismatch between training and inference behavior is a **serious issue** called **data leakage**—the model has access to “future” information during training that it won’t have at inference.

[00:48:18]  
Summary of the dilemma:  
- Auto-regressive training avoids data leakage but is slow.  
- Parallel non-auto-regressive training is fast but suffers from data leakage.  
- The question is how to get the best of both worlds.

[00:49:44]  
The answer lies in the **masking mechanism inside self-attention**.

[00:50:27]  
Detailed explanation of self-attention computations:  
- For each token embedding, three vectors are computed by multiplying with learned weight matrices:  
  - Query ($Q$)  
  - Key ($K$)  
  - Value ($V$)  

- These vectors form matrices $Q$, $K$, and $V$ stacking all tokens.

[00:52:33]  
The attention scores matrix is computed as:  
$$
\text{AttentionScores} = \frac{Q K^T}{\sqrt{d_k}}
$$  
where $d_k$ is the dimension of keys.

[00:53:52]  
Softmax is applied to attention scores to get attention weights, which are then multiplied with $V$ to produce contextual embeddings.

[00:55:14]  
For example, the contextual embedding for token "कैसे" (how) is computed as a weighted sum of the value vectors of all tokens, where weights come from attention scores.

[00:56:00]  
**Key insight:**  
During training, the model must **block attention from a token to any future tokens** to prevent data leakage.

[00:56:48]  
Masking is introduced as an additive matrix with values of $-\infty$ at positions where attention should be blocked (future tokens), and $0$ elsewhere.  
- Adding this mask before softmax forces those attention weights to zero.

[00:58:20]  
The masked attention scores matrix becomes:  
$$
\text{MaskedScores} = \text{AttentionScores} + \text{Mask}
$$  
Then  
$$
\text{AttentionWeights} = \text{Softmax}(\text{MaskedScores})
$$  
where softmax of $-\infty$ equals 0, effectively masking future tokens.

[00:59:04]  
This mask enforces **causal attention**, ensuring each token only attends to previous and current tokens.  
- This simulates auto-regressive behavior during training **while enabling parallel computation**.

[01:00:29]  
Thus, masked self-attention provides the **best of both worlds**:  
- Parallelizable training (non-auto-regressive)  
- Auto-regressive behavior at inference  
- No data leakage

The instructor emphasizes the importance of this concept and the detailed explanation was necessary to build a confident understanding.

---

### Key Insights and Concepts

| Term                     | Description                                                                                                         |
|--------------------------|---------------------------------------------------------------------------------------------------------------------|
| Auto-regressive model    | Generates each token conditioned on previously generated tokens; sequential generation.                             |
| Non-auto-regressive model| Generates tokens in parallel, without dependency on previous predictions (used in training for efficiency).          |
| Masked self-attention    | Variant of self-attention that prevents tokens from attending to future tokens, enforcing causality.                |
| Teacher forcing          | During training, feeding the correct previous token instead of model prediction to enable parallelization.          |
| Data leakage             | Occurs when model training has access to future tokens’ information not available during inference, causing errors. |
| Query, Key, Value vectors| Components computed from token embeddings used to calculate attention scores and weighted contextual embeddings.    |

---

### Timeline of Transformer Decoder Behavior

| Stage     | Behavior                                  | Reason                                                                                  |
|-----------|-------------------------------------------|-----------------------------------------------------------------------------------------|
| Training  | Non-auto-regressive (parallel)            | Full target sequence known; masking prevents data leakage; enables faster training.      |
| Inference | Auto-regressive (sequential)               | Future tokens unknown; requires sequential token generation for causality and coherence.|

---

### Summary

- Transformer decoders generate sequences auto-regressively at inference time, i.e., token-by-token, each conditioned on previously generated tokens.
- During training, the decoder can process all tokens in parallel because the entire target sequence is available.
- This is achieved via **masked multi-head self-attention**, which blocks attention to future tokens during training to prevent data leakage.
- Masking is implemented by adding a mask matrix with $-\infty$ to future token positions before softmax, ensuring zero attention to those tokens.
- This allows the model to simulate auto-regressive behavior while maintaining efficient parallel training.
- Teacher forcing feeds ground-truth previous tokens during training, further enabling parallelization without dependency on model predictions.
- Without masking, the model would “cheat” by accessing future tokens during training, harming generalization and inference performance.
- The masked self-attention mechanism thus resolves the dilemma of balancing training speed and correct sequential dependency, making Transformers highly effective for sequence generation tasks like translation.

---

The video concludes with encouragement to practice and internalize these concepts, promising further detailed videos on the decoder architecture, cross-attention, and complete Transformer understanding.