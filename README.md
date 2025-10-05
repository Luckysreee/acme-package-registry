# ACME Package Registry

A **command-line tool** that evaluates and scores **Hugging Face models** for ACME Corporation using multiple trustworthiness metrics.
It automates evaluation, generates composite trust scores, and outputs results in **NDJSON** format.

---

## Team Members

* **Nathan Allie**
* **Roshen Cherian**
* **Lekhya Sree Akella**
* **Raja Almdar Tariq Ali**

---

## Table of Contents

* [Overview](#overview)
* [Architecture](#architecture)
* [Installation](#installation)
* [Usage](#usage)
* [Configuration](#configuration)
* [Metrics System](#metrics-system)
* [Testing](#testing)
* [Development](#development)
* [Troubleshooting](#troubleshooting)
* [API Reference](#api-reference)

---

## Overview

The **ACME Package Registry CLI** provides a standardized and automated approach to evaluate models, codebases, and datasets hosted on the Hugging Face platform.
It computes multiple quantitative metrics such as **license validity**, **code quality**, **ramp-up time**, **bus factor**, and others to produce an overall **net trust score**.

### Key Features

* CLI with `install`, `test`, and `process` commands
* Parallelized metric evaluation
* NDJSON output for structured automation
* Built-in logging and configurable verbosity
* Modular design for metric addition or modification

---

## Architecture

### Project Structure

```
acme-package-registry/
├── run                        # Typer CLI entrypoint
├── requirements.txt            # Dependencies list
├── example_urls.txt            # Sample input file
├── src/
│   ├── orchestrator.py         # Main parallel execution logic
│   ├── hf_api.py               # Hugging Face API integration
│   ├── genai_client.py         # Optional LLM scoring (GenAI)
│   ├── log_utils.py            # Logging configuration
│   ├── net_score.py            # Computes composite trust score
│   ├── models.py               # Defines NDJSON schema (Pydantic)
│   ├── metrics/                # Metric implementations
│   │   ├── bus_factor.py
│   │   ├── code_quality.py
│   │   ├── dataset_and_code_score.py
│   │   ├── dataset_quality.py
│   │   ├── license_metric.py
│   │   ├── performance_claims.py
│   │   ├── ramp_up_time.py
│   │   └── size_score.py
│   ├── tests/                  # Pytest test suite
│   │   ├── test_metrics.py
│   │   ├── test_orchestrator.py
│   │   └── ...
│   └── helpers/
│   │   ├── utils.py
│   │   └── __init__.py
└── README.md

```

### Data Flow

1. The user provides a text file with one or more Hugging Face URLs.
2. The **orchestrator** runs each metric in parallel and logs execution times.
3. Each metric produces an independent score `[0,1]`.
4. The **net_score** module aggregates these into a weighted final trust score.
5. Results are written to standard output as **NDJSON** entries.

---

## Installation

### Prerequisites

* Python **3.11+**
* pip (latest stable)
* Internet connection (for Hugging Face API)

### Quick Setup

```bash
# Clone and enter the repository
git clone https://github.com/Enbeeay/acme-package-registry.git
cd acme-package-registry

# Install dependencies
./run install

# Verify installation
./run test
```

### Manual Setup

```bash
python3.11 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

---

## Usage

### Commands

| Command                    | Description                                            |
| -------------------------- | ------------------------------------------------------ |
| `./run install`            | Installs all dependencies listed in `requirements.txt` |
| `./run test`               | Runs automated tests with coverage tracking            |
| `./run process <url_file>` | Evaluates all URLs from a text file and outputs NDJSON |

### Example

```bash
./run process example_urls.txt
```

#### Sample Output

```json
{"name":"bert-base-uncased","category":"MODEL","net_score":0.8042,"ramp_up_time":0.7594,"bus_factor":0.4485,"performance_claims":1.0,"license":1.0,"dataset_quality":1.0,"code_quality":0.5}
```

---

## Configuration

The system supports runtime configuration via **environment variables**:

| Variable                                         | Description                                 | Default                                              |
| ------------------------------------------------ | ------------------------------------------- | ---------------------------------------------------- |
| `GENAI_API_URL`                                  | Purdue GenAI endpoint for LLM scoring       | `https://genai.rcac.purdue.edu/api/chat/completions` |
| `GEN_AI_STUDIO_API_KEY` / `GENAI_STUDIO_API_KEY` | API key for GenAI integration               | None                                                 |
| `LOG_FILE`                                       | File path for logs                          | `acme.log`                                           |
| `LOG_LEVEL`                                      | Verbosity level (0=silent, 1=info, 2=debug) | `0`                                                  |

The logger (`log_utils.py`) validates directory paths and raises an error if invalid, ensuring consistent automated grading behavior.

---

## Metrics System

Each metric returns a score between 0 and 1 and executes in parallel for efficiency.

| Metric                     | Description                                                    | Data Source                         |
| -------------------------- | -------------------------------------------------------------- | ----------------------------------- |
| **license**                | Validates license information from the Hugging Face model card | `hf_api.py`                         |
| **ramp_up_time**           | Evaluates documentation completeness                           | `metrics/ramp_up_time.py`           |
| **bus_factor**             | Measures number and diversity of contributors                  | `metrics/bus_factor.py`             |
| **performance_claims**     | Checks for explicit benchmark results                          | `metrics/performance_claims.py`     |
| **dataset_and_code_score** | Evaluates linked dataset/code availability                     | `metrics/dataset_and_code_score.py` |
| **dataset_quality**        | Measures dataset maintenance and quality                       | `metrics/dataset_quality.py`        |
| **code_quality**           | Analyzes readability, style, and structure                     | `metrics/code_quality.py`           |
| **size_score**             | Estimates deployability across devices                         | `metrics/size_score.py`             |

The final `net_score.py` script aggregates these metrics into a composite trust score.

---

## Testing

Tests are implemented with **pytest** and **pytest-cov**, located under `src/tests/`.

### Running Tests

```bash
./run test
```

The suite includes:

* Unit tests for individual metrics
* Integration tests for orchestrator behavior
* CLI-level tests for end-to-end validation

Target coverage: **≥80% line coverage** across all modules.

---

## Development

### Tooling

| Tool                    | Purpose              |
| ----------------------- | -------------------- |
| **black**               | Code formatting      |
| **isort**               | Import sorting       |
| **flake8**              | Linting              |
| **mypy**                | Type checking        |
| **pytest / pytest-cov** | Testing and coverage |

### Branching & Versioning

* **main** – Stable branch
* **feature/** branches – For feature work or bug fixes
* Tags like `v1.0`, `v1.1` can mark deliverables or releases
* GitHub Actions CI planned for automated lint/test enforcement

---

## Troubleshooting

| Issue                        | Cause                         | Resolution                                    |
| ---------------------------- | ----------------------------- | --------------------------------------------- |
| `Invalid log file directory` | `LOG_FILE` path doesn’t exist | Provide a valid directory path                |
| `requests` not found         | Missing dependency            | Run `./run install`                           |
| API scoring skipped          | No GenAI API key              | Export `GENAI_STUDIO_API_KEY`                 |
| CLI error “No such command”  | Missing subcommand            | Use `./run process <file>` not `./run <file>` |

---

## API Reference

### `setup_logging()`

Configures log file and verbosity.
Raises `SystemExit` if the path is invalid.

### `score_with_llm(task, readme, context, model=None)`

Calls Purdue GenAI API (if available) to produce 0–1 score.
Returns `None` when unavailable.

### `orchestrator.run_parallel_metrics()`

Handles concurrent metric execution and aggregation.

---

## License

This project was created for **ECE 46100 / CSCI 45000 – Software Engineering** (Fall 2025).
All dependencies used are open-source and listed in `requirements.txt`.


