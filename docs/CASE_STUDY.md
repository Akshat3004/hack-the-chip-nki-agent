# Engineering case study — autonomous NKI kernel generation

**Project:** Hack the Chip 2026, NYU × Amazon Annapurna Labs. This is a team project and this page is a portfolio summary rather than the original implementation.

**Original code:** https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent

## Challenge

Specialized accelerator kernels have correctness constraints that natural-language prompting alone does not enforce. Candidate NKI code may fail compilation, API legality, memory placement, numerical output checks, or traffic budgets.

## Approach

The team's pipeline uses a hardware-aware planner to derive short hints from official shapes and installed SDK information, Qwen3-8B to write candidate kernels, conservative AST-based legalization of narrow API errors, and an independent checker to validate outcomes.

A failed candidate receives diagnostic feedback, and the agent can generate or repair candidates across bounded rounds. Verification determines success; neither the legalizer nor model confidence guarantees correctness.

## Implementation map

| Area | Team implementation |
| --- | --- |
| Planning | [kernel_planner.py](https://github.com/DipeshK47/trainium-agent-labs/blob/feat/nki-diagnostic-selection/projects/02-kernel-agent/kernel_planner.py) |
| Agent control | [agent.py](https://github.com/DipeshK47/trainium-agent-labs/blob/feat/nki-diagnostic-selection/projects/02-kernel-agent/agent.py) |
| API legalization | [primitive_legalizer.py](https://github.com/DipeshK47/trainium-agent-labs/blob/feat/nki-diagnostic-selection/projects/02-kernel-agent/primitive_legalizer.py) |
| Simulator checker | [nkibench.py](https://github.com/DipeshK47/trainium-agent-labs/blob/feat/nki-diagnostic-selection/projects/02-kernel-agent/nkibench.py) |
| Controlled run logging | [run_controlled.py](https://github.com/DipeshK47/trainium-agent-labs/blob/feat/nki-diagnostic-selection/projects/02-kernel-agent/run_controlled.py) |

## What was measured

The team's later README reports checker-accepted kernels on Levels 1–7, with Level 1 explicitly warm-started and Level 8 not completed in the published summary. These are simulator checks, not hardware speed measurements; see [RESULTS.md](RESULTS.md). Original experiment notes also describe unsuccessful cold-start runs and controlled-ablation limitations.

## Takeaways

- An independent checker is essential for validating generated low-level code.
- Narrow repair rules are useful when each transformation is checked against original functional requirements.
- Individual passing candidates must be distinguished from repeated cold-start success probabilities.
- Hardware performance requires device benchmarks; simulator correctness does not establish speedup.

For detailed experiments, use the original team's README and commit history. This case study does not assert specific individual authorship of all listed modules.
