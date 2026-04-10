# 🛍️ ShopPerBench: Personalization Meets Explainable Reasoning in E-commerce

<p align="center">
  <b>The First Multi-Task Benchmark for Personalized E-commerce Reasoning Based on Real-World, Long-Horizon User Logs</b>
</p>

<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#highlights">Highlights</a> •
  <a href="#benchmark">Benchmark</a> •
  <a href="#pipeline">BiWalker-Shop</a> •
  <a href="#tasks">Tasks</a> •
  <a href="#results">Results</a> •
  <a href="#analysis">Analysis</a> •
  <a href="#dataset">Dataset</a> •
  <a href="#citation">Citation</a>
</p>

---

## 📌 Overview

Large language models (LLMs) are transforming e-commerce, yet **evaluating their reasoning in complex, long-term, and personalized shopping scenarios remains a critical challenge**. Existing benchmarks rely on isolated behaviors or simulated environments, failing to capture the causal logic of real-world interactions.

We introduce **ShopPerBench** — a benchmark built on **authentic, long-horizon user logs** (up to 12 months) from a production e-commerce engine (TaoBao). It systematically evaluates LLMs across 7 carefully designed tasks spanning product knowledge, user understanding, and personalized decision-making.

> 💡 **Key Finding:** Even top-tier "Think" models (o4 mini, GPT-5, Gemini-3-flash) achieve a **peak average success rate of only 75.38%**, revealing critical gaps in personalized e-commerce reasoning.

---

## ✨ Highlights

- 🏪 **Real-World Data** — Built from production-level TaoBao user logs and product catalogs, not simulated environments
- 📅 **Long-Horizon Modeling** — Captures user preference evolution across up to **12 months** of interaction history
- 🕸️ **Dual-Domain Graph Architecture** — Couples Item Attribute Knowledge Graph (IA-KG) with User Behavior Profile Decision Tree (UBP)
- 🧩 **7 Specialized Tasks** — Covering product knowledge, user modeling, and personalized recommendation
- 📊 **7,965 High-Quality Instances** — With multi-stage expert review and quality control
- 🤖 **17 SOTA LLMs Evaluated** — Including GPT-5, o4 mini, Gemini-3-flash, DeepSeek R1, Claude Sonnet4, Qwen3 series
- 🔍 **Deep Reasoning Analysis** — Uncovers "strategic shortcuts", "reasoning overfitting", and cascading failure mechanisms

---

## 📊 Benchmark Comparison

ShopPerBench fills a critical gap that existing benchmarks cannot address:

| Dataset | Instances | Tasks | Real-world | Reasoning | Long-term | Product Knowledge | User Behavior | Personalized |
|---------|-----------|-------|------------|-----------|-----------|-------------------|---------------|--------------|
| Amazon-M2 | 361,659 | 3 | ✅ | ❌ | ❌ | ❌ | Partial | ❌ |
| Amazon-ESCI | 178,952 | 3 | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| ECInstruct | 9,253 | 10 | ❌ | ❌ | ❌ | Partial | ❌ | ❌ |
| Shopping MMLU | 20,799 | 57 | ✅ | Partial | ❌ | Partial | Partial | ❌ |
| WebShop | 500 | 1 | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |
| ShoppingBench | 900 | 4 | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |
| **ShopPerBench (Ours)** | **7,965** | **7** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

---

## 🏗️ BiWalker-Shop: Synthetic Data Pipeline

<p align="center">
  <img src="KDD_benchmark 2/pic/shopperbench.pdf" alt="BiWalker-Shop Pipeline" width="90%"/>
  <br/>
  <em>The BiWalker-Shop pipeline: solid circles represent products, dashed circles represent attributes.</em>
</p>

Our novel **BiWalker-Shop** framework is a graph-driven synthesis pipeline consisting of four components:

### 1. IA-Knowledge Graph
Built from a **trillion-scale item library**, the IA-Knowledge Graph `G = (I, A, E)` encodes fine-grained product taxonomies with:
- **10,000+ unique item nodes** across 20+ major product categories
- **50,000+ fine-grained attribute nodes** (brands, prices, specifications, etc.)
- LLM-augmented annotation to align heterogeneous descriptors into a canonical vocabulary
- Multi-stage privacy and safety audits

