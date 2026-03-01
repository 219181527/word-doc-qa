# 🧠 word-doc-qa



A Transformer-based Question Answering system built from scratch in **Rust** using the **Burn** deep learning framework.



This project implements a 6-layer Transformer encoder designed to answer questions specifically about CPUT academic calendar documents (2024–2026). Unlike projects that wrap pretrained models, this architecture is trained from the ground up to demonstrate deep learning fundamentals.



---



## 📌 Project Overview



The system follows a full NLP pipeline:

* **Data Ingestion:** Loads and parses `.docx` calendar documents.

* **Preprocessing:** Custom tokenization and vocabulary building.

* **Modeling:** A Transformer encoder implemented in Rust.

* **Task:** Extractive QA—predicting answer spans (start and end tokens).

* **Interface:** CLI-based training and inference.



---



## 🏗️ Architecture



The model is built using the **Burn** framework with an **NdArray** backend and **Autodiff** enabled.



### Model Configurations



| Feature | Default Configuration | Small Configuration |

| :--- | :--- | :--- |

| **Layers** | 6 Transformer Encoders | 6 Transformer Encoders |

| **Embedding Size** | 256 | 128 |

| **Attention Heads** | 8 | 4 |

| **Feed-forward Dim** | 1024 | 512 |

| **Total Parameters** | **~6.9 Million** | **~2.2 Million** |



---



## 📊 Training Summary



The model was trained on a specialized local dataset with the following results:



* **Training Samples:** 27

* **Validation Samples:** 4

* **Final Training Accuracy:** 100%

* **Final Validation Accuracy:** 75%

* **Training Time:** ~49 minutes (CPU-bound)



---



## 🚀 Usage



Ensure you have the [Rust toolchain](https://rustup.rs/) installed.



### 1. Train the Model

`cargo run --release -- train`



### 2. Ask a Specific Question

`cargo run --release -- ask "What is the month and date of the 2026 End of Year Graduation Ceremony?"`



### 3. Run Demo Questions

`cargo run --release -- demo`



---



## 📂 Project Structure

```text
word-doc-qa/
├── src/
│   ├── config.rs      # Model & Training hyperparameters
│   ├── data.rs        # Document loading & dataset logic
│   ├── model.rs       # Manual Transformer implementation
│   ├── training.rs    # Burn training loop & learner
│   ├── inference.rs   # Prediction logic
│   └── tokenizer.rs   # Vocabulary & token mapping
├── data/              # CPUT Calendar documents (.docx)
├── docs/
│   └── REPORT.md      # Detailed academic report
├── Cargo.toml         # Rust dependencies (Burn, etc.)
└── README.md          # Project documentation
```
---



## 🧪 Experiments \& Observations



Two configurations were compared to test generalization on a small dataset:



| Model | Parameters | Learning Rate | Batch Size |

| :--- | :--- | :--- | :--- |

| **Default** | 6.9M | 1e-4 | 8 |

| **Small** | 2.2M | 3e-4 | 4 |



**Key Insights:**

> * **Overfitting:** The default model overfits quickly due to the small sample size (27 samples).

> * **Generalization:** The **Small** model showed slightly better generalization on unseen validation data.

> * **Reliability:** Span-based extractive QA works reliably for training-style questions.



Full experimental details are available in:

📄 **docs/REPORT.md**



---



## ⚠️ Limitations & Future Work



* **Dataset Size:** Currently limited to 27 training samples.

* **Tokenizer:** Uses a basic custom tokenizer; future versions could implement **BPE (Byte Pair Encoding)**.

* **Hardware:** Optimized for CPU; **WGPU/LibTorch** backends could be implemented for GPU acceleration.

* **Generalization:** Limited ability to handle "out-of-distribution" phrasing.



---



## 📚 Technologies Used



* **Rust**

* **Burn** (Deep Learning Framework)

* **NdArray** backend

* **Autodiff**

* **Git & GitHub**



---



## 🎓 Academic Context



Developed as part of a deep learning/NLP assignment focusing on:

1. Custom Transformer implementation.

2. Training-from-scratch methodologies.

3. Empirical comparison of model hyperparameters.

4. Detailed project report.



**Author:** [Mongameli Shasha](https://github.com/219181527)



