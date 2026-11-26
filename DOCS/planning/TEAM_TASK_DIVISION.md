# Agentic RAG Project: Team Task Division

## Team

| Member       | Team responsibility                                                                           |
| ------------ | --------------------------------------------------------------------------------------------- |
| **Seif**     | Team leader; coordinates progress, meetings, decisions, and final integration.                |
| **Mohamed**  | Has priority on core RAG, retrieval, and LangGraph workflow implementation.                   |
| **Abdullah** | Contributes to data preparation, retrieval testing, and evaluation throughout the project.    |
| **Ahmed**    | Contributes to model integration, HITL, interface, setup, and testing throughout the project. |

Seif is responsible for coordinating the team, but each task below assigns a concrete contribution to all four members. Mohamed receives priority on the core RAG work because he is especially interested in it; Abdullah and Ahmed also contribute to the RAG workflow, integration, tests, and documentation. Everyone should review and test parts built by teammates. Each member must make visible commits and pull requests using their own GitHub account.

## Project Goal

Build an Agentic Retrieval-Augmented Generation (RAG) customer-support assistant for **TechStore**, a fictional online electronics store. Use Python, LangChain, and LangGraph. The system will answer questions using the fictional TechStore knowledge base, route customer intents, retrieve policy or product evidence, evaluate answer quality, retry weak retrieval with a limit, and escalate unresolved or sensitive cases to human support using LangGraph checkpointing and interrupts. An interactive interface must show routes, sources, retries, and escalation status.

### Data Sources and Their Roles

