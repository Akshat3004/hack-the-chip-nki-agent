# Hack the Chip 2026 — Autonomous NKI Kernel Agent

**NYU × Amazon Annapurna Labs · Hack the Chip · Team project**  
**Qwen3-8B · AWS Trainium2 · Python · Neuron Kernel Interface (NKI) · Autonomous code generation**

An engineering case study of a team-built agent that **generates accelerator kernels, checks their correctness, diagnoses failures, and iteratively repairs candidates**. The system uses Qwen3-8B with hardware-aware prompts, bounded NKI API legalization, and an independent simulator-based checker.

> **Portfolio showcase, not a code fork:** This repository is maintained under the GitHub account of **Akshat Agrawal** to make the project discoverable. The original implementation and commit history live in the [team repository's development branch](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent). The work is collaborative, not a solo implementation. This repository contains documentation and links, **not a copy of the team's source code**.

**[Explore the real code](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent)** · **[Read the technical case study](docs/CASE_STUDY.md)** · **[Results and qualifications](docs/RESULTS.md)** · **[Project attribution](CONTRIBUTORS.md)**

## The engineering problem

An LLM can produce Python that *looks* like a valid AWS NKI kernel yet fails because of incorrect instruction namespaces, memory-buffer placement, tensor shape constraints, hardware hazards, or numerically wrong results. The project tests a controlled feedback loop in which a checker, rather than the language model, decides whether a candidate is acceptable.

## Architecture

~~~mermaid
flowchart LR
    A["NKI task + official shapes"] --> P["Hardware-aware planner"]
    SDK["Installed NKI SDK"] --> P
    P --> Q["Qwen3-8B<br/>kernel candidates"]
    Q --> L["Conservative<br/>AST legalizer"]
    L --> C["Independent NKI<br/>simulator + checker"]
    C -->|"verified"| K["Locked candidate<br/>and SHA-256"]
    C -->|"failed"| D["Failure diagnosis<br/>and selection"]
    D -->|"repair feedback"| Q
~~~

The loop follows **plan → generate → legalize → check → diagnose → repair**, with an experiment budget described in the original project as **up to eight rounds and four candidates per round**.

| Component | Role | Original source |
| --- | --- | --- |
| Task planner | Derives compact, fact-based hints from workload shapes, SDK limits, and instruction signatures | [kernel_planner.py](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent/kernel_planner.py) |
| Agent loop | Generates candidates, records attempts, and manages repair rounds | [agent.py](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent/agent.py) |
| NKI legalizer | Applies narrow, general-purpose AST fixes to invalid NKI API usage | [primitive_legalizer.py](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent/primitive_legalizer.py) |
| Independent verifier | Simulates candidates and evaluates syntax, constraints, numerical correctness, and traffic budgets | [nkibench.py](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent/nkibench.py) |
| Failure selection | Ranks candidate outcomes and surfaces actionable repair signals | [failure_selection.py](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent/failure_selection.py) |
| Experiment runner | Freezes source and tracks controlled evaluation runs | [run_controlled.py](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent/run_controlled.py) |

## Results at a glance

The team's development-branch README reports **checker-accepted kernels for Levels 1–7**, with Level 8 still in progress in its published summary. These are **individual simulator-verification results**, not a measured *7/8 repeatable cold-start solve rate*.

| Level | Operation | Reported checker reward | Qualifier |
| --- | --- | ---: | --- |
| 1 | 2D average pooling | 1.00 | **Warm start**; four shapes passed |
| 2 | 2D transpose | 1.00 | Four shapes passed |
| 3 | Single-tile matrix multiplication | 1.00 | One shape passed |
| 4–7 | Tiled / blocked matrix multiplication variants | 1.00 each | Four shapes per level |
| 8 | Single-head attention | Not yet reported complete | No success claimed |

**No Trainium device-level kernel throughput or latency speedup is claimed.** The reported correctness checks use the NKI simulator. See [Results and qualifications](docs/RESULTS.md) for how to interpret the numbers.

## What this project demonstrates

- Working with **LLM-based code generation** under strict domain constraints, not just chat or text retrieval.
- Treating executable verification as the authority for correctness.
- Designing **failure-directed repair** based on numerical and hardware-API error feedback.
- Distinguishing **warm-start outcomes, individual successes, controlled ablations, and repeatable solve rates**.
- Engineering for reproducibility with candidate logs, source snapshots, and independent checking.

## Running the original project

The source, dependencies, experiment commands and required hardware environment are documented in the [team's implementation README](https://github.com/DipeshK47/trainium-agent-labs/tree/feat/nki-diagnostic-selection/projects/02-kernel-agent/README.md). In the original project directory, the documented basic checks are:

~~~bash
python -B nkibench.py --selftest
python -B -m pytest tests -q
~~~

A full agent run additionally requires the **AWS Neuron/NKI SDK**, compatible Trainium setup and the model-serving endpoint. This portfolio repository is **not executable by itself**; clone the original team project and select its feature branch to inspect or reproduce the implementation. I have not rerun the original simulator experiments in preparing this showcase.

## Credits and project provenance

Hack the Chip was a **team project**, built upon materials in the NYU × Amazon Annapurna Labs workshop repository. This page is a portfolio guide maintained by Akshat Agrawal; code and historical results are attributed to the team. See [CONTRIBUTORS.md](CONTRIBUTORS.md).

### Suggested résumé project line

> **Hack the Chip — Autonomous NKI Kernel Agent (team project):** Contributed to an LLM-driven kernel generation and repair pipeline using Qwen3-8B, hardware-aware prompts, AST-based NKI API legalization, and simulator-backed verification; the team reported checker-accepted results for seven challenge levels under documented configurations.
