# SybilScope

**An open-source Ethereum analytics framework that uses graph structure, behavioral patterns, temporal relationships, and explainable evidence to identify and investigate potentially coordinated wallet clusters.**

> **Status:** Research and early development. The first milestone is a minimal, understandable baseline, not a complete detector.

---

## Overview

Systems that give one reward, vote, or allocation per participant (airdrops, governance, quests, incentive programs) are vulnerable to **Sybil behavior**: one actor operating many wallets that appear independent.

A single Sybil wallet often looks normal in isolation. The stronger evidence appears when wallets are examined **together**. SybilScope therefore models Ethereum as a **relationship graph** and asks:

> *Do these wallets interact with the blockchain in a way that provides credible evidence they may be controlled by the same actor?*

## What SybilScope Is (and Is Not)

SybilScope reports **evidence of coordinated on-chain behavior**. It does **not**:

- prove who owns a wallet or deanonymize anyone
- accuse users of fraud
- treat similarity as ownership (**similarity ≠ Sybil**)
- rely on a single rule such as "same funder = Sybil"

Results use calibrated language: *strong / moderate / weak / insufficient evidence*, and *potential Sybil cluster*, never "these wallets definitely belong to the same person."

## Core Hypothesis

> Wallets controlled by the same actor may show measurable similarities in on-chain structure and behavior that are unlikely to occur independently at the same frequency.

No single signal is sufficient. Independent signals should reinforce or weaken one another.

## Detection Signals

| Signal | Examples |
|---|---|
| **Funding** | Shared upstream funder, funding timing, intermediary wallets, star/chain/tree patterns |
| **Temporal** | First-transaction time, intervals, bursts, synchronized activity, funding-to-first-action delay |
| **Sequence** | Ordered action patterns (e.g. Fund → Approve → Swap → Transfer → Claim) |
| **Contract interaction** | Shared contracts, call order, protocol combinations |
| **Value** | Repeated or round amounts (supporting evidence only) |
| **Gas** | Gas price/usage strategies (supporting evidence only) |
| **Graph structure** | Star, chain, tree, and dense-cluster topologies |

## Approach

```text
Blockchain Data → Ingestion → Preprocessing
                                  │
                     ┌────────────┴────────────┐
                     ▼                         ▼
             Behavioral Features        Graph Construction
                     └────────────┬────────────┘
                                  ▼
                    Clustering / Linkage Analysis
                                  ▼
                       Evidence Fusion / Scoring
                                  ▼
                 Explainable Investigation + Visualization
```

Cluster scores are meant to be **decomposable** so every verdict can be inspected:

```text
Cluster C17, overall coordination score: 0.87   (illustrative only)
  - Common funding:       0.94
  - Temporal similarity:  0.89
  - Sequence similarity:  0.91
  - Contract similarity:  0.82
  - Gas similarity:       0.67
  - Graph linkage:        0.93
```

The actual scoring method must be researched, calibrated, and benchmarked.

## Explainability

Each flagged cluster should come with human-readable reasons, for example:

```text
Wallets A, B, C, D were grouped because:
1. All were funded by the same upstream address.
2. Funding occurred within a short time window.
3. Their first five protocol interactions followed a highly similar sequence.
4. They interacted with the same contracts in similar order.
Confidence: High
```

An optional LLM layer may summarize and explain evidence, but it sits **on top of** measurable evidence and is never treated as ground truth.

## Limitations to Study

- **False positives:** unrelated wallets may share an exchange, a popular protocol, an airdrop, or timing.
- **False negatives:** sophisticated operators can use different funders, randomize timing, route funds through intermediaries, vary gas, split activity across chains, or split clusters.

## Evaluation Plan

- **Synthetic benchmarks** with known ground truth (e.g. 100 wallets: 20 genuine + 80 across 8 simulated Sybil clusters)
- **Publicly documented Sybil campaigns** as case studies
- **Labelled historical data**, where legitimate labels exist
- **Metrics:** precision, recall, F1, PR-AUC / ROC-AUC (address-level); cluster precision/recall/F1, adjusted Rand index, NMI (cluster-level)
- **Adversarial scenarios:** naive Sybil → timing randomization → indirect funding → gas randomization → cluster splitting → cross-chain → burner wallets → delayed coordination

No performance claims will be made without a defensible evaluation.

## Tech Stack (initial, subject to change)

- **Language:** Python
- **Data:** pandas, NumPy
- **Graph:** NetworkX (igraph / graph-tool if scale requires)
- **ML / clustering:** scikit-learn, Louvain/Leiden, DBSCAN/HDBSCAN, hierarchical methods
- **Visualization:** Plotly, PyVis
- **Data sources:** Ethereum RPC and Etherscan-compatible APIs, behind a modular ingestion layer

## Roadmap

1. **Baseline:** ingest a small Ethereum dataset, build a wallet interaction graph, find simple clusters, explain the evidence *(first milestone)*
2. Behavioral and temporal features
3. Clustering method comparison
4. Evidence fusion and explainability
5. Benchmarking against simple heuristic baselines
6. Adversarial testing
7. Documentation and public release

## Repository Structure (proposed)

```text
sybilscope/
├── docs/            # architecture, methodology, research, references
├── data/            # raw, processed, synthetic
├── notebooks/       # exploration, experiments, benchmarks
├── src/sybilscope/  # ingestion, preprocessing, graph, features,
│                    # clustering, scoring, explainability, visualization
├── tests/
└── experiments/     # baselines, adversarial, results
```

## Design Principles

1. **Evidence over assumptions:** every detection is traceable
2. **Multiple signals over single heuristics**
3. **Reproducibility:** experiments can be rerun by others
4. **Explainability:** users can see why a cluster was flagged
5. **Adversarial thinking:** assume operators will try to evade detection
6. **No manufactured certainty:** weak evidence is reported as weak

## Ethics and Privacy

SybilScope uses only public blockchain data and reports behavioral linkage, not real-world identity.

## Contributing

Contributions, critiques, and benchmark ideas are welcome. Please open an issue to discuss significant changes.

## License

TBD
