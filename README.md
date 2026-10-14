# MSEC: Minimum Sufficient Context Certification

**Risk-controlled token budget certification for LLMs** — not a compressor, a certification layer.

## Overview

MSEC (Minimum Sufficient Context) frames context budget selection as a **finite-sample risk control problem**. Instead of picking a compression ratio by average benchmark accuracy, users pre-declare:

- **τ (tau)**: absolute error ceiling (e.g., 3%)
- **Δ (delta)**: maximum regression vs. full context (e.g., 2%)
- **α (alpha)**: family-wise confidence (e.g., 0.05)

The **certified MSEC** is the smallest token budget on a pre-declared grid that passes both tests with fixed-sequence testing controlling FWER at α.

## Key Results (Pilot: n=500, Nemotron-3-Super)

| Metric | Value |
|--------|-------|
| Certified MSEC (exact lookup) | 128 words = 247 Nemotron tokens (21.8× reduction) |
| Full-context error (2-hop) | 49.2% |
| query_select@2048 vs full | **Certifiably better** (u_Δ = −0.026) |
| Failure mechanism | 100% confident distractor capture, 0% abstention |
| Naive budget-picking | **Violates τ on held-out data** (6.1% upper bound) |
| Certified MSEC | **Holds τ** (2.4% upper bound) |

## Installation

```bash
pip install -r requirements.txt
```

## Quick Start (requires API keys)

```bash
# NoLiMa audit (NVIDIA NIM)
python eval_harness.py --task nolima --model nemotron-3-super-120b-a12b \
  --provider nvidia --n 500 --seed 20261013 --rpm 36 --keysfile keys.txt

# RULER (Together.ai)
python eval_harness.py --task ruler --model llama-3.1-70b-instruct \
  --provider together --n 300 --seed 20261014 --together-key $TOGETHER_KEY
```

## Tasks Supported

| Task | Failure Mode Tested |
|------|---------------------|
| NoLiMa | Non-literal retrieval (lexical disjoint Q↔E) |
| RULER | Context length scaling, multi-hop |
| LongBench | Bilingual, multi-task |
| AgentRAG | Closed-loop, tool use, execution instability |

## Methods

- `query_select` — lexical overlap retrieval
- `truncate_head` / `truncate_tail` — naive truncation
- `random_select` — random subset
- `llmlingua2` — learned (opt-in, sidecar)

## Citation

```bibtex
@article{tomar2026msec,
  title={Certifying Minimum Sufficient Context: Risk-Controlled Token Budgets for Language Models},
  author={Tomar, Kshitij Pal Singh},
  year={2026}
}
```

## License

MIT — see LICENSE file.