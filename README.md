# PolicyGuard AI — Travel Reimbursement Approval Agent

## Overview

PolicyGuard AI is a lightweight GenAI and Agentic AI prototype that evaluates employee travel reimbursement claims against a defined reimbursement policy.

The solution combines a locally hosted LLM, Python-based tools, and deterministic business rules to recommend whether a claim should be approved, partially approved, rejected, or sent for manual review.

This project was developed as part of the HCL AI Developer Candidate Assignment.

## Key Features

- **LLM-powered agent:** Uses Llama 3.2 through Ollama for claim interpretation and tool orchestration.
- **Policy lookup:** Retrieves applicable policy rules using stable policy identifiers such as `POL-CAT-01` and `POL-PD-02`.
- **Receipt validation:** Checks whether the required receipts are available.
- **Expense limit validation:** Applies meal, lodging, and ground transportation limits.
- **Approval threshold checks:** Identifies claims that exceed the agent's approval authority.
- **Deterministic decision engine:** Uses Python to calculate reimbursement amounts, deductions, and final decisions.
- **Structured output:** Uses Pydantic to validate claim data and the final result structure.
- **Results dashboard:** Displays claim decisions and approved versus deducted amounts.
- **Local execution:** Uses Ollama, so an external LLM API key is not required.

## Technology Stack

- Python 3.11+
- Ollama
- Llama 3.2
- Pydantic
- Pandas
- Matplotlib
- Jupyter Notebook

## Architecture

```text
Sample Claims (JSON)
        |
        v
Input Validation (Pydantic)
        |
        v
Llama 3.2 Agent
        |
        v
Policy and Validation Tools
        |
        v
Deterministic Decision Engine
        |
        v
Output Validation
        |
        v
Structured JSON Results
        |
        v
Results Dashboard
```

The LLM assists with reasoning and tool selection. Python remains responsible for applying policy rules, calculating reimbursement amounts, and determining the final decision.

## Decisions Supported

The agent produces one of four decisions:

| Decision | Meaning |
|---|---|
| `APPROVE` | The claim meets the applicable reimbursement rules. |
| `PARTIAL_APPROVE` | The claim is eligible, but some amount must be deducted under policy limits. |
| `REJECT` | The claim contains no reimbursable expenses. |
| `MANUAL_REVIEW` | A human reviewer must resolve missing information, exceptions, or other policy concerns. |

## Sample Claim Scenarios

The notebook evaluates all five claims provided in the assignment.

| Claim ID | Expected Decision | Reason |
|---|---|---|
| CLM-001 | APPROVE | Eligible expenses with required receipts and amounts within policy limits. |
| CLM-002 | REJECT | Spa and minibar expenses are ineligible. |
| CLM-003 | PARTIAL_APPROVE | Lodging exceeds the permitted nightly limit. |
| CLM-004 | MANUAL_REVIEW | Business-class airfare, a missing hotel receipt, and a claim exceeding $2,000. |
| CLM-005 | MANUAL_REVIEW | Missing receipt for a client dinner expense. |

The notebook contains the complete structured results, validation checks, tool-call audit information, and dashboard.

## Setup and Installation

### 1. Install Python dependencies

Python 3.11 or later is recommended.

```bash
pip install ollama pydantic pandas matplotlib jupyter
```

### 2. Install Ollama

Install Ollama from its official website:

https://ollama.com/

Make sure the Ollama service is running.

### 3. Download the model

Open a terminal and run:

```bash
ollama pull llama3.2
```

Verify that the model is available:

```bash
ollama list
```

### 4. Run the notebook

1. Clone or download this repository.
2. Open the Jupyter notebook.
3. Ensure the required Python packages are installed in the active environment.
4. Confirm that Ollama is running and `llama3.2` is available.
5. Select **Kernel → Restart Kernel and Run All Cells**.
6. Review the evaluation results, audit trail, final JSON output, and dashboard.

No external LLM API key or environment variable is required.

## Design Decisions

### Why use a deterministic decision engine?

Financial decisions should be predictable and auditable. The LLM can help interpret claims and select relevant tools, but Python applies the authoritative reimbursement rules and performs the final calculations.

### Why route some claims to manual review?

The system avoids forcing a decision when important information is missing or an exception requires human judgment. Examples include missing receipts, business-class airfare, and claims exceeding the approval threshold.

### Why use a local LLM?

Ollama enables the prototype to run locally without depending on a paid external LLM API.

### Why keep the implementation lightweight?

The assignment focuses on demonstrating practical GenAI, tool usage, policy enforcement, and reliable structured outputs rather than building a full enterprise reimbursement platform.

## Limitations and Future Improvements

- Add support for uploading claims through a web interface or API.
- Integrate with a receipt storage system for attachment verification.
- Add more extensive automated tests for unusual and conflicting claims.
- Improve policy retrieval for larger, unstructured policy collections.
- Add persistent audit logging and reviewer feedback.
- Introduce calibrated confidence scoring and monitoring for production use.

## Project Scope

This is a demonstration prototype built using the mock policy and five sample claims supplied with the HCL assignment. It is not intended to replace an organization's official reimbursement approval process.

## Deliverable

The Jupyter notebook is the primary project deliverable. It contains the implementation, setup instructions, sample outputs, evaluation checks, dashboard, and design notes.
