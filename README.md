# CAUM — Structural Observation for AI Agents

**Detect loops, stagnation, and wasted compute in autonomous agent execution — without reading your prompts or payloads.**

[![Research](https://img.shields.io/badge/Zenodo-Paper%201-blue)](https://doi.org/10.5281/zenodo.18928199)
[![Research](https://img.shields.io/badge/Zenodo-Paper%202-blue)](https://doi.org/10.5281/zenodo.19019191)
[![Patent](https://img.shields.io/badge/USPTO-19%2F533%2C228-green)](https://www.caum.systems)
[![Patent](https://img.shields.io/badge/USPTO-19%2F553%2C646-green)](https://www.caum.systems)
[![Live](https://img.shields.io/badge/API-Railway-orange)](https://caum-observation-production.up.railway.app)

---

## What is CAUM?

CAUM is a **structural observation layer** for AI agents. It watches *how* an agent behaves — tool call diversity, trajectory geometry, regime transitions — without reading *what* the agent says.

**Core contract: CAUM does not decide. CAUM records.**

---

## Validated Results

Validated on **80,036 real agent sessions** from the [nebius/SWE-agent-trajectories](https://huggingface.co/datasets/nebius/SWE-agent-trajectories) dataset (Llama 8B, 70B, 405B):

| Metric | Value |
|--------|-------|
| AUC @ step 10 | **0.741** |
| AUC @ full session | **0.814** |
| Cohen's d | **+0.977** |
| P(failure \| LOOP regime) | **88.7%** |
| LOOP detection F1 | **0.742** (threshold UDS < 0.55) |
| Failed sessions vs successful | **2x longer** (31 vs 15 steps avg) |
| Compute savings @ 10K runs/day | **~$1.7M/year** |

Cross-model validation — no retraining required:

| Model | AUC @ step 10 | AUC @ full |
|-------|--------------|------------|
| Llama 8B | 0.730 | 0.816 |
| Llama 70B | 0.668 | 0.778 |
| Llama 405B | 0.584 | 0.776 |

---

## How It Works

```
Agent Steps → SBERT Embeddings → Trajectory Analysis → Regime Classification → UDS Score + Attestation
```

**Five structural signals:**
- `TCR` — Tool Coherence Ratio: are tools diverse or repetitive?
- `ESR` — Execution Substance Ratio: is each step meaningful?
- `SCI` — Structural Coherence Index: is the trajectory progressing?
- `ZT Similarity` — Zero-Trust cosine similarity between consecutive steps
- `Regime` — EXPLORER / GRIND / STAGNATION / LOOP

**Output:**
```python
{
  "uds": 0.73,           # Unified Dynamic Score [0-1]
  "tier": "T2",          # T1 (healthy) → T5 (critical)
  "dominant_regime": "EXPLORER",
  "shields_fired": 0,
  "advisory": {"level": 0, "level_name": "CLEAR"}
}
```

---

## Quick Start

```python
from caum_monitor_v10 import ZeroTrustAuditor

auditor = ZeroTrustAuditor()

# Push one step at a time as your agent executes
auditor.push("file_search", "find authentication.py in /src")
auditor.push("file_reader", "reading JWT validation at line 42")
auditor.push("code_editor", "fixed algorithm parameter in jwt.decode")
auditor.push("test_runner", "pytest — 12 passed in 1.2s")
auditor.push("submit",      "PR #142 submitted")

report = auditor.finalize()
print(f"UDS={report['uds']} tier={report['tier']} regime={report['dominant_regime']}")
```

**Or score a JSONL file directly:**
```python
from caum_monitor_v10 import score_jsonl
result = score_jsonl("path/to/session.jsonl")
```

**JSONL format:**
```jsonl
{"tool": "file_search", "content": "find auth.py"}
{"tool": "code_editor", "content": "fixed jwt.decode algorithm parameter"}
{"tool": "test_runner", "content": "12 passed 0 failed"}
```

---

## REST API

```bash
curl -X POST https://caum-observation-production.up.railway.app/v1/analyze \
  -H "Content-Type: application/json" \
  -d '{"trajectory": [{"tool": "search", "content": "..."}, ...]}'
```

Full API docs: [caum.systems/documentation](https://www.caum.systems/documentation/)

---

## Behavioral Regimes

| Regime | Resolve Rate | What it means |
|--------|-------------|---------------|
| EXPLORER | 67.8% | Healthy — agent is making progress |
| WARMING_UP | 56.6% | Early stage — insufficient data |
| GRIND | 36.9% | Warning — repetitive but moving |
| LOOP | **11.3%** | Critical — 88.7% probability of failure |

---

## Deployment Options

| Mode | Use Case |
|------|----------|
| **Forensic** | Batch analysis of historical sessions, compliance audits |
| **Live API** | Real-time monitoring with webhook alerts |
| **Enterprise SDK** | Embedded in your infrastructure, zero data transfer |

---

## Research & Patents

- **Paper 1:** [The Competence Illusion — 336 sessions, 88.1% detection rate](https://doi.org/10.5281/zenodo.18928199)
- **Paper 2:** [1,000-Session Real-World Validation — Cohen's d=0.977, AUC=0.758](https://doi.org/10.5281/zenodo.19019191)
- **Patent CIP-1:** USPTO 19/533,228 — Geometric trajectory diagnostics
- **Patent CIP-2:** USPTO 19/553,646 — Semantic + Swarm + Cryptographic Vault

---

## Try It

Upload a trajectory and get a free structural observation report:

**[caum.systems/upload](https://www.caum.systems/upload/)**

Enterprise pilot: [contact@caum.systems](mailto:contact@caum.systems)

---

*CAUM Systems LLC — Wyoming*  
*US Patent Applications: 19/533,228 | 19/553,646*
