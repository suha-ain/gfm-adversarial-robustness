# Adversarial Robustness of Graph Foundational Models (GFMs)

This repository contains the complete benchmarking suite, experimental configurations, and evaluation pipelines used to systematically analyze the security vulnerabilities of **Graph Foundational Models (GFMs)** under adversarial conditions.

---

## 📖 Abstract

Graph Foundational Models (GFMs) represent a paradigm shift in graph machine learning, transitioning from localized, task-specific Graph Neural Networks (GNNs) to unified, task-agnostic architectures capable of zero-shot in-context learning (ICL). While these models demonstrate remarkable generalization across structural and semantic boundaries, their robustness under adversarial stress remains largely unquantified.

This project presents a rigorous, empirical evaluation of three state-of-the-art GFM paradigms. By subjecting these architectures to targeted white-box attacks (**Nettack**) and uninformed, topology-only black-box attacks (**Structack**), we expose critical vulnerabilities within their context aggregation windows, graph serialization protocols, and multi-modal fusion layers. Our findings outline the trade-offs between zero-shot adaptivity and structural security.

---

## 🧠 Architectures & Evaluated Paradigms

### 1. GOFA: Linguistic Prompting (Hybrid GNN+LLM)
* **Mechanics:** Combines a GNN encoder with a frozen, pre-trained LLM decoder (such as Mistral-7B). It represents graphs as Text-Attributed Graphs (TAGs), projecting topological features and semantic descriptions into a shared embedding space.
* **Failure Mode / Hypothesis:** Actively tests the "Semantic Shield" hypothesis. We evaluate if the LLM's strong text-embedding representations can override malicious structural edits, or if textual modifications can be used to bypass topological filtering.

### 2. PRODIGY: Structural Prompting (Pure In-Context GNN)
* **Mechanics:** Enables in-context learning directly over graphs without relying on natural language translators. It extracts localized subgraphs around target nodes alongside labeled support nodes, utilizing structural similarity matching.
* **Failure Mode / Hypothesis:** Probes "Structural Prompt Poisoning." Since PRODIGY depends entirely on the immediate neighborhood context to infer node classes, injecting heterophilous (out-of-class) structural noise into the prompt window should systematically degrade its in-context matching capabilities.

### 3. GraphGPT: Sequential Prompting (Pure LLM Sequence Model)
* **Mechanics:** Discards traditional message-passing layers entirely. It flattens 2D graphs into 1D sequences of text tokens using deterministic graph-traversal algorithms (e.g., Breadth-First Search/BFS or Eulerian paths) before running auto-regressive next-token prediction.
* **Failure Mode / Hypothesis:** Tests the "Sequence Fragility" hypothesis. Because graph-to-sequence mapping is highly sensitive, minor structural additions can break traversal pathways, altering the sequential token stream and leading to catastrophic prediction failures.

---

## ⚔️ Adversarial Threat Model

We implement and compare two highly distinct adversarial threat vectors representing different levels of attacker access and computational capacity:

### 1. Nettack (Targeted, Gradient-Based, White-Box)
* **Objective:** Maximize the classification loss of a specific target node *v* by executing the minimal number of structural or feature modifications.
* **Methodology:** Trains a surrogate 2-layer GCN to calculate loss gradients. It greedily selects edge additions/deletions and node feature flips while maintaining strict constraints to ensure perturbations are statistically unnoticeable (preserving degree distributions and feature co-occurrence).
* **Target Setting:** White-box poisoning.

### 2. Structack (Targeted, Heuristic, Black-Box)
* **Objective:** Degrade GFM classification performance without relying on model gradients, node labels, or feature information.
* **Methodology:** A topology-only, black-box attack that connects target nodes to structurally distant, low-centrality "intruders" from conflicting classes. This exploits message-passing normalization functions to inject maximum heterophilous noise into the neighborhood context.
* **Target Setting:** Black-box evasion.

---

## 📊 Dataset Specifications

The benchmarking suite leverages three classical datasets curated to evaluate specific dimensions of structural and semantic robustness:

| Dataset | Graph Type | Nodes | Edges | Classes | Primary Evaluation Focus |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Cora** | Citation | 2,708 | 5,429 | 7 | Baseline verification; linguistic & structural robustness. |
| **CiteSeer** | Citation | 3,327 | 4,732 | 6 | Sparse topology evaluation; vulnerability to neighborhood poisoning. |
| **PolBlogs** | Social Network | 1,490 | 19,025 | 2 | Pure topological disruption; echo chamber structural manipulation. |

---

## 🚀 Installation & Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/suha-ain/gfm-adversarial-robustness.git
cd graph-adversarial-robustness
```

### 2. Create and Activate Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install GPU-Optimized Dependencies
For optimal inference speeds with the LLM backbones, a CUDA-enabled GPU (such as an NVIDIA A100 or H100) is highly recommended.

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
pip install torch_geometric
pip install deeprobust
pip install transformers accelerate bitsandbytes networkx scipy scikit-learn
```

---

## 💻 Directory Structure & Usage

### Codebase Organization

```text
graph-adversarial-robustness/
│
├── README.md                              # Project documentation
├── requirements.txt                       # Dependency listings
├── Dataset_Descriptions.md                # Detailed dataset specs
├── Selected_GFMs_Implementation.md        # Technical breakdown of GFM implementations
│
├── src/
│   ├── generate_attacks.py                # Core adversarial graph generator
│   ├── GraphGPT_Adversarial_Pipeline.py   # Sequential prompting pipeline
│   ├── gofa_full_adversarial_pipeline.py  # Language prompting evaluation pipeline
│   ├── gofa_metrics_pipeline.py           # GOFA evaluation metrics pipeline
│   └── prodigy_metrics_pipeline.py        # In-context GNN evaluation pipeline
│
└── adversarial_datasets/                 # Output folder for generated poisoned graphs
```

### Running the Evaluation Pipelines
To generate the adversarial perturbations and evaluate each Graph Foundational Model paradigm, execute the corresponding pipeline scripts:

#### 1. Generate Adversarial Graphs
Generate the poisoned adjacency and feature matrices (`.npz` files) before feeding them to the GFM pipelines:

```bash
python src/generate_attacks.py
```

#### 2. Run the GOFA (Language Prompting) Evaluation

```bash
python src/gofa_full_adversarial_pipeline.py
```

#### 3. Run the PRODIGY (Structural Prompting) Evaluation

```bash
python src/prodigy_metrics_pipeline.py
```

#### 4. Run the GraphGPT (Sequential Prompting) Evaluation

```bash
python src/GraphGPT_Adversarial_Pipeline.py
```

---

## 📈 Evaluation Metrics
The experimental framework computes three key performance indicators (KPIs) to quantify adversarial vulnerability:

* **Clean Accuracy (CA):** The baseline classification accuracy of the GFM evaluated on unperturbed, clean graph topologies.
* **Adversarial Accuracy (AA):** The classification accuracy of the GFM evaluated on the perturbed test sets under a fixed perturbation budget.
* **Attack Success Rate (ASR):** The percentage of test nodes that were classified *correctly* in their clean state but flipped to *incorrect* classifications after the adversarial graph modifications were applied:

$$ASR=\frac{\text{Successful Node Flips}}{\text{Originally Correct Clean Predictions}}\times100\%$$

---

## 🤝 Contributing
Contributions, bug reports, and structural attack suggestions are highly welcome! Please open an issue or submit a pull request for new defense integrations or model backbones.

---

## 📝 License
This project is licensed under the MIT License - see the LICENSE file for details.
