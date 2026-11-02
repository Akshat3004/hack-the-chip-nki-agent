# Reported results and measurement boundaries

These numbers are summarized from the public team project documentation, not independently reproduced for this portfolio page.

**Primary source:** [Project 2 README on the team's development branch](https://github.com/DipeshK47/trainium-agent-labs/blob/feat/nki-diagnostic-selection/projects/02-kernel-agent/README.md)

| Level | Task | Team-reported checker reward | Passing shapes | Qualification |
| --- | --- | ---: | ---: | --- |
| 1 | Average pooling 2D | 1.00 | 4/4 | Warm start from saved candidate |
| 2 | 2D transpose | 1.00 | 4/4 | Reported verified candidate |
| 3 | Single-tile matmul | 1.00 | 1/1 | Reported verified candidate |
| 4 | Tiled matmul | 1.00 | 4/4 | Reported verified candidate |
| 5 | Matmul with hoisted loads | 1.00 | 4/4 | Reported verified candidate |
| 6 | M/N-blocked matmul | 1.00 | 4/4 | Reported verified candidate |
| 7 | M/N/K-blocked matmul | 1.00 | 4/4 | Reported verified candidate |
| 8 | Single-head attention | Not reported complete | — | Work ongoing in team README |

The submission notes describe other trials, including five-repeat cold-start experiments that initially failed Levels 1–4 and later controlled runs. Consequently, **the table is not evidence of a 7/8 cold-start solve rate**.

The original submission also reports a small synthetic error/repair diagnostic comparison of **21/38 legacy versus 30/38 targeted checks**, as well as Qwen3-8B LoRA training over **41 examples for 82 steps** with held-out loss changing from **0.9951 to 0.1275**. Those results have limits: the synthetic set is small and skewed, and lower validation loss alone does **not** prove improvements on NKI benchmark correctness.

**No real Trainium kernel speedup or device-latency measurement is claimed here.** Checker outcomes were assessed via CPU simulation of NKI constraints and numerical correctness.

Source: [team Hackathon submission notes](https://github.com/DipeshK47/trainium-agent-labs/blob/feat/nki-diagnostic-selection/HACKATHON_SUBMISSION.md).