### 2. UBP-Decision Tree
A hierarchical user representation framework `T_u = (B_u, P_u)` with:
- **Behavior Branch** `B_u`: Captures interaction sequences (Search, Click, Cart, Buy, Review) across multi-scale temporal windows (`<1 week`, `<1 month`, `>1 year`)
- **Profile Branch** `P_u`: LLM-induced semantic features including user Interests, Roles ("parenting expert", "outdoor enthusiast"), and Core Preferences
- **30,000+ anonymized users** with over **1 million raw interaction events**

### 3. Dual-Domain Coupling & Walk Strategy
Three **Bridge Modules** (`Intent`, `Need`, `Sentiment`) semantically couple the IA-KG and UBP-Decision Tree, enabling constrained meta-path walking:
- **Reasoning depth**: 1–5 hops, controllable for task complexity
- **Reasoning width**: Parallel path diversity for multi-constraint evaluation
- Formal path slice: `ρ = (n₁, r₁, n₂, r₂, ..., nₖ)` where `k ∈ [1,5]`

### 4. Quality Control
- ✅ **Trajectory Authenticity**: Every path is a structural restoration of real interaction logs
- ✅ **Distractor Criteria**: Logically plausible, difficulty-calibrated, uniqueness-guaranteed wrong options
- ✅ **Expert Arbitration**: Multi-person, multi-round verification across 4 dimensions (real-world plausibility, linguistic specification, logical uniqueness, option homogeneity)

---

## 📋 Task Overview

<p align="center">
  <img src="KDD_benchmark 2/pic/task_taxonomy.png" alt="Task Taxonomy" width="50%"/>
  <br/>
  <em>Task taxonomy of ShopPerBench across three core reasoning capabilities.</em>
</p>

ShopPerBench covers **7 tasks** across three capability domains:

| Task | Instances | Avg. Tokens | Avg. Hops | Capability Domain |
|------|-----------|-------------|-----------|-------------------|
| Product Attribute | 1,171 | 1,397 | 1.2 | Product Knowledge & Relation Reasoning |
| Accessory Substitute | 917 | 1,297 | 2.1 | Product Knowledge & Relation Reasoning |
| User Profiling | 821 | 3,139 | 2.4 | User Understanding & Behavior Modeling |
| User Interest Prediction | 1,008 | 3,772 | 2.8 | User Understanding & Behavior Modeling |
| User Intent Mining | 1,478 | 1,197 | 3.5 | Demand Recognition & Personalized Decision |
| User Behavior Inference | 1,396 | 1,491 | 4.2 | Demand Recognition & Personalized Decision |
| Personalized Recommendation | 1,174 | 1,223 | 4.7 | Demand Recognition & Personalized Decision |

> 📝 **Note**: User Profiling (3,139 avg. tokens) and User Interest Prediction (3,772 avg. tokens) are significantly more token-intensive, reflecting the challenge of aggregating long-term personalized behavioral sequences.

---

## 🏆 Main Results

### Overall Performance of 17 LLMs

