# Project Report: Transformer-Based Question Answering System in Rust (Burn)



Author: Mongameli Shasha  

Course: Software Engineering  

Repository: https://github.com/219181527/word-doc-qa  



---



# Section 1: Introduction



## 1.1 Problem Statement and Motivation



This project aims to develop a Transformer-based Question Answering (QA) system capable of answering questions about structured academic calendar documents (CPUT calendars for 2024–2026). The system reads `.docx` documents, extracts textual content, and trains a neural network to predict answer spans for natural language questions.



The motivation for this project was to:



- Implement a Transformer architecture from scratch using Rust.

- Gain hands-on experience with the Burn deep learning framework.

- Understand the challenges of building NLP systems in a systems programming language.

- Explore model training, inference, and experimentation in a low-level ML ecosystem.



Unlike using pre-trained models (e.g., BERT), this system trains a Transformer encoder from scratch on domain-specific data.



---



## 1.2 Overview of the Approach



The system consists of:



1. Document ingestion and preprocessing.

2. Custom tokenization and vocabulary building.

3. A 6-layer Transformer encoder architecture.

4. A span-based prediction head (start and end token logits).

5. A training loop with validation and checkpointing.

6. A CLI-based inference interface.



The architecture is implemented using the Burn framework with the NdArray backend and Autodiff enabled.



---



## 1.3 Key Design Decisions



Several important design decisions were made:



- Use of a Transformer encoder instead of RNN/LSTM.

- Span-based QA instead of generative text output.

- Minimum 6-layer architecture (as per assignment requirement).

- Custom tokenizer instead of external NLP libraries.

- CPU-based training using Burn’s NdArray backend.



A major decision was to train from scratch rather than fine-tune a pretrained model, to better demonstrate architectural understanding.



---



# Section 2: Implementation



---



## 2.1 Architecture Details



### Model Overview



The model is a Transformer encoder with a span prediction head.



Architecture:



Input Tokens  

→ Token Embeddings  

→ Positional Embeddings  

→ 6× Transformer Encoder Layers  

→ Linear Projection to 2 logits per token  

→ Start and End Predictions  



---



### Layer Specifications



Default Configuration:



- Vocabulary size: 125 (after tokenization)

- Maximum sequence length: 512

- d_model: 256

- n_heads: 8

- n_layers: 6

- Feed-forward dimension (d_ff): 1024

- Dropout: 0.1

- Output size: 2 (start \& end logits)



Total parameters:

≈ 6,918,146



Alternative Small Configuration:



- d_model: 128

- n_heads: 4

- n_layers: 6

- d_ff: 512



Total parameters:

≈ 2,279,426



---



### Key Components Explained



#### Multi-Head Self-Attention



Each encoder layer includes multi-head attention where:



Attention(Q, K, V) = softmax(QKᵀ / √d_k) V



This allows the model to learn contextual relationships between tokens in the calendar text.



#### Feed-Forward Network



Each layer includes a position-wise feed-forward network:



Linear(d_model → d_ff)  

ReLU  

Linear(d_ff → d_model)



This enables non-linear transformation after attention.



#### Span Prediction Head



Instead of generating text, the model predicts:



- Start token index

- End token index



This approach is common in extractive QA systems.



---



## 2.2 Data Pipeline (8 Marks)



### Document Processing



Documents are loaded from:



- data/calader_2026.docx

- data/calendar_2025.docx

- data/calendar_2024.docx



The system extracts raw text content from each document.



Example log: [data] Loaded 'data/calader_2026.docx' (239 bytes)





---



### Tokenization Strategy



A custom tokenizer was implemented:



- Lowercasing

- Whitespace splitting

- Vocabulary building from training data

- Mapping tokens to integer IDs

- Padding to max sequence length



Final vocabulary size:

125 tokens



---



### Training Data Generation



The system generates Q\&A pairs from document content.



Total samples generated:

31 Q\&A pairs



Split:

- 27 training samples

- 4 validation samples



This extremely small dataset significantly influenced model behavior.



---



## 2.3 Training Strategy (7 Marks)



### Hyperparameters



Default Configuration:



- Epochs: 30

- Batch size: 8

