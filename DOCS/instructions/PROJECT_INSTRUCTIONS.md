# TechStore Project Instructions

Use this file to record decisions and instructions that the whole team should follow. Add new items under the most relevant heading or in **Additional Team Instructions**.

## Project Overview

Build an Agentic Retrieval-Augmented Generation (RAG) customer-support assistant for **TechStore**, a fictional online electronics store. The system uses Python, LangChain, and LangGraph to answer customer questions from TechStore policies, FAQs, product information, and troubleshooting guidance.

## Mandatory Development Instructions

These rules apply throughout the project to every contributor and every code change:

- Act as a senior AI engineer specializing in Agentic RAG, LLMs, LangGraph, and backend development.
- Understand the existing repository and the relevant implementation before modifying anything.
- Never rewrite a working component without a clear, documented reason.
- Follow a modular, clean, and maintainable architecture.
- Avoid adding dependencies unless they are necessary for the requirements.
- Use environment variables for API keys and sensitive configuration; never commit secrets.
- Explain significant architectural decisions and their tradeoffs in the project documentation or pull request.
- Test changes before claiming a feature works; do not assume correctness without verification.
- Keep implementation aligned with the actual project requirements and the TechStore scope.

## Mandatory Security and Customer Privacy

These requirements apply throughout development and in the finished customer-support system:

- Never expose API keys, credentials, or other secrets; never hardcode them.
- Validate and constrain user inputs before processing them.
- Defend against prompt injection in customer queries and retrieved documents.
- Treat retrieved documents as untrusted data, not executable instructions; retrieved content must not override system or application rules.
- Prevent customers from accessing internal company documents or data they are not authorized to see.
- Apply role-based access control when the application has different user or staff permissions.
- Collect and store only the personal customer information needed for the task.
- Mask or omit sensitive information in application logs and error reports.
- Do not use real customer personal information in development or evaluation datasets; use synthetic or properly anonymized data.

## Code Quality and Development Workflow

These rules are mandatory for all code changes:

1. Inspect relevant files and repository structure before editing.
2. Work on one component at a time.
3. Explain the purpose of significant changes.
4. Do not modify unrelated files.
5. Follow existing naming conventions.
6. Use type hints and meaningful variable names.
7. Add comments where complex logic needs explanation.
8. Handle exceptions properly; do not hide errors to make the application appear functional.
9. Remove unused imports and dead code when they are within the scope of the change.
10. Run relevant tests after every major change and report their actual results.
11. Update documentation when changing architecture.
12. After completing and verifying a task or subtask, mark its checkbox in `DOCS/planning/TEAM_TASK_DIVISION.md`. Do not mark incomplete work complete or check off another member's task without that member's confirmation.

## Documentation Requirements

Maintain the following project documentation as the system evolves:

- `README.md`: installation, setup, configuration, and run instructions.
- Architecture documentation and system workflow diagrams.
- API documentation for application interfaces and graph inputs/outputs.
- Knowledge-base ingestion documentation, including accepted formats and metadata.
- Environment variable documentation that never reveals secret values.
- Testing and evaluation documentation, including methods and actual results.
- A record of important architectural decisions and their tradeoffs.

## Critical Engineering Correctness Rules

Prioritize engineering correctness over implementation speed. Do not:

- Claim a feature is implemented when it is not.
- Substitute mock responses for real LLM or retrieval behavior without clearly labeling them as mocks.
- Hardcode answers to expected test questions.
- Fabricate retrieval results, evaluation scores, or test outcomes.
- Ignore errors or silently bypass failed components.
- Introduce unnecessary complexity or dependencies.
- Change architecture without explaining why.
- Mark incomplete functionality as complete.

Always:

- Verify each component independently and verify integration between components.
- Use actual TechStore knowledge-base retrieval for policy and product answers.
- Maintain traceability between generated answers and their source documents.
- Report implementation limitations honestly and explain important tradeoffs.
- Keep the system modular and extensible.
- Prioritize reproducibility and measurable evaluation.
- When a requirement is unclear, inspect the existing code and documentation first. If it remains unresolved, explain the ambiguity before making an assumption.

## Development Task Completion Report

At the end of each development task, report:

1. What was implemented.
2. Which files were created or modified.
3. How the implementation works.
4. Tests performed and their actual results.
5. Known limitations.
6. Recommended next steps.

Do not state that tests passed unless they were run. If a check could not be run, say why.

## Team

- **Seif:** Team leader. Coordinates meetings, decisions, progress, and integration.
- **Mohamed:** Priority owner for core RAG, retrieval, and LangGraph workflow implementation.
- **Abdullah:** Contributes to dataset preparation, policy content, retrieval testing, and evaluation.
- **Ahmed:** Contributes to model integration, product and payment content, HITL, interface, setup, and testing.

All members should contribute code, review teammates' work, test across modules, and use personal GitHub accounts for commits and pull requests. See **Mandatory GitHub Workflow** below.

## Data Sources

### TechStore Knowledge Base

The team writes these fictional documents. Treat them as the authoritative source for TechStore rules and product answers:

