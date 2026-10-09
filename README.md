# PolicyGuard AI — Travel Reimbursement Approval Agent

## Overview

PolicyGuard AI is a lightweight GenAI and Agentic AI prototype that evaluates employee travel reimbursement claims against a defined reimbursement policy.

The solution combines a locally hosted Large Language Model (LLM), Python-based tools, policy retrieval, and deterministic business rules to recommend whether a claim should be approved, partially approved, rejected, or sent for manual review.

This project was developed as part of the HCL AI Developer Candidate Assignment.

## Key Features

- **LLM-powered agent:** Uses Llama 3.2 through Ollama for claim interpretation and tool orchestration.
- **Policy retrieval (RAG):** Retrieves relevant reimbursement policy rules using ChromaDB and embeddings.
- **Policy references:** Associates decisions with stable policy identifiers such as `POL-CAT-01` and `POL-PD-02`.
- **Receipt validation:** Checks whether required receipts are available.
- **Expense limit validation:** Applies meal, lodging, and ground transportation limits.
- **Approval threshold checks:** Identifies claims requiring additional approval.
- **Deterministic decision engine:** Uses Python to calculate reimbursable amounts, deductions, and final decisions.
- **Structured output:** Uses Pydantic to validate input data and the final result structure.
- **Results dashboard:** Summarizes claim decisions and financial outcomes.
- **Local execution:** Uses Ollama to run the LLM locally without requiring an external LLM API key.

## Technology Stack

- Python 3.11+
- LangChain
- Ollama
- Llama 3.2
- ChromaDB
- `nomic-embed-text` embeddings
- Pydantic
- PyPDF
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
LLM Agent (Llama 3.2)
        |
        v
Policy Retrieval (ChromaDB)
        |
        v
Policy and Validation Tools
        |
        v
Deterministic Decision Engine
        |
        v
Structured Output Validation
        |
        v
JSON Results
        |
        v
Results Dashboard
```

The LLM assists with interpreting claims, identifying relevant policy context, and selecting tools. Python applies the defined business rules, calculates reimbursement amounts and deductions, and enforces the final decision logic.

This separation improves consistency, supports auditing, and reduces the risk of relying on free-form LLM responses for financial calculations.

## Decisions Supported

The agent produces one of four decisions:

| Decision | Meaning |
|---|---|
| `APPROVE` | The claim meets the applicable reimbursement rules. |
| `PARTIAL_APPROVE` | The claim is eligible, but some amount must be deducted under policy limits. |
| `REJECT` | The claim contains no reimbursable expenses. |
| `MANUAL_REVIEW` | A human reviewer must resolve missing information, exceptions, or other policy concerns. |

For claims requiring manual review, `approved_amount` and `deducted_amount` are represented as `null` when the final financial outcome has not been determined. This prevents an unresolved claim from being presented as a finalized financial decision.

## Sample Claim Scenarios

The notebook evaluates all five claims provided in the assignment.

| Claim ID | Expected Decision | Reason |
|---|---|---|
| CLM-001 | `APPROVE` | Eligible expenses with required receipts and amounts within policy limits. |
| CLM-002 | `REJECT` | Spa and minibar expenses are ineligible. |
| CLM-003 | `PARTIAL_APPROVE` | Lodging exceeds the permitted nightly limit. |
| CLM-004 | `MANUAL_REVIEW` | Business-class airfare, a missing hotel receipt, and a claim exceeding $2,000. |
| CLM-005 | `MANUAL_REVIEW` | Missing receipt for a client dinner expense and insufficient information to calculate the meal limit. |

The notebook contains the structured results, policy references, tool-call information, validation checks, and dashboard.

## Setup and Installation

### Prerequisites

- Python 3.11 or later
- Jupyter Notebook
- Ollama installed and running
- Sufficient local resources to run the selected LLM

### 1. Install Python dependencies

Run the following command in your terminal:

```bash
pip install langchain langchain-ollama langchain-chroma chromadb pypdf pydantic pandas matplotlib ollama jupyter
```

### 2. Install Ollama

Install Ollama from its official website:

https://ollama.com/

Ensure that the Ollama service is running before executing the notebook.

### 3. Download the required models

Download the LLM:

```bash
ollama pull llama3.2
```

Download the embedding model:

```bash
ollama pull nomic-embed-text
```

Verify that the models are available:

```bash
ollama list
```

### 4. Prepare the project files

Clone or download this repository.

Ensure that the policy PDF and sample claims JSON are available at the paths expected by the notebook. If they are stored in a `data` directory, the structure should be:

```text
PolicyGuard-AI/
├── SumitSingh.ipynb
├── README.md
└── data/
    ├── Reimbursement_Policy.pdf
    └── sample_claims.json
```

Adjust the notebook filename or paths if your repository uses different names.

### 5. Run the notebook

1. Open the notebook in Jupyter.
2. Select the Python environment containing the required dependencies.
3. Confirm that Ollama is running and both models are available.
4. Select **Kernel → Restart Kernel and Run All Cells**.
5. Review the claim evaluation results, tool-call information, final JSON output, and dashboard.

No external LLM API key or environment variable is required for the local Ollama configuration.

## Design Decisions

### Why use a deterministic decision engine?

Financial decisions should be predictable and auditable. The LLM assists with interpretation and tool selection, while Python applies reimbursement rules, calculates amounts, and determines the final decision.

### Why use retrieval-augmented generation?

Retrieving relevant policy rules provides the agent with supporting context before a claim is evaluated. Policy references also help reviewers understand which rules support a decision.

### Why route claims to manual review?

The system avoids forcing a decision when important information is missing or a policy exception requires human judgment. Examples include missing receipts, business-class airfare, and claims exceeding the approval threshold.

### Why use a local LLM?

Ollama enables the prototype to run locally without depending on a paid external LLM API. This also reduces the need to transmit claim information to an external model service.

### Why keep the implementation lightweight?

The assignment focuses on practical GenAI, tool usage, policy enforcement, and reliable structured outputs rather than building a complete enterprise reimbursement platform.

## Limitations and Future Improvements

- Add support for claim submission through a web interface or API.
- Integrate with a receipt storage system for attachment verification.
- Expand automated testing for unusual and conflicting claims.
- Evaluate retrieval quality across a larger policy collection.
- Add persistent audit logging and reviewer feedback.
- Improve confidence calibration and monitoring.
- Add authentication and access controls before any real-world deployment.

## Project Scope

This is a demonstration prototype built using the mock policy and five sample claims supplied with the HCL assignment.

It is not intended to replace an organization's official reimbursement approval process. All final approval decisions should remain subject to the organization's authorization requirements.

## Deliverable

The Jupyter notebook is the primary project deliverable. It contains the implementation, setup instructions, sample outputs, evaluation checks, dashboard, and design notes.
