# PolicyGuard AI — Travel Reimbursement Approval Agent

## Overview

PolicyGuard AI is a lightweight GenAI and Agentic AI prototype that evaluates employee travel reimbursement claims against a supplied travel policy. It checks expense eligibility, receipt requirements, per-diem limits, approval thresholds, and exceptions, then produces a structured recommendation for each claim.

The notebook uses **Llama 3.2 through Ollama** for reasoning and tool orchestration. Python's deterministic decision engine remains authoritative for policy enforcement, calculations, deductions, approval thresholds, and final decisions.

### Supported decisions

- `APPROVE`
- `PARTIAL_APPROVE`
- `REJECT`
- `MANUAL_REVIEW`

## Architecture

```text
Sample Claim JSON
       |
       v
Pydantic Input Validation
       |
       v
Llama 3.2 Agent (Ollama)
       |
       v
Tool Selection and Policy Context
       |
       +--> policy_lookup
       +--> check_receipt
       +--> check_limit
       +--> check_approval_threshold
       |
       v
Deterministic Decision Engine
       |
       v
Pydantic Output Validation
       |
       v
Final JSON Results
       |
       v
Summary Tables and Dashboard
```

## Features

- **Policy lookup:** Retrieves applicable rules and policy references for expense categories.
- **Receipt validation:** Checks whether required receipts are attached.
- **Limit checking:** Applies configured per-diem or category limits and calculates deductions.
- **Approval threshold checking:** Determines the applicable approval tier from the reimbursable amount.
- **Agentic tool orchestration:** Lets the LLM select relevant tools for a claim.
- **Deterministic final decisions:** Keeps financial calculations and final outcomes in Python rather than relying on free-form LLM output.
- **Schema validation:** Uses Pydantic to validate claim inputs and the final output contract.
- **Audit evidence:** Captures the tools selected by the agent for review.
- **Evaluation and visual summaries:** Compares outcomes with expected results and displays claim summaries and charts.

## Technology Stack

- Python 3.11+
- Jupyter Notebook / JupyterLab
- [Ollama](https://ollama.com/) for local model execution
- Llama 3.2 (`llama3.2`)
- Pydantic for data validation
- pandas for tabular summaries
- Matplotlib for visualizations

## Prerequisites

Install or configure the following before running the notebook:

1. Python 3.11 or later.
2. Jupyter Notebook or JupyterLab.
3. Ollama installed and running locally.
4. The `llama3.2` model downloaded through Ollama.

No external LLM API key or environment variable is required by the notebook.

## Setup

### 1. Create and activate a Python environment (optional but recommended)

Using Conda:

```bash
conda create -n policyguard-ai python=3.11 -y
conda activate policyguard-ai
```

If you already have a suitable environment, activate that environment instead.

### 2. Install dependencies

```bash
python -m pip install ollama pydantic pandas matplotlib jupyterlab
```

### 3. Download the model

Make sure the Ollama application/service is running, then execute:

```bash
ollama pull llama3.2
ollama list
```

Confirm that `llama3.2` appears in the model list.

### 4. Launch JupyterLab

```bash
jupyter lab
```

Open `SumitSingh.ipynb` in JupyterLab and select the Python kernel for the environment where the dependencies were installed.

## Run the Notebook

1. Start Ollama and confirm that the `llama3.2` model is available.
2. Open `SumitSingh.ipynb` in JupyterLab or Jupyter Notebook.
3. Select the correct Python kernel.
4. Run the notebook from top to bottom using **Run All**.
5. Review the evaluation results, sample outputs, agent audit trail, and dashboard.
6. The final code cell prints the required JSON array.

The notebook includes five sample claims supplied in Appendix B of the assignment. It does not require real employee or company data.

## Agent Tools

The LLM can request the following Python tools:

| Tool | Purpose |
| --- | --- |
| `policy_lookup` | Retrieves applicable travel policy rules for an expense category. |
| `check_receipt` | Checks whether the expense has the receipt required by policy. |
| `check_limit` | Calculates the reimbursable amount and any deduction for applicable limits. |
| `check_approval_threshold` | Determines the approval tier based on the reimbursable amount. |

Tool calls and arguments are validated at the Python boundary. Invalid or incomplete arguments are handled as structured tool errors rather than being allowed to crash the full workflow.

## Decision and Reliability Design

The implementation separates probabilistic model behavior from deterministic business logic:

- **LLM:** Interprets the claim and selects useful tools.
- **Python tools:** Retrieve policy context and perform validation calculations.
- **Decision engine:** Applies the policy and determines the final outcome.
- **Pydantic:** Validates input models and the final output schema.

This separation is important for reimbursement processing because financial outcomes should be predictable, explainable, and auditable.

### Manual review

The prototype routes claims to `MANUAL_REVIEW` when policy exceptions or incomplete information require a human decision. Notebook examples include business-class airfare, missing required receipts, high-value claims, and missing documentation.

For claims awaiting manual review, `approved_amount` and `deducted_amount` are `null` rather than zero. This indicates that the financial outcome has not yet been finalized.

## Output Contract

Each claim result contains exactly these nine fields:

| Field | Description |
| --- | --- |
| `claim_id` | Identifier of the claim. |
| `decision` | One of the four supported decision values. |
| `approved_amount` | Final approved amount, or `null` while awaiting manual review. |
| `deducted_amount` | Final deduction amount, or `null` while awaiting manual review. |
| `missing_docs` | List of missing required documents. |
| `policy_refs` | Policy identifiers relevant to the decision. |
| `confidence` | Rule-based prototype confidence indicator; not a calibrated probability. |
| `explanation` | Human-readable explanation of the outcome. |
| `tools_used` | Tools used during claim evaluation. |

The final notebook cell prints the results as a JSON array.

## Policy Retrieval Choice

The prototype uses deterministic policy lookup rather than a vector database. The supplied policy is small, structured, and uses stable `POL-*` identifiers, so direct lookup provides exact references with less complexity.

For a larger, unstructured enterprise policy collection, the retrieval layer could be extended with document ingestion, chunking, embeddings, and vector or hybrid search.

## Assumptions and Limitations

- This is an assignment prototype, not a production reimbursement platform.
- The notebook uses the five sample claims provided with the assignment.
- The confidence value is a rule-based indicator and is not a statistically calibrated probability.
- The claim structure provides trip dates rather than individual expense dates. For the 30-day timeliness rule, the prototype uses `trip_end` as a proxy; production logic should validate each actual expense date.
- Manual-review claims do not receive finalized financial amounts until a reviewer resolves the issue.
- Local-model tool-calling quality may vary by model version and runtime.

## Potential Future Improvements

- Ingest policy documents from enterprise repositories.
- Add vector or hybrid retrieval for a larger policy corpus.
- Add receipt OCR and image validation.
- Detect duplicate claims.
- Strengthen schemas for tool arguments.
- Persist audit logs and evaluation results.
- Integrate a human approval workflow.
- Add monitoring, observability, and model/version management.
- Expose the workflow through a production API.

## Project File

- `SumitSingh.ipynb` — end-to-end implementation, sample claim processing, evaluation, audit trail, dashboard, and final JSON output.
