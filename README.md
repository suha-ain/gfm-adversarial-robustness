# Adversarial Robustness of Graph Foundational Models (GFMs)

This repository contains the complete benchmarking suite, experimental configurations, and evaluation pipelines used to systematically analyze the security vulnerabilities of **Graph Foundational Models (GFMs)** under adversarial conditions.

---

## 📖 Abstract

Graph Foundational Models (GFMs) represent a paradigm shift in graph machine learning, transitioning from localized, task-specific Graph Neural Networks (GNNs) to unified, task-agnostic architectures capable of zero-shot in-context learning (ICL). While these models demonstrate remarkable generalization across structural and semantic boundaries, their robustness under adversarial stress remains largely unquantified.

This project presents a rigorous, empirical evaluation of three state-of-the-art GFM paradigms:
1. **Linguistic Prompting** (GOFA)
2. **Structural Prompting** (PRODIGY)
3. **Sequential Prompting** (GraphGPT)

By subjecting these architectures to targeted white-box attacks (**Nettack**) and uninformed, topology-only black-box attacks (**Structack**), we expose critical vulnerabilities within their context aggregation windows, graph serialization protocols, and multi-modal fusion layers. Our findings outline the trade-offs between zero-shot adaptivity and structural security.

---

## 🧠 Architectures & Evaluated Paradigms

We evaluate three foundational architectures that process structural data using fundamentally different representational paradigms:

```text
  +-------------------------------------------------------------------------+
  |                        GRAPH FOUNDATIONAL PARADIGMS                     |
  +--------------------+--------------------------------+-------------------+
  |      GOFA          |            PRODIGY             |     GraphGPT      |
  | (Linguistic / TAG) |    (Structural In-Context)     |  (Sequential/BFS) |
  +--------------------+--------------------------------+-------------------+
  |    Graph + Text    |        Subgraph Prompt         |  Eulerian/BFS Seq |
  |         |          |               |                |         |         |
  |         v          |               v                |         v         |
  |   Frozen LLM +     |      Pre-trained Message       |  Tokenized String |
  |  GNN Alignment     |         Passing GNN            |   Autoregressive  |
  +--------------------+--------------------------------+-------------------+