- **Primary retrieval knowledge base:** The team-authored fictional TechStore policies, FAQ, product catalog, and troubleshooting guide. These documents define the store's authoritative answers.
- **Intent and evaluation dataset:** [Bitext Customer Support Dataset](https://huggingface.co/datasets/bitext/Bitext-customer-support-llm-chatbot-training-dataset), approximately 26,872 synthetic records across 27 intents and 10 categories. Use its `instruction`, `category`, `intent`, `response`, and `flags` fields for intent examples, routing tests, and evaluation cases.
- **Important limitation:** Bitext is synthetic training data, not genuine TechStore customer history and not the authority for TechStore policies. If an example response conflicts with a fictional TechStore policy, follow the policy document.

## Ordered Tasks

Complete tasks in order because later tasks depend on the interfaces and outputs established earlier. Every task assigns a concrete action to Seif, Mohamed, Abdullah, and Ahmed; Seif coordinates the work, while the named task specialist handles the main implementation. The team may work on independent subtasks at the same time after their prerequisites are agreed.

### Task 1: Create the repository and agree on the project

**Team assignments**

- [x] **Seif:** Create the shared GitHub repository, invite everyone, coordinate the domain decision, and open the first issues.
- [ ] **Mohamed:** Review the project requirements and propose a practical RAG workflow and technical scope.
- [ ] **Abdullah:** Review the Bitext dataset and propose example TechStore customer questions across its support categories.
- [x] **Ahmed:** Create the initial folder structure and `.gitignore`,
- [ ] **Everyone:** Agree that TechStore is the fictional online electronics store, its target users are customers seeking support, and its problem is finding accurate answers across policies and product information; create personal feature branches.

**Checkpoint:** Repository, domain decision, and first issue assignments are ready before document collection begins.

### Task 2: Find and prepare the domain documents

**Task owner: Abdullah**

Use the Bitext Customer Support Dataset for synthetic intent examples and evaluation cases. Create a separate fictional TechStore knowledge base from the ten documents listed below; those policy and product documents are the authoritative retrieval source.

#### Small tasks and checkpoints

- [ ] **Seif:** Coordinate the TechStore policy scope, provide the shared writing template, and write the Escalation Policy covering complaints, exceptions, and unresolved cases.
- [ ] **Mohamed:** Write the Return Policy, Refund Policy, and Warranty Policy, keeping their rules consistent and specific enough to retrieve.
- [ ] **Abdullah:** Load and inspect the Bitext dataset, verify its fields and license/usage terms, and write the Shipping Policy, Order Management guide, and Customer FAQ.
- [ ] **Ahmed:** Write the Payment Policy, Product Catalog, and Troubleshooting Guide, including realistic but clearly fictional products and specifications.
- [ ] **Everyone:** Review the ten documents for contradictions, missing details, and clear human-escalation cases.

**Checkpoint:** The Bitext source and its usage terms are documented, all ten fictional TechStore documents exist, and the team has reviewed them for consistency.

### Task 3: Agree on the architecture and shared data formats

**Lead: Mohamed**  
**Collaborators: Seif, Abdullah, Ahmed**

Mohamed will draft the architecture and define the interfaces that the members will use together:

- A graph state containing the original query, intent, route, retrieved documents, draft/final answer, evaluation result, retry count, and HITL status.
- A TechStore document representation that preserves text, title, policy section, document version, and source type; Bitext examples retain their instruction/category/intent/response/flags labels separately.
- Consistent route and status names.
- The graph inputs and outputs expected by the UI.
- A workflow diagram covering routing, retrieval, evaluation, refinement, and human review.

The team will review the design before implementation. Seif records the agreed contracts in the README or a short architecture document.

#### Small tasks and checkpoints

- [ ] **Seif:** Lead the design discussion and record decisions the team approves.
- [ ] **Mohamed:** Design the RAG state, retrieval-to-answer flow, graph routes, and retry cycle.
- [ ] **Abdullah:** Define the Bitext example fields and TechStore policy metadata needed for intent testing, retrieval, and evaluation.
- [ ] **Ahmed:** Define the graph result fields and HITL statuses the interface needs to display.
- [ ] **Everyone:** Review the shared names and approve the workflow diagram before implementation.

**Checkpoint:** All four members approve the interfaces before separate modules are implemented.

### Task 4: Set up configuration, dependencies, and project instructions

**Lead: Ahmed**  
**Collaborators: Seif, Abdullah, Mohamed**

Ahmed will:

- Add the initial `requirements.txt` or `pyproject.toml` with the agreed libraries.
- Add `.env.example` with placeholder values, never real API keys.
- Add a central configuration module for model names, vector-store path, chunk parameters, retrieval count, quality threshold, and maximum retries.
- Document installation, environment setup, and local run instructions.
- Confirm the setup works in a clean Python environment.

Seif reviews the repository setup; Abdullah checks data and vector-store settings; Mohamed checks graph-related settings.

#### Small tasks and checkpoints

- [ ] **Seif:** Create task branches and review repository setup and secret-handling rules.
- [ ] **Mohamed:** List and verify the LangChain, LangGraph, embedding, and vector-store dependencies needed for the RAG workflow.
- [ ] **Abdullah:** Specify data-file and dataset-path settings needed by ingestion and evaluation.
- [ ] **Ahmed:** Add dependencies, `.env.example`, central configuration, and initial setup instructions.
- [ ] **Everyone:** Install the project in a clean environment and report setup problems.

**Checkpoint:** A fresh environment can install the project without committed secrets.

### Task 5: Load and clean the documents

**Lead: Abdullah**  
**Collaborators: Mohamed, Ahmed**

Abdullah will implement loading for the Bitext dataset and the team's Markdown or text policy files, then normalize their content. Preserve Bitext fields for intent/evaluation use and preserve TechStore document title, section, version, and source type for retrieval. Mohamed checks the document interface; Ahmed tests representative and malformed inputs.

#### Small tasks and checkpoints

- [ ] **Seif:** Confirm the fictional TechStore policies are in scope and record their version/date so the team can reproduce tests.
- [ ] **Mohamed:** Define the text and metadata format the loader must pass into the RAG pipeline.
- [ ] **Abdullah:** Load the Bitext dataset and TechStore policy files, preserving dataset fields and policy metadata.
- [ ] **Ahmed:** Test the loaders with empty, malformed, and representative customer examples and policy documents.
- [ ] **Everyone:** Review loaded examples and verify that dataset labels and policy source/version metadata remain attached.

**Checkpoint:** Loaded documents contain usable text and traceable source metadata.

### Task 6: Split documents into searchable chunks

**Lead: Mohamed**  
**Collaborators: Abdullah, Ahmed**

Mohamed will implement chunking for the fictional TechStore knowledge-base documents with configurable size and overlap. Preserve each policy or catalog source and section. Use Bitext instructions and responses as labeled intent/evaluation examples; do not treat them as authoritative TechStore policy chunks.

#### Small tasks and checkpoints

- [ ] **Seif:** Agree with the team on a manageable chunking approach and record the selected settings.
- [ ] **Mohamed:** Lead chunk design for the RAG pipeline and implement chunking that keeps each TechStore policy section's meaning intact.
- [ ] **Abdullah:** Check sample chunks for lost eligibility, deadline, order, refund, warranty, or escalation rules.
- [ ] **Ahmed:** Test short records, missing fields, and chunk-boundary behavior.
- [ ] **Everyone:** Review chunk examples before the full dataset is indexed.

**Checkpoint:** TechStore policy chunks retain their source metadata and pass the chunking tests; Bitext examples remain labeled for routing/evaluation.

### Task 7: Build embeddings, the vector store, and retrieval

**Lead: Ahmed**  
**Collaborators: Abdullah, Mohamed**

Ahmed will build the embedding and vector-store pipeline for the TechStore policy and product documents. Keep Bitext examples in the labeled intent/evaluation dataset unless the team explicitly defines a separate, non-authoritative example retriever.

Ahmed will:

- Configure the agreed embedding model.
- Create and persist the vector store from the processed chunks.
- Implement a retriever that accepts a query and returns ranked documents.
- Make result count and persistence location configurable.
- Return a clear empty-result outcome when no useful documents are found.

Abdullah will check returned documents against the sample questions; Mohamed will integrate the retriever interface with the graph.

#### Small tasks and checkpoints

- [ ] **Seif:** Review the model and storage choices and confirm the team can run them with its available resources.
- [ ] **Mohamed:** Lead embedding and vector-retrieval implementation; compare results for customer questions against the TechStore knowledge base.
- [ ] **Abdullah:** Prepare labeled Bitext examples and check that retrieved TechStore policies support the expected answer.
- [ ] **Ahmed:** Configure embedding settings, persist the index, and handle empty-result responses.
- [ ] **Everyone:** Test index creation and retrieval before connecting the full graph.

**Checkpoint:** Sample questions return relevant chunks with source metadata.

### Task 8: Implement intent routing in LangGraph

**Coordinator: Seif**
**Collaborators: Mohamed, Abdullah, Ahmed**

Mohamed will implement the conditional graph logic, with Seif coordinating scope and review. Route requests through these paths:

- TechStore policy or product questions that need knowledge-base retrieval.
- Customer-support intents such as order management, payments, delivery, refunds, and account management; use Bitext labels as training/testing examples, then retrieve the matching TechStore policy when an answer needs company information.
- General conversational questions that do not need policy retrieval.
- Out-of-scope requests, unresolved complaints, policy exceptions, or cases with insufficient evidence that should receive a safe response or human escalation.

Mohamed reviews graph state and edges; Abdullah provides examples for the retrieval route; Ahmed checks the conversation and out-of-scope behavior. The route decision and route history must be available to the UI.
Abdullah provides labeled Bitext cases and verifies expected intents; Ahmed verifies conversation, out-of-scope, and escalation status behavior. Seif coordinates review. The route decision and route history must be available to the UI.

#### Small tasks and checkpoints

- [ ] **Seif:** Coordinate the routing requirements and confirm the three expected branches.
- [ ] **Mohamed:** Implement and review the LangGraph state updates and conditional edges, prioritizing the retrieval path.
- [ ] **Abdullah:** Use Bitext intent/category examples to create customer queries and verify that the expected TechStore intents route correctly.
- [ ] **Ahmed:** Write general and out-of-scope examples and define the route/status values the UI will show.
- [ ] **Everyone:** Review and test branch examples; Seif records the agreed acceptance results.

**Checkpoint:** Every sample input reaches its expected branch and the selected route is available in graph output.

### Task 9: Connect retrieval, answer generation, and source display

**Lead: Mohamed**  
**Collaborators: Abdullah, Ahmed, Seif**

Mohamed will connect the retrieval branch to the TechStore vector store and add the generation step that answers from retrieved policy or product context. The answer must not invent a company rule or treat a Bitext response as TechStore policy. Carry source documents through to the graph output. Abdullah checks policy grounding; Ahmed checks UI compatibility; Seif reviews the integrated workflow.

#### Small tasks and checkpoints

- [ ] **Seif:** Coordinate integration and check the end-to-end path on a sample TechStore customer question.
- [ ] **Mohamed:** Lead the core RAG implementation: retrieve relevant TechStore policies or product details, pass evidence to generation, and return source metadata.
- [ ] **Abdullah:** Verify that generated order, delivery, refund, or warranty details match the fictional TechStore policies.
- [ ] **Ahmed:** Connect the LLM call and make sure answers and citations fit the UI result format.
- [ ] **Everyone:** Test that the answer is grounded in retrieved TechStore documents and sources are visible.

**Checkpoint:** A domain query produces an answer and the evidence used to generate it.

### Task 10: Add evaluation, query refinement, and retry limits

**Lead: Abdullah**  
**Collaborators: Mohamed, Ahmed, Seif**

Abdullah will lead the quality loop:

- Evaluate whether retrieved documents are relevant and whether the draft answer is supported by them.
- Record an evaluation result and a reason, not just a pass/fail value.
- When quality is too low, produce a refined search query and retrieve again.
- Increment and preserve a retry counter.
- Stop retrying when the configured maximum is reached and return a clear result or request human help.

Mohamed reviews the graph cycle and stop condition; Ahmed tests the displayed retry details; Seif reviews failure behavior.

#### Small tasks and checkpoints

- [ ] **Seif:** Agree on the quality threshold and maximum retry count with the team.
- [ ] **Mohamed:** Connect evaluator outcomes to the LangGraph cycle and ensure the retry limit always stops the loop.
- [ ] **Abdullah:** Define retrieval and grounded-answer checks, build evaluation examples, and inspect evaluator results.
- [ ] **Ahmed:** Implement or tune the query-refinement prompt and expose retry count/details to the UI.
- [ ] **Everyone:** Test accepted answers, failed retrieval, a refinement retry, and maximum-retry termination.

**Checkpoint:** The workflow recovers from weak retrieval when possible and always stops at the configured limit.

### Task 11: Integrate the LLM client and prompts

**Lead: Ahmed**  
**Collaborators: Abdullah, Mohamed, Seif**

Ahmed will implement the shared model client and prompts for routing (if model-assisted), answer generation, evaluation, query refinement, conversation, and out-of-scope handling. API keys must load from environment variables, errors must be understandable, and prompts should require evidence-based answers for retrieved content.

Abdullah reviews the evaluation and refinement prompts; Mohamed reviews the prompts' expected graph inputs and outputs; Seif reviews configuration and secret handling.

#### Small tasks and checkpoints

- [ ] **Seif:** Review API-key handling and ensure no credentials are committed.
- [ ] **Mohamed:** Specify the graph inputs/outputs and refine the evidence-grounded RAG answer prompt.
- [ ] **Abdullah:** Review evaluation and refinement prompts against Bitext intent examples and expected TechStore policy sources.
- [ ] **Ahmed:** Implement the shared LLM client, environment configuration, and prompts for routing, generation, evaluation, refinement, and conversation.
- [ ] **Everyone:** Test representative prompts and document errors or inconsistent outputs.

**Checkpoint:** Model calls use the shared client, prompt outputs fit the graph, and configuration contains no real credentials.

### Task 12: Add human-in-the-loop pause and resume

**Lead: Mohamed**  
**Collaborators: Ahmed, Seif, Abdullah**

Mohamed will integrate LangGraph checkpointing and interrupts for cases requiring human support, such as unresolved complaints, policy exceptions, or answers with insufficient evidence. The workflow must retain enough state to resume. Ahmed wires the approve, reject, edit, or clarify actions; Abdullah supplies support cases that should escalate; Seif tests pause and resume.

#### Small tasks and checkpoints

- [ ] **Seif:** Coordinate the review policy and lead the end-to-end pause/resume acceptance check.
- [ ] **Mohamed:** Implement LangGraph checkpointing and interrupt/resume behavior in the workflow.
- [ ] **Abdullah:** Provide an unresolved complaint, policy-exception, or low-evidence support example that should trigger human review.
- [ ] **Ahmed:** Add the UI actions for approval, rejection, edits, or clarification and show pending status.
- [ ] **Everyone:** Test approval and at least one change/rejection path, confirming state is preserved.

**Checkpoint:** A reviewer can act on the paused graph and the graph resumes with the correct state.

### Task 13: Build the interactive interface

**Lead: Ahmed**  
**Collaborators: Mohamed, Seif, Abdullah**

Ahmed will build the UI using the team's chosen framework. It must allow customers to submit a TechStore support question and show:

- The selected route and current workflow status.
- The answer and its source documents.
- Evaluation details, refined queries, and retry count.
- The pending HITL review state and controls to approve, reject, edit, or clarify.
- The final outcome after resumption.

Mohamed supplies the graph interface; Seif tests the integrated application; Abdullah checks that sources and evaluation information are clear.

#### Small tasks and checkpoints

- [ ] **Seif:** Review the interface against the assignment requirements and coordinate a complete demo.
- [ ] **Mohamed:** Provide and integrate the graph invocation/result interface, including route history and resume state.
- [ ] **Abdullah:** Check that product/order details, policy citations, and evaluation messages are understandable and accurate.
- [ ] **Ahmed:** Build the query input, answer/source views, progress details, and HITL controls.
- [ ] **Everyone:** Run domain, conversation, out-of-scope, retry, and HITL examples through the UI.
- [ ] **Everyone:** Run product/policy, customer-support intent, general conversation, out-of-scope, retry, and human-escalation examples through the UI.

**Checkpoint:** A user can see and complete each required workflow from the interface.

### Task 14: Create the evaluation set and measure results

**Lead: Abdullah**  
**Collaborators: Seif, Mohamed, Ahmed**

Abdullah will organize a repeatable test set with expected routes, relevant TechStore policy sources, and expected answer facts. Use held-out Bitext instructions and labels for intent/routing tests, plus team-written cases for TechStore policy answers, insufficient evidence, and escalation. Do not treat Bitext's synthetic response as the authoritative answer when testing policy-grounded responses. The team will run tests and report:

- Route accuracy.
- Retrieval relevance.
- Answer faithfulness and groundedness.
- Retry frequency and HITL completion.
- Latency for each route and for the complete workflow.

The report must state how metrics were calculated and note any manual judgments. Do not present unmeasured or fabricated results.

#### Small tasks and checkpoints

- [ ] **Seif:** Review the evaluation plan and ensure report conclusions match the recorded measurements.
- [ ] **Mohamed:** Run retrieval and graph-route tests and help diagnose low-quality results.
- [ ] **Abdullah:** Lead the test set, expected evidence, metric definitions, and relevance/groundedness assessment.
- [ ] **Ahmed:** Record results in a reproducible format and prepare tables or screenshots for the report.
- [ ] **Everyone:** Run assigned cases and report actual results, including failures and latency.

**Checkpoint:** Results can be reproduced and no metric or result is invented.

### Task 15: Integrate, test, and stabilize the complete system

**Coordinator: Seif (team leader)**
**Collaborators: Abdullah, Mohamed, Ahmed**

Seif will coordinate an end-to-end integration pass. Every member will test the branch they implemented and at least one branch implemented by another member. The team will:

- Run the automated tests and fix project-related failures.
- Check a clean setup using only the README and `.env.example`.
- Test missing documents, empty retrieval, model/API errors, retry limit, and resume behavior.
- Check that no secrets, private data, or unnecessary generated files were committed.
- Review pull requests; Seif merges approved work into `main`.

#### Small tasks and checkpoints

- [ ] **Seif:** Coordinate integration order, review completion, and merge approved pull requests.
- [ ] **Mohamed:** Test and fix graph, retrieval, and retry integration issues.
- [ ] **Abdullah:** Test ingestion, metadata, source accuracy, and evaluation behavior.
- [ ] **Ahmed:** Test configuration, model errors, interface behavior, and HITL controls.
- [ ] **Everyone:** Review a teammate's pull request, run the full tests, and record remaining limitations.

**Checkpoint:** The integrated app starts cleanly and the required branches and failure paths have been tested.

### Task 16: Complete the README, screenshots, and technical report

**Lead: Ahmed**  
**Collaborators: Seif, Abdullah, Mohamed**

Ahmed will assemble the README and coordinate screenshots and report formatting. Each member supplies and verifies their own contribution:

- **Seif:** project motivation, final architecture rationale, integration process, and team contribution matrix.
- **Abdullah:** TechStore document sources and authorship, Bitext dataset and its synthetic-data limitation, ingestion/evaluation methods, measured results, and interpretation.
- **Mohamed:** LangGraph state, nodes, conditional edges, retry cycle, HITL checkpoint design, and technical challenges.
- **Ahmed:** UI design and screenshots, setup instructions, README, report formatting, limitations, and future work (with team review).

The report must include the title page, team contribution matrix, problem/domain motivation, system architecture and workflow diagram, technical challenges and solutions, quantitative and qualitative evaluation, screenshots of routing/refinement/HITL, limitations, and future work.

#### Small tasks and checkpoints

- [ ] **Seif:** Write the project motivation, coordinate the contribution matrix, and review final consistency.
- [ ] **Mohamed:** Write the RAG architecture, LangGraph state/routes, retrieval/refinement loop, and workflow diagram explanation.
- [ ] **Abdullah:** Write the TechStore knowledge-base and Bitext dataset sections, clearly explain that Bitext is synthetic and not the policy authority, and report evaluation methods and measured results.
- [ ] **Ahmed:** Assemble the README, setup instructions, UI screenshots, HITL walkthrough, limitations, and future work.
- [ ] **Everyone:** Review the section describing their work and verify the report against the assignment requirements.

**Checkpoint:** README is runnable as written and the report has all required sections and evidence.

### Task 17: Final review and submission

**Coordinator: Seif (team leader)**
**Collaborators: Abdullah, Mohamed, Ahmed**

Seif will lead a final checklist review with all members. Confirm that:

- The repository link and submitted report are correct.
- The source code is modular and includes ingestion, graph state, nodes, edges, prompts, and UI.
- `.env.example`, setup instructions, sample queries, and architecture diagrams are present.
- The application demonstrates all routing branches, refinement, and HITL pause/resume.
- Evaluation metrics and limitations are reported honestly.
- The contribution matrix matches actual work and GitHub history.
- Each member has personal commits, pull requests, and code reviews visible in GitHub.

#### Small tasks and checkpoints

- [ ] **Seif:** Lead the final checklist, confirm the repository/report submission, and verify team contributions are represented fairly.
- [ ] **Mohamed:** Demonstrate the complete RAG graph, including retrieval, refinement, retry limit, and source-grounded response.
- [ ] **Abdullah:** Verify the data sources, sample cases, evaluation results, and report figures.
- [ ] **Ahmed:** Verify setup instructions, `.env.example`, UI screenshots, and HITL pause/resume demonstration.
- [ ] **Everyone:** Confirm personal commits, pull requests, reviews, and remaining limitations before submission.

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
- [ ] Tests cover the important branches and failure cases.

### Repository and Report

- [ ] README contains architecture, setup, environment, and sample queries.
- [ ] `.env.example` contains placeholders only; no secrets are committed.
- [ ] Results include relevance, faithfulness, groundedness, and latency measures.
- [ ] Screenshots show conditional routing, self-correction, and HITL.
- [ ] The report contains the contribution matrix, challenges, results, limitations, and future work.
- [ ] All four members have verifiable personal commits, pull requests, and reviews.