1. Return Policy
2. Refund Policy
3. Shipping Policy
4. Warranty Policy
5. Payment Policy
6. Order Management
7. Customer FAQ
8. Escalation Policy
9. Product Catalog
10. Troubleshooting Guide

Keep policy rules consistent across documents. Identify them as fictional TechStore content and include useful metadata such as document title, section, version, and source type.

### Bitext Customer Support Dataset

Source: [Bitext Customer Support Dataset](https://huggingface.co/datasets/bitext/Bitext-customer-support-llm-chatbot-training-dataset)

Use the dataset's `instruction`, `category`, `intent`, `response`, and `flags` fields for synthetic intent examples, conditional-routing tests, and evaluation cases.

**Important:** The dataset is synthetic. Do not describe it as genuine TechStore customer history or use its responses as the authority for TechStore policies. If a Bitext response conflicts with the fictional TechStore knowledge base, the TechStore document is authoritative.

Verify the dataset's license and usage conditions before redistributing it.

Known data issues (measured on the committed CSV: 26,872 records, 27 intents, 11 categories):

- About 13,000 responses and 6,700 instructions contain unfilled `{{placeholders}}` such as `{{Order Number}}`. Clean or mask them before use in prompts or evaluation; never present them as TechStore values.
- 2,237 instructions are duplicates. Deduplicate before creating any train/held-out split so test cases do not leak.
- The `flags` field uses undocumented letter codes; decode and document them before relying on them.
- The dataset has no product, warranty, or troubleshooting examples. Write those routing and evaluation cases by hand.
- Seif has confirmed the license allows redistribution, so the committed CSV stays. Abdullah records the exact license name, its conditions, and the required credit line in Task 2.

## Required System Behavior

- Route TechStore product and policy questions to retrieval.
- Handle customer-support intents such as order management, payment, delivery, refunds, and account management.
- Handle general conversation without unnecessary retrieval.
- Handle out-of-scope requests safely.
- Evaluate retrieved evidence and generated answers.
- Refine the query and retry retrieval when evidence or answer quality is insufficient; enforce a maximum retry count.
- Escalate unresolved complaints, policy exceptions, and cases with insufficient evidence to human support.
- Use LangGraph checkpointing and interrupts so human review can pause and resume execution.
- Show routes, cited sources, refinement attempts, retry counts, and human-review status in the UI.

## Answer and Source Rules

- Base company-policy and product answers on retrieved TechStore documents.
- Do not invent policies, prices, product specifications, availability, or order details.
- If the knowledge base does not contain enough evidence, say so and follow the escalation policy.
- Show the source document and relevant section when possible.
- Keep the difference between synthetic test data and authoritative fictional company policy clear.

## Mandatory GitHub Workflow

Source: Course Project Specification, Section 2 (personal GitHub accounts; grades based on commit history, pull requests, code reviews and verifiable contributions; single-uploader rule). The steps below are team policy built on it. They apply to EVERY task, including documentation changes.

### Required steps

1. Read all project instruction files before starting (`CLAUDE.md`, this file, `DOCS/planning/TEAM_TASK_DIVISION.md`, `README.md`).
2. Create a separate feature branch for each task, from `main`. Never work directly on `main`.
   Names: `feat/<member>-<topic>`, `docs/<member>-<topic>`, `test/<member>-<topic>`.
3. With approval, pull the latest `main` locally, then create the branch from it.
4. Implement the task and commit. Commits use the responsible member's personal GitHub account (see "Commits made on behalf of a teammate").
5. Push the feature branch to the shared repository.
6. Open a Pull Request for review.
7. Teammates review the code and give feedback.
8. Address review comments and obtain approval.
9. Seif merges, only after approval. Never merge without the review process.

### Approvals

- Seif merges all PRs.
- PRs by other members need Seif's approval before merging.
- Seif's own PRs do not need another teammate's approval. They still follow the full branch, commit, push and PR flow, and teammates may review and comment. Seif addresses any feedback before merging.
- Claude/AI assistants must ask for explicit approval before every state-changing action: file edits, commits, pull, push, PR creation and merge. Approval for one action does not carry over to the next. Read-only inspection needs no approval.

### Commits made on behalf of a teammate

Seif may commit work for another member. Both the commit message and the PR description must state this openly, for example:
`feat(ui): add query input — Ahmed's Task 13, done by Seif`
Never hide the real author or imply the teammate made the commit. Such work counts as Seif's contribution in GitHub history, and the contribution matrix must record it accurately.

### Mandatory rules

- Every member must have their own commits and verifiable contributions.
- Maintain individual history through commits, PRs and code reviews.
- Never use one account to upload everyone's work.
- Never overwrite another member's changes.
- Never skip the branch, commit, push or PR workflow.
- If anything is unclear or conflicts with these instructions, stop and ask.
- Include tests or clear manual verification steps in each PR.
- Keep API keys and other secrets out of the repository. Use `.env.example` with placeholders.
- Task checkbox updates (rule 12 above) also go through a branch and PR.
- Keep the contribution matrix accurate to the work actually completed.