- Learning rate: 1e-4

- Validation split: 0.15

- Optimizer: Adam

- Backend: NdArray + Autodiff



---



### Optimization Strategy



- Full backpropagation through 6-layer Transformer.

- Gradients computed using Burn Autodiff.

- Model updated using Adam optimizer.

- Checkpoints saved every 5 epochs.



---



### Challenges Faced and Solutions



This project involved extensive debugging and trial-and-error:



#### 1. Backend Feature Errors



Errors such as: could not find ndarray in backend





Solution:

Enable required features in Cargo.toml:

- ndarray

- autodiff

- dataset



---



#### 2. Debug Mode Training Freeze



Training initially appeared frozen at: \[train] Starting training…





Cause:

Running in debug mode (unoptimized build).



Solution:

Use: cargo run --release -- train





This reduced execution time dramatically.



---



#### 3. GitHub Authentication Issues



Encountered 403 errors due to multiple GitHub accounts.



Solution:

- Generated Personal Access Token

- Cleared Windows Credential Manager

- Re-authenticated as correct account



---



#### 4. Overfitting



Model reached:



- 100% training accuracy

- 75% validation accuracy



Cause:

Very small dataset (27 training samples).



Solution:

Compared smaller model configuration to test generalization.



---



# Section 3: Experiments and Results (50 Marks)



---



## 3.1 Training Results (20 Marks)



Final Training Summary:



- Total parameters: 6.9M

- Total training time: 2928.4 seconds (~49 minutes)

- CPU-based training

- Backend: NdArray



Final metrics:



Train Accuracy: 100%  

Validation Accuracy: 75%  



Loss decreased steadily across epochs.



Observation:

Clear overfitting after epoch 12.



---



## 3.2 Model Performance (20 Marks)



Example Questions:



1. Q: What is the month and date of the 2026 End of Year Graduation Ceremony?  

&nbsp;  A: The 2026 End of Year Graduation Ceremony is held in December 2026.



2. Q: When does Term 1 start in 2026?  

&nbsp;  A: 23 March: START OF TERM 2 (Incorrect)



3. Q: How many times did the HDC hold meetings in 2024?  

&nbsp;  A: [Correct if matches training sample]



---



### What Works Well



- Exact training-style questions are answered correctly.

- Span prediction is stable.

- Model loads and performs inference reliably.

- Architecture is fully functional.



---



### Failure Cases



- Rephrased questions fail.

- Model confuses similar events (Term 1 vs Term 2).

- Generalization limited due to small dataset.



Root Cause:

Dataset size too small for 6.9M parameter model.



---



## 3.3 Configuration Comparison



| Configuration | Parameters | Learning Rate | Batch Size |

|--------------|------------|---------------|------------|

| Default      | 6.9M       | 1e-4          | 8          |

| Small        | 2.2M       | 3e-4          | 4          |



Observation:



- Default model memorizes training data faster.

- Small model generalizes slightly better.

- Both models overfit due to dataset size.



---



# Section 4: Conclusion (15 Marks)



## What I Learned



- How Transformer architectures operate internally.

- How attention mechanisms are implemented.

- How training loops work in Rust.

- How feature flags affect Burn backend configuration.

- How overfitting manifests in small datasets.



---



## Challenges Encountered



- Backend configuration errors.

- Autodiff feature gating issues.

- Slow debug-mode training.

- Git authentication conflicts.

- Span prediction alignment.



Each issue required debugging, documentation review, and iterative fixes.



---



## Potential Improvements



- Increase training dataset size.

- Use pre-trained embeddings.

- Add better tokenization (subword/BPE).

- Early stopping based on validation loss.

- GPU backend support.



---



## Future Work



- Replace extractive QA with generative QA.

- Add web interface.

- Integrate vector database retrieval.

- Deploy as a REST API.

- Fine-tune a pretrained model for comparison.



---



# Final Reflection



This project demonstrated the complexity of building modern NLP systems from scratch in Rust. While performance is limited by dataset size, the system successfully implements a complete Transformer-based QA pipeline including training, validation, checkpointing, and inference.



The extensive debugging process provided valuable insight into both deep learning systems and Rust ecosystem tooling.



