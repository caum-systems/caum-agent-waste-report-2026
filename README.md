# AI Agent Waste Report 2026
### What 2,615,295 real steps reveal about why AI agents fail

> **Key finding:** Failed AI agent sessions waste 3.4× more tokens in loops than successful ones (13.83% vs 4.04%). 98.7% of that waste is pure LOOP — circular repetition, not slow progress.

This repository contains the methodology, aggregated findings, and analysis scripts for the CAUM State of AI Agent Waste Report 2026.

---

## Quick Summary

| Metric | Value |
|--------|-------|
| Sessions analyzed | **99,167** |
| Total steps | **2,615,295** |
| Resolved session waste | 4.04% |
| Unresolved session waste | **13.83%** |
| Waste ratio (fail/success) | **3.4×** |
| Loop % of all waste | **98.7%** |
| Cohen's d (effect size) | **+0.548** (GOOD) |

---

## Why This Matters

Every company building AI agents tracks pass rate — did the agent complete the task?

Nobody tracks what happens *during* sessions that fail.

We found that failing sessions don't drift slowly into failure. They **loop** — the agent executes the same actions in a cycle, gets the same results, executes them again. This pattern appears, on average, at step 7.4 of a ~28-step session. 74% of the session remains when the first loop is detected.

That means intervention is possible. Before the session fails. Before the tokens are wasted.

---

## Datasets Used

| Source | Sessions | Type |
|--------|----------|------|
| [nebius/SWE-agent-trajectories](https://huggingface.co/datasets/nebius/SWE-agent-trajectories) | 79,773 | SWE-bench (code tasks) |
| Claude SWE-bench trajectories | 8,588 | SWE-bench (code tasks) |
| GPT-4o SWE-bench trajectories | 5,816 | SWE-bench (code tasks) |
| Llama balanced dataset | 4,990 | SWE-bench (code tasks) |

All sessions are real agent trajectories from production model evaluations.

---

## Per-Model Results (SWE-bench)

| Model | Framework | Cohen's d | AUC | Verdict |
|-------|-----------|-----------|-----|---------|
| GPT-4o | SWE-agent | **+1.099** | 0.757 | Excellent |
| GPT-4o | OpenHands | **+1.131** | 0.852 | Excellent |
| Llama | SWE-agent | **+0.968** | 0.747 | Excellent |
| Claude-3.7 | SWE-agent | **+0.775** | 0.650 | Good |
| Claude-3.5 | SWE-agent | +0.175 | 0.554 | Weak |

**Claude 3.5 note:** Claude 3.5 shows very weak structural signal (d=+0.175). The model appears to maintain a consistent behavioral pattern regardless of whether it's converging on a solution. We ran 5 independent experiments to confirm this is a model property, not a measurement artifact.

---

## Enterprise Impact (Toolathlon — Real-World Tasks)

We also analyzed 6,818 sessions from the [Toolathlon-Trajectories](https://huggingface.co/datasets/hkust-nlp/Toolathlon-Trajectories) dataset (WooCommerce, rail booking, travel planning — enterprise-realistic tasks). 22 models evaluated.

| Model | Cohen's d | Pass Rate |
|-------|-----------|-----------|
| Gemini 2.5 Flash | +0.527 | 4.8% |
| Gemini 2.5 Pro | +0.394 | 11% |
| GPT-5 High | +0.320 | 32% |
| o4-mini | +0.313 | 16% |
| Claude 4.5 Sonnet | -0.142 | 39% |

Key observation: Signal is weaker on web/service tasks vs code tasks. Maximum d = +0.527 vs +1.131 on SWE-bench. Domain mismatch is real.

---

## The Cost of Loop Waste

Using 800 tokens/step (conservative) and $10/M tokens (blended):

| Scale | Annual waste from loops |
|-------|------------------------|
| 100 sessions/day | ~$950/year |
| 1,000 sessions/day | ~$9,500/year |
| **10,000 sessions/day** | **~$95,000/year** |
| 100,000 sessions/day | ~$950,000/year |

---

## Detection Method

CAUM (Cognitive Autonomous Unit Monitor) classifies each agent step using:

1. **LZ-complexity** of the tool-name sequence — behavioral diversity score
2. **Cosine similarity** of consecutive step embeddings (all-MiniLM-L6-v2) — repetition detection
3. **Swarm-based novelty score** — information gain per step
4. **Cryptographic Merkle commitment** — every classification is Ed25519 signed

**Per-step regimes:**
- `EXPLORER` — varied approaches, healthy expansion
- `GRIND` — repetitive but marginally productive
- `STAGNATION` — no meaningful progress
- `LOOP` — circular repetition (98.7% of waste)

**Performance:** 13ms/session on warm embedding cache. Zero model calls. Works with any framework.

**Privacy:** Zero semantic access. CAUM never reads prompts, source code, or agent reasoning. Only tool names and content embeddings are processed.

---

## Patent Status

The CAUM motor algorithm is patent pending:
- **CIP-1**: 19/533,228 (filed 2026-02-08) — Geometric trajectory diagnostics
- **CIP-2**: 19/553,646 (filed 2026-03-02) — Semantic+Swarm+Vault

---

## Free Report

Want to see this analysis on your own agent data?

**→ [caum.systems/upload](https://caum.systems/upload)**

Upload a JSONL trajectory file → get a forensic PDF with per-step regime classification, loop timeline, and a cryptographically signed certificate.

No code changes. No API key required. Free.

---

## Citation

If you use these findings in your research:

```
@techreport{caum2026waste,
  title     = {State of AI Agent Waste 2026: Loop Detection in 99,167 Real Sessions},
  author    = {CAUM Systems},
  year      = {2026},
  url       = {https://caum.systems},
  note      = {2,615,295 steps analyzed across GPT-4o, Claude, Llama, Gemini and 20+ models}
}
```

---

## Contact

**caum.systems** | contact@caum.systems

*Live demo: [caum.systems/demo](https://caum.systems/demo/intro/) (2 min, no signup)*

---

**HOW TO USE THIS README:**

1. Create a public GitHub repo named: `ai-agent-waste-report-2026`
2. Paste this README.md as the main README
3. Add the full_scale_results.json as a data file
4. Star it from your main account + share the repo URL
5. This README will rank on Google for:
   - "AI agent waste 2026"
   - "AI agent loop detection"
   - "SWE-bench agent analysis"
   - "CAUM agent monitor"
   - "AI agent token waste report"
