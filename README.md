# ACME Package Registry

A command-line tool to evaluate and score Hugging Face models, datasets, and repositories for reuse within the ACME Corporation.  
Developed as part of **ECE 46100 / CSCI 45000 – Software Engineering (Fall 2025)**.

---

## Collaborators
- Nathan Allie  
- Roshen Cherian  
- Lekhya Sree Akella  
- Raja Almdar Tariq Ali  

---

## Overview

The ACME Package Registry CLI provides a lightweight framework for evaluating the trustworthiness and reusability of machine learning models.  
It uses metadata from the Hugging Face Hub and Git repositories to compute a *net score* representing documentation quality, code maintainability, and overall project reliability.

The tool outputs all results in **NDJSON** format, allowing for automated integration into other pipelines or dashboards.

---

## Features

- Command-line interface with `install`, `test`, and `process` commands  
- Modular metric system for code, dataset, and documentation evaluation  
- Parallel metric execution for efficiency  
- Schema validation and serialization using Pydantic  
- NDJSON output with reproducible structure  
- Logging system with configurable path and verbosity  
- Automated testing with coverage enforcement  

---

## Project Structure
```

acme-package-registry/
├── src/
│   ├── orchestrator.py
│   ├── models.py
│   ├── hf_api.py
│   ├── LLM_endpoint.py
│   ├── logging_config.py
│   └── metrics/
│       ├── bus_factor.py
│       ├── code_quality.py
│       ├── dataset_code_avail.py
│       ├── dataset_quality.py
│       ├── license.py
│       ├── perf_claims.py
│       ├── ramp_up.py
│       └── size.py
├── tests/
├── run
├── requirements.txt
└── README.md

```
---

## Metrics

Each metric reflects an important aspect of model trustworthiness or usability:

Ramp-Up Time – Measures how quickly an engineer can understand and use the repository.  
Bus Factor – Analyzes the distribution of Git contributors to assess risk.  
License – Checks for compatibility with ACME’s open-source policies.  
Code Quality – Evaluates readability and documentation, optionally assisted by GenAI.  
Performance Claims – Checks for benchmarking or evaluation statements in the README.  
Dataset and Code Availability – Detects presence of linked datasets and scripts.  
Dataset Quality – Evaluates completeness and clarity of dataset metadata.  
Size Score – Compares model size across various target hardware types.  

---

## Installation

Requirements:  
• Python 3.11 or higher  
• Git installed and configured  

Steps:  
1. Clone the repository:  
   `git clone https://github.com/Enbeeay/acme-package-registry.git`  
   `cd acme-package-registry`

2. (Optional) Create and activate a virtual environment:  
   `python3.11 -m venv .venv`  
   `source .venv/bin/activate`

3. Install dependencies:  
   `./run install`

---

## Usage

To evaluate models from a list of URLs:  
`./run process example_urls.txt`

Example Output:
```

{
"name": "bert-base-uncased",
"category": "MODEL",
"net_score": 0.8042,
"bus_factor": 0.4485,
"license": 1.0,
"code_quality": 0.5,
"dataset_and_code_score": 1.0,
"dataset_quality": 1.0
}

```

Other commands:  
• `./run install` – Install dependencies  
• `./run test` – Run all tests with coverage  
• `./run --help` – Show available commands  

---

## Configuration

The software is configured primarily through environment variables, which can be changed at runtime without modifying code.

GENAI_API_URL – Endpoint for Purdue GenAI Studio (default: https://genai.rcac.purdue.edu/api/chat/completions)  
GEN_AI_STUDIO_API_KEY / GENAI_STUDIO_API_KEY – API key for GenAI access  
LOG_FILE – Log file path (default: acme.log)  
LOG_LEVEL – Logging verbosity (0 = Critical, 1 = Info, 2 = Debug)  

Example:  
```

export LOG_FILE="./logs/acme_debug.log"
export LOG_LEVEL=2
./run process example_urls.txt

```

---

## Building

The CLI does not require compilation.  
To set up the environment and build a runnable installation:

1. Create a Python virtual environment  
2. Run `./run install` to install dependencies  

The `./run` script manages dependency installation, testing, and processing, ensuring reproducibility across systems.

---

## Testing

Testing is automated using pytest with coverage measurement.  
To execute all tests, run:
```

./run test

```

Test types include:  
• Unit tests for individual metric modules  
• Integration tests for orchestrator and NDJSON output  
• Manual validation for invalid URLs and logging behavior  

All tests target ≥80% line coverage as per the specification.

---

## Logging

Logging is handled by `src/logging_config.py`.  
It supports the following environment variables:

LOG_FILE – Path to write logs  
LOG_LEVEL – Numeric verbosity level  

A critical level (0) creates an empty log file, while levels 1 and 2 add informational and debug details respectively.  
Invalid file paths terminate execution immediately, ensuring controlled error handling.

---

## Version Control

The project uses Git and is hosted publicly at:  
https://github.com/Enbeeay/acme-package-registry  

Branching follows a feature-branch workflow:  
• main is stable and protected.  
• Each feature or bug fix occurs on its own branch (feature/<name>).  
• Pull Requests are reviewed before merging.  
• Version tags (v1.0, v1.1, etc.) mark release milestones.  

---

## Dependencies

Typer [all] – Command-line interface and argument parsing.  
Pydantic – Schema definition and output validation.  
huggingface_hub – Fetches model and dataset metadata.  
GitPython – Extracts repository and contributor data.  
pytest, pytest-cov – Automated testing and coverage tracking.  
black, isort, flake8, mypy – Code formatting, linting, and type checking.  
validators – Validates URL structure and prevents malformed inputs.  

All dependencies are installed via:  
`./run install`

---

## Acknowledgment

Developed as part of the coursework for  
ECE 46100 / CSCI 45000 – Software Engineering  
Purdue University, Fall 2025.

