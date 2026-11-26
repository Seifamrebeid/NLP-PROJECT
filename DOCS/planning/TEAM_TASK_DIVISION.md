# Agentic RAG Project: Team Task Division

## Team Working Agreement

Tasks and subtasks are not pre-assigned to specific members. The team agrees who picks up each piece of work. Everyone should review and test parts built by teammates. Each member must make visible commits and pull requests using their own GitHub account.

## Project Goal

Build an Agentic Retrieval-Augmented Generation (RAG) customer-support assistant for **TechStore**, a fictional online electronics store. Use Python, LangChain, and LangGraph. The system will answer questions using the fictional TechStore knowledge base, route customer intents, retrieve policy or product evidence, evaluate answer quality, retry weak retrieval with a limit, and escalate unresolved or sensitive cases to human support using LangGraph checkpointing and interrupts. An interactive interface must show routes, sources, retries, and escalation status.

### Data Sources and Their Roles

- **Primary retrieval knowledge base:** The team-authored fictional TechStore policies, FAQ, product catalog, and troubleshooting guide. These documents define the store's authoritative answers.
- **Intent and evaluation dataset:** [Bitext Customer Support Dataset](https://huggingface.co/datasets/bitext/Bitext-customer-support-llm-chatbot-training-dataset), 26,872 synthetic records across 27 intents and 11 categories (ACCOUNT, ORDER, REFUND, INVOICE, CONTACT, PAYMENT, FEEDBACK, DELIVERY, SHIPPING, SUBSCRIPTION, CANCEL). Use its `instruction`, `category`, `intent`, `response`, and `flags` fields for intent examples, routing tests, and evaluation cases.
- **Important limitation:** Bitext is synthetic training data, not genuine TechStore customer history and not the authority for TechStore policies. If an example response conflicts with a fictional TechStore policy, follow the policy document.
- **Known data issues (measured on the committed CSV):** about 13,000 responses and 6,700 instructions contain unfilled `{{placeholders}}` (for example `{{Order Number}}`); 2,237 instructions are duplicates; the `flags` field is made of undocumented letter codes; there are no product, warranty, or troubleshooting examples, so those routes need team-written test cases. Task 5 handles the first three and Task 8 the last.

## Ordered Tasks

Complete tasks in order because later tasks depend on the interfaces and outputs established earlier. Tasks are not pre-assigned: the team decides who takes each task and subtask. The team may work on independent subtasks at the same time after their prerequisites are agreed.

### Task Status and Dependencies

Each task heading has a status marker:

- 🟢 **Ready:** every prerequisite task below is finished, so the task can start now.
- 🔴 **Blocked:** at least one prerequisite task is unfinished; the table names it.

A task counts as finished when all its checkboxes are checked and its checkpoint is met. When a task finishes, recolor every task whose prerequisites are now all finished, in the same pull request as the checkbox update (see rule 12 in `PROJECT_INSTRUCTIONS.md`). Tasks that share a prerequisite can run in parallel, for example Tasks 2 and 3 after Task 1. Status as of the last update: only Task 1 is ready; it is in progress.

| Task | Prerequisites | Why |
| ---- | ------------- | --- |
| 1 | None | Creates the repository and the domain decision. |
| 2 | 1 | The Task 1 checkpoint requires the repository and domain decision before document collection. |
| 3 | 1 | Needs the agreed domain; can run in parallel with Task 2. |
| 4 | 3 | Needs the agreed libraries and shared settings. |
| 5 | 2, 3 | Needs the documents and the metadata format. |
| 6 | 3, 5 | Needs loaded documents in the agreed representation. |
| 7 | 4, 6 | Needs chunks and the retrieval configuration. |
| 8 | 3, 4 | Needs the graph state and configuration. |
| 9 | 7, 8, 11 | Needs the retriever, the routes, and the shared LLM client. |
| 10 | 9, 11 | Needs the working retrieval-to-answer path and the LLM client. |
| 11 | 3, 4 | Needs the graph input/output contracts and the environment configuration; must be ready before Task 9 starts. |
| 12 | 9, 11 | Needs the graph to checkpoint and the LLM client. |
| 13 | 9, 12 | Needs the graph interface and the HITL actions. |
| 14 | 2, 8, 12 | The test set needs the documents and routes; measuring needs the HITL flow. |
| 15 | 9, 10, 11, 12, 13, 14 | Integrates everything built before it. |
| 16 | 14, 15 | Needs measured results and a stable system for screenshots. |
| 17 | 16 | Final review of the finished report and repository. |

### 🟢 Task 1: Create the repository and agree on the project

**Subtasks**

- [x] Create the shared GitHub repository, invite everyone, coordinate the domain decision, and open the first issues.
- [ ] Review the project requirements and propose a practical RAG workflow and technical scope.
- [ ] Review the Bitext dataset and propose example TechStore customer questions across its support categories.
- [x] Create the initial folder structure and `.gitignore`.
- [ ] Agree that TechStore is the fictional online electronics store, its target users are customers seeking support, and its problem is finding accurate answers across policies and product information; create personal feature branches.

**Checkpoint:** Repository, domain decision, and first issues are ready before document collection begins.

### 🔴 Task 2: Find and prepare the domain documents

Use the Bitext Customer Support Dataset for synthetic intent examples and evaluation cases. Create a separate fictional TechStore knowledge base from the ten documents listed below; those policy and product documents are the authoritative retrieval source.

#### Small tasks and checkpoints

- [ ] Coordinate the TechStore policy scope, provide the shared writing template, and write the Escalation Policy covering complaints, exceptions, and unresolved cases.
- [ ] Write the Return Policy, Refund Policy, and Warranty Policy, keeping their rules consistent and specific enough to retrieve.
- [ ] Load and inspect the Bitext dataset, verify its fields and license/usage terms, and write the Shipping Policy, Order Management guide, and Customer FAQ. Record the exact license name, its conditions (for example attribution), and the known data issues in the README Data Sources section. The license has been confirmed to allow redistribution, so the committed CSV stays; add the required credit line.
- [ ] Write the Payment Policy, Product Catalog, and Troubleshooting Guide, including realistic but clearly fictional products and specifications.
- [ ] Review the ten documents for contradictions, missing details, and clear human-escalation cases.

**Checkpoint:** The Bitext source and its usage terms are documented, all ten fictional TechStore documents exist, and the team has reviewed them for consistency.

### 🔴 Task 3: Agree on the architecture and shared data formats

Draft the architecture and define the interfaces that the team will use together:

- A graph state containing the original query, intent, query complexity, required data source, route, retrieved documents, draft/final answer, evaluation result, retry count, and HITL status.
- A TechStore document representation that preserves text, title, policy section, document version, and source type; Bitext examples retain their instruction/category/intent/response/flags labels separately.
- Consistent route and status names.
- The graph inputs and outputs expected by the UI.
- A workflow diagram covering routing, retrieval, evaluation, refinement, and human review.

The team will review the design before implementation. Record the agreed contracts in the README or a short architecture document.

#### Small tasks and checkpoints

- [ ] Lead the design discussion and record decisions the team approves in a decision log (`DOCS/architecture/DECISIONS.md`) that states each decision's tradeoffs.
- [ ] Design the RAG state, retrieval-to-answer flow, graph routes, and retry cycle; draw the state-schema diagram; and document the graph input/output contract (API documentation).
- [ ] Define the Bitext example fields and TechStore policy metadata needed for intent testing, retrieval, and evaluation, including an `audience` field (customer or internal) that access control can filter on.
- [ ] Define the graph result fields and HITL statuses the interface needs to display.
- [ ] Review the shared names and approve the workflow diagram before implementation.

**Checkpoint:** All four members approve the interfaces before separate modules are implemented.

### 🔴 Task 4: Set up configuration, dependencies, and project instructions

The setup work covers:

- Add the initial `requirements.txt` or `pyproject.toml` with the agreed libraries.
- Add `.env.example` with placeholder values, never real API keys.
- Add a central configuration module for model names, vector-store path, chunk parameters, retrieval count, quality threshold, and maximum retries.
- Document installation, environment setup, and local run instructions.
- Confirm the setup works in a clean Python environment.

Review the repository setup, check data and vector-store settings, and check graph-related settings.

#### Small tasks and checkpoints

- [ ] Create task branches and review repository setup and secret-handling rules. Write the security and privacy checklist from `PROJECT_INSTRUCTIONS.md` (input limits, prompt-injection rules, role-based access, log masking, synthetic-data-only rule) so later tasks can check against it.
- [ ] List and verify the LangChain, LangGraph, embedding, and vector-store dependencies needed for the RAG workflow.
- [ ] Specify data-file and dataset-path settings needed by ingestion and evaluation.
- [ ] Add dependencies, `.env.example`, central configuration, and initial setup instructions.
- [ ] Install the project in a clean environment and report setup problems.

**Checkpoint:** A fresh environment can install the project without committed secrets.

### 🔴 Task 5: Load and clean the documents

Implement loading for the Bitext dataset and the team's Markdown or text policy files, then normalize their content. Preserve Bitext fields for intent/evaluation use and preserve TechStore document title, section, version, and source type for retrieval. Check the document interface and test representative and malformed inputs.

#### Small tasks and checkpoints

- [ ] Confirm the fictional TechStore policies are in scope and record their version/date so the team can reproduce tests.
- [ ] Define the text and metadata format the loader must pass into the RAG pipeline.
- [ ] Load the Bitext dataset and TechStore policy files, preserving dataset fields and policy metadata.
- [ ] Clean the Bitext data: handle the `{{placeholder}}` fields, remove duplicate instructions before any train/held-out split, and decode and document the `flags` codes. Document the accepted ingestion formats and metadata (ingestion documentation).
- [ ] Test the loaders with empty, malformed, and representative customer examples and policy documents, including records that contain placeholders or duplicates.
- [ ] Review loaded examples and verify that dataset labels and policy source/version metadata remain attached.

**Checkpoint:** Loaded documents contain usable text and traceable source metadata.

### 🔴 Task 6: Split documents into searchable chunks

Implement chunking for the fictional TechStore knowledge-base documents with configurable size and overlap. Preserve each policy or catalog source and section. Use Bitext instructions and responses as labeled intent/evaluation examples; do not treat them as authoritative TechStore policy chunks.

#### Small tasks and checkpoints

- [ ] Agree with the team on a manageable chunking approach and record the selected settings.
- [ ] Lead chunk design for the RAG pipeline and implement chunking that keeps each TechStore policy section's meaning intact.
- [ ] Check sample chunks for lost eligibility, deadline, order, refund, warranty, or escalation rules.
- [ ] Test short records, missing fields, and chunk-boundary behavior.
- [ ] Review chunk examples before the full dataset is indexed.

**Checkpoint:** TechStore policy chunks retain their source metadata and pass the chunking tests; Bitext examples remain labeled for routing/evaluation.

### 🔴 Task 7: Build embeddings, the vector store, and retrieval

Build the embedding and vector-store pipeline for the TechStore policy and product documents. Keep Bitext examples in the labeled intent/evaluation dataset unless the team explicitly defines a separate, non-authoritative example retriever.

The pipeline work covers:

- Configure the agreed embedding model.
- Create and persist the vector store from the processed chunks.
- Implement a retriever that accepts a query and returns ranked documents.
- Make result count and persistence location configurable.
- Return a clear empty-result outcome when no useful documents are found.

Check returned documents against the sample questions and integrate the retriever interface with the graph.

#### Small tasks and checkpoints

- [ ] Review the model and storage choices and confirm the team can run them with its available resources.
- [ ] Review the embedding and retriever interface for graph integration; compare results for customer questions against the TechStore knowledge base.
- [ ] Prepare labeled Bitext examples and check that retrieved TechStore policies support the expected answer.
- [ ] Configure embedding settings, persist the index, and handle empty-result responses.
- [ ] Test index creation and retrieval before connecting the full graph.

**Checkpoint:** Sample questions return relevant chunks with source metadata.

### 🔴 Task 8: Implement intent routing in LangGraph

Implement the conditional graph logic, with the scope and review coordinated across the team. Route requests by intent, complexity (simple single-fact versus multi-part or multi-document), and required data source through these paths:

- TechStore policy or product questions that need knowledge-base retrieval.
- Customer-support intents such as order management, payments, delivery, refunds, and account management; use Bitext labels as training/testing examples, then retrieve the matching TechStore policy when an answer needs company information.
- General conversational questions that do not need policy retrieval.
- Out-of-scope requests, unresolved complaints, policy exceptions, or cases with insufficient evidence that should receive a safe response or human escalation.

Review graph state and edges, provide labeled Bitext cases and verify expected intents, and verify conversation, out-of-scope, and escalation status behavior. The route decision, complexity, and route history must be available to the UI.

#### Small tasks and checkpoints

- [ ] Write the routing rubric (intent, complexity, required data source) and confirm the three branches the course spec requires (retrieval, conversation, out-of-scope) plus the human-escalation path.
- [ ] Implement and review the LangGraph state updates and conditional edges, prioritizing the retrieval path.
- [ ] Use Bitext intent/category examples to create customer queries and verify that the expected TechStore intents route correctly. Fill `data/sample_queries.json` with at least one sample query per branch (retrieval, conversation, out-of-scope, escalation) and per complexity level; write product, warranty, and troubleshooting queries by hand because Bitext has none.
- [ ] Write general and out-of-scope examples and define the route/status values the UI will show.
- [ ] Review and test branch examples; record the agreed acceptance results.

**Checkpoint:** Every sample input reaches its expected branch and the selected route is available in graph output.

### 🔴 Task 9: Connect retrieval, answer generation, and source display

Connect the retrieval branch to the TechStore vector store and add the generation step that answers from retrieved policy or product context. The answer must not invent a company rule or treat a Bitext response as TechStore policy. Carry source documents through to the graph output. Check policy grounding and UI compatibility, and review the integrated workflow. Prerequisite: the core of the shared LLM client from Task 11 (client plus environment configuration) must be merged before this task starts.

#### Small tasks and checkpoints

- [ ] Coordinate integration and check the end-to-end path on a sample TechStore customer question. Verify that retrieval applies the `audience` filter so customers never receive internal-only documents, and that retrieved text is treated as data, not instructions.
- [ ] Lead the core RAG implementation: retrieve relevant TechStore policies or product details, pass evidence to generation, and return source metadata.
- [ ] Verify that generated order, delivery, refund, or warranty details match the fictional TechStore policies.
- [ ] Connect the generation step to the shared LLM client and make sure answers and citations fit the UI result format.
- [ ] Test that the answer is grounded in retrieved TechStore documents and sources are visible.

**Checkpoint:** A domain query produces an answer and the evidence used to generate it.

### 🔴 Task 10: Add evaluation, query refinement, and retry limits

Build the quality loop:

- Evaluate whether retrieved documents are relevant and whether the draft answer is supported by them.
- Record an evaluation result and a reason, not just a pass/fail value.
- When quality is too low, produce a refined search query and retrieve again.
- Increment and preserve a retry counter.
- Stop retrying when the configured maximum is reached and return a clear result or request human help.

Review the graph cycle and stop condition, test the displayed retry details, and review failure behavior. The refinement prompt uses the shared LLM client delivered early from Task 11.

#### Small tasks and checkpoints

- [ ] Agree on the quality threshold and maximum retry count with the team.
- [ ] Connect evaluator outcomes to the LangGraph cycle and ensure the retry limit always stops the loop.
- [ ] Define retrieval and grounded-answer checks, build evaluation examples, and inspect evaluator results.
- [ ] Implement or tune the query-refinement prompt and expose retry count/details to the UI.
- [ ] Test accepted answers, failed retrieval, a refinement retry, and maximum-retry termination.

**Checkpoint:** The workflow recovers from weak retrieval when possible and always stops at the configured limit.

### 🔴 Task 11: Integrate the LLM client and prompts

Implement the shared model client and prompts for routing (if model-assisted), answer generation, evaluation, query refinement, conversation, and out-of-scope handling. API keys must load from environment variables, errors must be understandable, and prompts should require evidence-based answers for retrieved content.

Review the evaluation and refinement prompts, review the prompts' expected graph inputs and outputs, and review configuration and secret handling. Scheduling: deliver the client and environment configuration first, before Task 9 starts, and add the remaining prompts as Tasks 9 and 10 need them.

#### Small tasks and checkpoints

- [ ] Review API-key handling and ensure no credentials are committed. Implement input validation (length and content limits on customer queries) and prompt-injection defenses for customer queries and retrieved documents; add log masking so secrets and personal data never reach logs or error reports.
- [ ] Specify the graph inputs/outputs and refine the evidence-grounded RAG answer prompt.
- [ ] Review evaluation and refinement prompts against Bitext intent examples and expected TechStore policy sources.
- [ ] Implement the shared LLM client, environment configuration, and prompts for routing, generation, evaluation, refinement, and conversation.
- [ ] Test representative prompts and document errors or inconsistent outputs.

**Checkpoint:** Model calls use the shared client, prompt outputs fit the graph, and configuration contains no real credentials.

### 🔴 Task 12: Add human-in-the-loop pause and resume

Integrate LangGraph checkpointing and interrupts for cases requiring human support, such as unresolved complaints, policy exceptions, or answers with insufficient evidence. The workflow must retain enough state to resume. Wire the approve, reject, edit, or clarify actions, supply support cases that should escalate, and test pause and resume.

#### Small tasks and checkpoints

- [ ] Coordinate the review policy, define which reviewer roles may approve, reject, or edit (role-based access control), and lead the end-to-end pause/resume acceptance check.
- [ ] Implement LangGraph checkpointing and interrupt/resume behavior in the workflow.
- [ ] Provide an unresolved complaint, policy-exception, or low-evidence support example that should trigger human review, and add it to `data/sample_queries.json` as the sample HITL-interrupt query.
- [ ] Add the UI actions for approval, rejection, edits, or clarification and show pending status.
- [ ] Test approval and at least one change/rejection path, confirming state is preserved.

**Checkpoint:** A reviewer can act on the paused graph and the graph resumes with the correct state.

### 🔴 Task 13: Build the interactive interface

Build the UI using the team's chosen framework. It must allow customers to submit a TechStore support question and show:

- The selected route and current workflow status.
- The answer and its source documents.
- Evaluation details, refined queries, and retry count.
- The pending HITL review state and controls to approve, reject, edit, or clarify.
- The final outcome after resumption.

Supply the graph interface, test the integrated application, and check that sources and evaluation information are clear.

#### Small tasks and checkpoints

- [ ] Review the interface against the assignment requirements and coordinate a complete demo.
- [ ] Provide and integrate the graph invocation/result interface, including route history and resume state.
- [ ] Check that product/order details, policy citations, and evaluation messages are understandable and accurate.
- [ ] Build the query input, answer/source views, progress details, and HITL controls.
- [ ] Run product/policy, customer-support intent, general conversation, out-of-scope, retry, and human-escalation examples through the UI.

**Checkpoint:** A user can see and complete each required workflow from the interface.

### 🔴 Task 14: Create the evaluation set and measure results

Organize a repeatable test set with expected routes, relevant TechStore policy sources, and expected answer facts. Use held-out Bitext instructions and labels for intent/routing tests, plus team-written cases for TechStore policy answers, insufficient evidence, and escalation. Do not treat Bitext's synthetic response as the authoritative answer when testing policy-grounded responses. The team will run tests and report:

- Route accuracy.
- Retrieval relevance.
- Answer faithfulness and groundedness.
- Retry frequency and HITL completion.
- Latency for each route and for the complete workflow.

The report must state how metrics were calculated and note any manual judgments. Do not present unmeasured or fabricated results.

#### Small tasks and checkpoints

- [ ] Review the evaluation plan and ensure report conclusions match the recorded measurements.
- [ ] Run retrieval and graph-route tests and help diagnose low-quality results.
- [ ] Lead the test set, expected evidence, metric definitions, and relevance/groundedness assessment. Use only synthetic or anonymized cases; no real customer data.
- [ ] Record results in a reproducible format, write the testing and evaluation documentation (methods and actual results), and prepare tables or screenshots for the report.
- [ ] Run assigned cases and report actual results, including failures and latency.

**Checkpoint:** Results can be reproduced and no metric or result is invented.

### 🔴 Task 15: Integrate, test, and stabilize the complete system

Coordinate an end-to-end integration pass. Each member tests the branch they implemented and at least one branch implemented by another member. The team will:

- Run the automated tests and fix project-related failures.
- Check a clean setup using only the README and `.env.example`.
- Test missing documents, empty retrieval, model/API errors, retry limit, and resume behavior.
- Check that no secrets, private data, or unnecessary generated files were committed.
- Review pull requests and merge approved work into `main` only after the review process.

#### Small tasks and checkpoints

- [ ] Coordinate integration order, review completion, and merge approved pull requests. Run the security pass against the Task 4 checklist: prompt-injection attempts in queries and in a poisoned retrieved document, an attempt to retrieve an internal-only document, oversized or malformed input, and a check that logs and the repository contain no secrets or personal data.
- [ ] Test and fix graph, retrieval, and retry integration issues.
- [ ] Test ingestion, metadata, source accuracy, and evaluation behavior.
- [ ] Test configuration, model errors, interface behavior, and HITL controls.
- [ ] Review a teammate's pull request, run the full tests, and record remaining limitations.

**Checkpoint:** The integrated app starts cleanly and the required branches and failure paths have been tested.

### 🔴 Task 16: Complete the README, screenshots, and technical report

Assemble the README and coordinate screenshots and report formatting. Each contributor supplies and verifies the content for the work they did:

- Project motivation, final architecture rationale, integration process, and team contribution matrix.
- TechStore document sources and authorship, Bitext dataset and its synthetic-data limitation, ingestion/evaluation methods, measured results, and interpretation.
- LangGraph state, nodes, conditional edges, retry cycle, HITL checkpoint design, and technical challenges.
- UI design and screenshots, setup instructions, README, report formatting, limitations, and future work (with team review).

The report must be a PDF (`DOCS/reports/final_report.pdf`) and must include the title page with each member's GitHub username, team contribution matrix, problem/domain motivation (target users, why single-pass RAG is inadequate, and how agentic orchestration solves it), system architecture and workflow diagram (including the state schema, nodes, conditional edges, loops, and HITL interrupt points, with the design rationale), technical challenges and solutions, quantitative and qualitative evaluation, screenshots of routing/refinement/HITL, limitations, and future work.

#### Small tasks and checkpoints

- [ ] Write the problem definition and project motivation (target users, why single-pass RAG is inadequate, how agentic orchestration helps), coordinate the contribution matrix, write the security and privacy section, and review final consistency.
- [ ] Write the RAG architecture, LangGraph state/routes, retrieval/refinement loop, and workflow diagram explanation; include the state-schema diagram and the technical challenges and solutions for the graph and retrieval.
- [ ] Write the TechStore knowledge-base and Bitext dataset sections, clearly explain that Bitext is synthetic and not the policy authority, document the data challenges (placeholders, duplicates, flags, license) and how they were handled, and report evaluation methods and measured results.
- [ ] Assemble the README (including a sample-queries section with one query per conditional branch and the HITL interrupt, taken from `data/sample_queries.json`), setup instructions, title page with GitHub usernames, UI screenshots, HITL walkthrough, limitations, future work, and the PDF export of the report.
- [ ] Review the section describing your work and verify the report against the assignment requirements.

**Checkpoint:** README is runnable as written and the report has all required sections and evidence.

### 🔴 Task 17: Final review and submission

Lead a final checklist review with all members. Confirm that:

- The repository link and submitted report are correct.
- The source code is modular and includes ingestion, graph state, nodes, edges, prompts, and UI.
- `.env.example`, setup instructions, sample queries, and architecture diagrams are present.
- The application demonstrates all routing branches, refinement, and HITL pause/resume.
- Evaluation metrics and limitations are reported honestly.
- The contribution matrix matches actual work and GitHub history.
- Each member has personal commits, pull requests, and code reviews visible in GitHub.

#### Small tasks and checkpoints

- [ ] Lead the final checklist, confirm the repository/report submission, and verify team contributions are represented fairly. Compare each member's commits and PRs against the contribution matrix and rebalance any remaining work so contributions are roughly equal, as the course spec requires.
- [ ] Demonstrate the complete RAG graph, including retrieval, refinement, retry limit, and source-grounded response.
- [ ] Verify the data sources, sample cases, evaluation results, and report figures.
- [ ] Verify setup instructions, `.env.example`, UI screenshots, and HITL pause/resume demonstration.
- [ ] Confirm personal commits, pull requests, reviews, and remaining limitations before submission.

**Checkpoint:** The complete repository and report are ready for submission and the contribution history is verifiable.

## Suggested Repository Structure

```text
Agentic-RAG-System/
├── app/
│   ├── streamlit_app.py
│   └── components/
├── data/
│   ├── raw/
│   ├── processed/
│   └── sample_queries.json
├── DOCS/
│   ├── architecture/
│   │   └── DECISIONS.md
│   ├── instructions/
│   │   └── PROJECT_INSTRUCTIONS.md
│   ├── planning/
│   │   └── TEAM_TASK_DIVISION.md
│   ├── requirements/
│   │   └── Agentic RAG System Project.pdf
│   └── reports/
│       └── final_report.pdf
├── results/
│   ├── screenshots/
│   └── evaluation_results.csv
├── src/
│   ├── config.py
│   ├── schemas.py
│   ├── ingestion/
│   ├── retrieval/
│   ├── graph/
│   ├── nodes/
│   ├── edges/
│   ├── llm/
│   ├── prompts/
│   ├── security/
│   └── human_loop/
├── tests/
├── .env.example
├── .gitignore
├── requirements.txt
└── README.md
```

## GitHub Working Rules

The full mandatory workflow is in [Mandatory GitHub Workflow](../instructions/PROJECT_INSTRUCTIONS.md#mandatory-github-workflow). Summary:

- Do not commit directly to `main`; create a feature branch for each task.
- Push the branch and open a pull request; teammates review it. Seif merges only after approval.
- Keep pull requests focused and include tests or clear manual verification steps.
- Use each person's own GitHub account for commits and reviews. If Seif commits for a teammate, say so in the commit message and PR.
- Use descriptive commit messages, for example:

```text
feat(ingestion): load domain PDF documents
feat(graph): add conditional query routing
feat(retrieval): add bounded query refinement
feat(hitl): pause and resume graph execution
feat(ui): show route and source documents
test(evaluation): add routing and grounding cases
docs(report): document architecture and results
```

## Final Acceptance Checklist

### Application

- [ ] Custom domain documents are loaded, chunked, embedded, and indexed.
- [ ] Domain-specific queries use vector retrieval.
- [ ] General conversation bypasses retrieval.
- [ ] Out-of-scope requests are handled appropriately.
- [ ] LangGraph conditional routing controls the branches.
- [ ] The evaluator can trigger query refinement and re-retrieval.
- [ ] The retry counter prevents an infinite loop.
- [ ] LangGraph checkpointing and interrupts support human review and resume.
- [ ] The UI displays route, sources, refinements, and HITL state.
- [ ] Routing considers intent, complexity, and required data source.
- [ ] Input validation, prompt-injection defenses, role-based access (`audience` filter and reviewer roles), and log masking are implemented and tested.
- [ ] Tests cover the important branches and failure cases.

### Repository and Report

- [ ] README contains architecture, setup, environment, and sample queries for each conditional branch and the HITL interrupt.
- [ ] The Bitext license name, conditions, and credit line are recorded, and its known data issues are handled.
- [ ] `.env.example` contains placeholders only; no secrets are committed.
- [ ] Results include relevance, faithfulness, groundedness, and latency measures.
- [ ] Screenshots show conditional routing, self-correction, and HITL.
- [ ] The report is a PDF and contains the title page with GitHub usernames, contribution matrix, problem definition, state-schema and workflow diagram, challenges, results, limitations, and future work.
- [ ] All four members have verifiable personal commits, pull requests, and reviews.
