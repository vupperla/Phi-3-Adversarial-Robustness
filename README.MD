# Phi-3 Adversarial Robustness

> Fine-tuning Microsoft Phi-3 Mini for credit default prediction and evaluating its robustness against adversarial attacks.

**Author:** Vin | [GitHub](https://github.com/vupperla)  
**Type:** Independent Study — Master of Science in Cybersecurity Engineering  
**Institution:** University of Washington  
**Year:** 2025 – 2026  
**Advisor:** Dr. Brent Lagesse

---

## Overview

This project is a two-part independent study exploring the security vulnerabilities of fine-tuned large language models (LLMs) in a real-world classification task.

**Part 1** covers fine-tuning Microsoft Phi-3 Mini (3.8B parameters) on the UCI Credit Card Default dataset using QLoRA — a parameter-efficient fine-tuning method that updates only 0.08% of model parameters.

**Part 2** builds on Part 1 by evaluating the fine-tuned model's robustness against two adversarial attacks — a label flip data poisoning attack and a black-box ZOO inference attack — and implements defenses against each.

---

## Project Structure

phi3-adversarial-robustness/
│
├── README.md
├── requirements.txt
│
├── part1_finetuning/
│   └── phi3_credit_finetuning.ipynb        # Fine-tuning Phi-3 Mini with QLoRA
│
└── part2_adversarial/
├── label_flip_attack.ipynb             # Label flip poisoning attack + Isolation Forest defense
└── zoo_attack.ipynb                    # ZOO black-box attack + consistency-based defense

---

## Part 1 — Fine-Tuning Phi-3 Mini

### Dataset
- **UCI Credit Card Default Dataset** — 30,000 client records from a Taiwanese bank
- Balanced to 5,000 samples (2,500 defaults / 2,500 non-defaults) to fix class imbalance

### Model
- **Microsoft Phi-3 Mini 4K Instruct** (3.8B parameters)
- Fine-tuned using **QLoRA** (4-bit quantization + LoRA adapters)
- Only ~3.15 million parameters updated (~0.08% of total)
- Trained on **Google Colab Pro** with A100 GPU — ~25–35 minutes per run

### Results
| Metric | Score |
|--------|-------|
| Accuracy | 80.8% |
| F1 Score | 0.5714 |

---

## Part 2 — Adversarial Attacks & Defenses

### Attack 1: Label Flip Poisoning
Flips 35% of training labels to corrupt model evaluation without touching the model itself.

| Metric | Clean | Poisoned | Drop |
|--------|-------|----------|------|
| Accuracy | 80.8% | 57.0% | 23.8% |
| F1 Score | 0.5714 | 0.3175 | 0.254 |

**Defense: Isolation Forest**  
Anomaly detection on training data to identify and remove poisoned samples before retraining.

| Metric | After Defense |
|--------|--------------|
| Accuracy | 74.4% |
| F1 Score | 0.5115 |
| Accuracy Recovered | 17.4% |

---

### Attack 2: ZOO (Zeroth Order Optimization) Black-Box Attack
Uses IBM's Adversarial Robustness Toolbox (ART) to flip predictions from "Default" to "No Default" by making small iterative input modifications — without access to model weights.

| Metric | Result |
|--------|--------|
| Success Rate (no defense) | 100% |
| Avg iterations per client | 1.6 |

**Defense: Consistency-Based Rate Limiting**  
Tracks per-client submission history and rejects repeated, slightly modified inputs to block ZOO's iterative probing strategy.

| Metric | Result |
|--------|--------|
| Attacks Blocked | 55% |
| Success Rate (with defense) | 45% |

---

## Key Findings

- Fine-tuned LLMs are highly vulnerable to adversarial attacks even with small perturbations
- Label flip poisoning caused a 23.8% accuracy drop with only 35% label corruption
- ZOO achieved 100% targeted misclassification before defenses were applied
- No single defense fully eliminates adversarial risk — layered defenses are necessary
- Isolation Forest recovered 17.4% accuracy against poisoning
- Consistency-based rate limiting blocked 55% of ZOO attacks

---

## Tech Stack

| Category | Tools |
|----------|-------|
| Model | Microsoft Phi-3 Mini 4K Instruct |
| Fine-tuning | QLoRA, LoRA, HuggingFace Transformers, PEFT, TRL |
| Attacks | IBM Adversarial Robustness Toolbox (ART) |
| Defenses | Isolation Forest (scikit-learn), custom consistency checker |
| Training | Google Colab Pro, A100 GPU, BitsAndBytes |
| Languages | Python |
| Libraries | PyTorch, Pandas, NumPy, Matplotlib, scikit-learn |

---

## Setup & Requirements
```bash
# Clone the repository
git clone https://github.com/vupperla/phi3-adversarial-robustness.git
cd phi3-adversarial-robustness

# Install dependencies
pip install -r requirements.txt
```

> **Note:** Notebooks were developed on Google Colab Pro with an A100 GPU. Update the Google Drive mount paths in each notebook to match your own Drive directory before running.

> **Note:** Training requires a GPU with at least 16GB VRAM. Google Colab Pro with A100 is recommended.

---

## References

1. Hu et al. "LoRA: Low-Rank Adaptation of Large Language Models." arXiv:2106.09685, 2021.
2. Dettmers et al. "QLoRA: Efficient Finetuning of Quantized LLMs." arXiv:2305.14314, 2023.
3. Chen et al. "ZOO: Zeroth Order Optimization based Black-box Attacks to Deep Neural Networks." AISec @ CCS, 2017.
4. IBM Adversarial Robustness Toolbox: https://github.com/Trusted-AI/adversarial-robustness-toolbox
5. UCI Credit Card Default Dataset: https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients

---

## License

This project is for academic and research purposes.