| Model | Product Attr. | Access. Subst. | User Profiling | User Interest | User Intent | User Behavior | Pers. Recom. | **Avg.** |
|-------|--------------|----------------|----------------|--------------|-------------|--------------|--------------|---------|
| GPT-4o mini | 58.50 | 27.59 | 46.89 | 53.27 | 45.87 | 57.88 | 62.86 | 50.41 |
| GPT-4o | 75.83 | 35.33 | 46.77 | 50.29 | 57.78 | 75.50 | 71.12 | 58.95 |
| GPT-4.1 | 79.42 | 44.17 | 57.98 | 54.17 | 69.76 | 72.85 | 67.04 | 63.63 |
| DeepSeek V3 | 71.91 | 37.51 | 61.39 | 60.42 | 66.78 | 80.80 | 67.63 | 63.78 |
| Qwen3-235B-instruct | 78.99 | 49.95 | 59.32 | 73.31 | 74.15 | 82.16 | 67.38 | 69.32 |
| o3 | **88.56** | 61.05 | 52.98 | 58.83 | 82.27 | 60.82 | 62.86 | 66.77 |
| o4 mini | 88.39 | 57.69 | **62.97** | 75.49 | 88.36 | 85.33 | 68.19 | 75.20 |
| GPT-5 | 87.25 | 60.51 | 60.89 | 70.91 | 89.76 | 85.22 | 67.34 | 74.55 |
| DeepSeek R1 | 86.25 | 60.41 | 59.93 | 62.40 | 85.12 | **86.39** | 66.10 | 72.37 |
| Qwen3-235B-think | 84.63 | **63.69** | 61.02 | 66.96 | **89.17** | 85.89 | 64.31 | 73.62 |
| Claude Sonnet4 | 77.97 | 50.16 | 54.81 | 63.69 | 65.83 | 85.10 | 65.84 | 66.20 |
| **Gemini-3-flash** | 85.36 | 65.67 | 61.35 | **82.33** | 73.86 | 83.15 | **75.92** | **75.38** |

> 🥇 **Top performers**: Gemini-3-flash (75.38%), o4 mini (75.20%), GPT-5 (74.55%)  
> 🔴 **Hardest task**: Accessory Substitute (avg. 50.19%) — even top models barely cross 65%

---

## 🔬 Key Analyses

### RQ1: "Think" vs "No-Think" — Gain Asymmetry

<p align="center">
  <img src="KDD_benchmark 2/pic/radar_deepseek.png" alt="DeepSeek Think vs No-Think" width="28%"/>
  <img src="KDD_benchmark 2/pic/radar_qwen3-235b-a22b.png" alt="Qwen3-235B Think vs No-Think" width="28%"/>
  <img src="KDD_benchmark 2/pic/radar_qwen3-30b-a3b.png" alt="Qwen3-30B Think vs No-Think" width="28%"/>
  <br/>
  <em>Performance comparison of "Think" vs "No-Think" model versions: DeepSeek (left), Qwen3-235B (center), Qwen3-30B (right).</em>
</p>

A pronounced **"gain asymmetry"** emerges across model families:

- ✅ **"Think" wins** on strongly constrained logical tasks (Product Attribute, Intent Mining, Accessory Substitute) — improvements up to **+18.94 points**
- ❌ **"Think" loses** on preference-driven tasks (User Profiling, Interest Prediction) — models fall into **"Reasoning Overfitting"**, treating stochastic user preferences as deterministic causalities

### RQ2: Crucial Capabilities for E-commerce Reasoning

<p align="center">
  <img src="KDD_benchmark 2/pic/ds_7task_8tag_normalized_to_sum.png" alt="Counterfactual Importance Scores" width="60%"/>
  <br/>
  <em>Distribution of counterfactual importance scores across 7 tasks from DeepSeek R1's chain of thought.</em>
</p>

Using **Counterfactual Importance Scores (CIS)** on 100 correct reasoning traces per task, we identify two distinct thinking archetypes:
- **Abstraction-Driven Reasoning** (User Profiling): Excels at pattern summarization but prone to overconfidence
- **Alignment-Driven Reasoning** (User Interest Prediction): Focuses on logical validation but faces evidence brittleness

### RQ3: Reasoning Complexity — The Knowledge Constraint Barrier

<p align="center">
  <img src="KDD_benchmark 2/pic/reasoning_hops_optimized.png" alt="Reasoning Hops Analysis" width="55%"/>
  <br/>
  <em>Performance evolution across reasoning hops (1–5) for various LLMs.</em>
</p>

A non-linear relationship between hops and accuracy reveals:
- **2-hop barrier**: Despite low hop count, Accessory Substitute and User Profiling require dense domain knowledge — causing notable performance dips
- **3-hop divergence**: "Think" models gain +30.58% over "No-Think" at the intent mining level
- **5-hop ceiling**: Even advanced reasoning architectures struggle with 12-month history aggregation and multi-objective optimization

### RQ4: Ablation Studies

<p align="center">
  <img src="KDD_benchmark 2/pic/scale_up.png" alt="Scaling Analysis" width="55%"/>
  <br/>
  <em>Performance scaling from Qwen3 0.6B to 32B across seven tasks.</em>
