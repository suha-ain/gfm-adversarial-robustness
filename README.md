Adversarial Robustness of Graph Foundational Models

This repository contains the code, experiments, and evaluation pipeline for our research project assessing the vulnerability of Graph Foundational Models (GFMs) to adversarial attacks.

📖 Abstract

Graph Foundational Models (GFMs) have emerged as a transformative paradigm, shifting from task-specific Graph Neural Networks to versatile, task-agnostic learners. However, the move toward In-Context Learning (ICL) over graphs introduces new, unquantified adversarial risks. This project presents a systematic evaluation of the adversarial robustness of three leading GFM architectures: GOFA (Language Prompting), PRODIGY (Structural Prompting), and GraphGPT (Sequential Prompting).

We subject these models to targeted white-box attacks (Nettack) and heuristic black-box attacks (Structack) to quantify the trade-offs between zero-shot flexibility and security.

🧠 Models Evaluated

PRODIGY: Evaluated for vulnerabilities in Structural Prompt Poisoning. We test how injecting low-centrality nodes disrupts the prompt graph and task graph communication.

GOFA: Evaluated for the Semantic Shield effect, testing if an LLM-based decoder can utilize raw text features to override malicious structural noise.

GraphGPT: Evaluated for Sequence Fragility, observing how topological perturbations derail Breadth-First Search (BFS) graph serialization.

⚔️ Adversarial Attacks

Nettack (Targeted White-Box): Perturbs both graph structure and node features using a surrogate GCN to maximize classification error.

Structack (Heuristic Black-Box): A topology-only attack that injects edges between target nodes and low-centrality "intruders" from conflicting classes, maximizing the structural shock to the in-context prompt.

📊 Datasets

Cora & CiteSeer: Used to evaluate Text-Attributed Graph (TAG) robustness (Linguistic + Structural).

PolBlogs: Used to isolate and evaluate pure topological disruption (Echo Chamber structural poisoning).

🚀 Installation & Setup

Clone the repository:

git clone [https://github.com/yourusername/graph-adversarial-robustness.git](https://github.com/yourusername/graph-adversarial-robustness.git)
cd graph-adversarial-robustness


Create a virtual environment (Optional but recommended):

python -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`


Install dependencies:

pip install -r requirements.txt


💻 Usage

To run the adversarial evaluation pipeline for GraphGPT across the benchmark datasets:

python src/GraphGPT_Adversarial_Pipeline.py


(Note: Ensure you have access to a CUDA-enabled GPU, preferably an A100, for optimal inference speeds with the LLM backbones).

📈 Evaluation Metrics

The pipeline calculates the following metrics for each dataset:

Clean Accuracy (CA): Accuracy on the unperturbed graph.

Adversarial Accuracy (AA): Accuracy post-attack.

Attack Success Rate (ASR): The percentage of nodes correctly classified in the clean state that were flipped post-attack.

🤝 Contributing

Contributions, issues, and feature requests are welcome!

📝 License

This project is MIT licensed.