</p>

**Temporal Window Analysis**: DeepSeek R1 maintains accuracy at 62.1% even at 18-month history (vs. V3's decline to 58.46%), demonstrating superior long-horizon preference modeling.

**Bridge Module Ablation**: Bridge modules yield consistent gains across all tasks:
- User Intent Mining: +8.92% (80.25 → 89.17)
- User Behavior Inference: +7.63%  
- Personalized Recommendation: **+8.33%** (55.98 → 64.31)

### RQ5: Strategic Shortcuts & Cascading Failure Mechanisms

<p align="center">
  <img src="KDD_benchmark 2/pic/thinking_process_distribution_new.png" alt="Thinking Process Distribution" width="45%"/>
  <img src="KDD_benchmark 2/pic/human_evaluation.png" alt="Human Evaluation Error Types" width="45%"/>
  <br/>
  <em>Left: Count distribution of sub-tasks during the thinking process. Right: Statistical distribution of error types via manual annotation.</em>
</p>

A critical **"strategic shortcut"** pattern emerges: LLMs systematically bypass deep User Profile Construction in favor of shallow need-product pattern matching. This leads to:

- **Independent failures** in User Profiling: Isolated summarization errors (User Behavior Summary: 28.51%)
- **Cascading collapses** in Personalized Recommendation: Misinterpretations amplify through Comparative Reasoning (35.65%) → User Behavior Summary (34.11%) → Uncertainty Management collapse (21.71%)

> 🔑 **Conclusion**: Reasoning failures in e-commerce are not accidental — they are the inevitable consequence of strategic shortcuts that bypass structural persona abstraction.

---

## 📁 Dataset & Code

> 🚀 **Dataset and code will be released soon!**

The release will include:
- 📦 Full ShopPerBench evaluation dataset (7,965 instances across 7 tasks)
- 🏗️ BiWalker-Shop pipeline implementation
- 📏 Evaluation scripts and leaderboard
- 🤖 Baseline model inference scripts

---

## 🗂️ Repository Structure

```
ShopPerBench/
├── data/                      # Benchmark dataset (7 task files)
│   ├── product_attribute.json
│   ├── accessory_substitute.json
│   ├── user_profiling.json
│   ├── user_interest_prediction.json
│   ├── user_intent_mining.json
│   ├── user_behavior_inference.json
│   └── personalized_recommendation.json
├── pipeline/                  # BiWalker-Shop synthesis pipeline
│   ├── ia_knowledge_graph/    # IA-KG construction
│   ├── ubp_decision_tree/     # UBP-DT construction
│   ├── coupling_walk/         # Dual-domain coupling & path walking
│   └── quality_control/       # Multi-stage QC scripts
├── evaluation/                # Evaluation scripts
│   ├── eval.py
│   └── metrics.py
├── baselines/                 # Baseline inference scripts
└── README.md
```

---

## 📝 Citation

If you find ShopPerBench useful in your research, please cite our paper:

```bibtex
@inproceedings{zhao2025shopperbench,
  title     = {ShopPerBench: Personalization Meets Explainable Reasoning in E-commerce},
  author    = {Zhao, Lulu and Wang, Zhengyang and Zhang, Xingyao and Chen, Xuefeng and Su, Wenbo},
  booktitle = {Proceedings of the ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (KDD)},
  year      = {2025},
  publisher = {ACM}
}
```

---

## 👥 Authors

- **Lulu Zhao** — Alibaba Group (zll476809@taobao.com)
- **Zhengyang Wang** — Alibaba Group
- **Xingyao Zhang** — Alibaba Group
- **Xuefeng Chen** — Alibaba Group
- **Wenbo Su** — Alibaba Group

---

## 📜 License

This dataset is released for research purposes. All user data has been fully anonymized through multi-stage de-identification. No personally identifiable information (PII) is included.

---

<p align="center">
  <em>ShopPerBench — Driving LLMs from merely "answering questions" to becoming shopping decision-makers that understand users, plan effectively, and explain their reasoning.</em>
</p>
