# System Design Scenario: AI-Driven Legacy Modernization (COBOL to Java)

## 1. The Scenario
**Objective:** Design an automated pipeline that ingests a massive, monolithic COBOL banking application and refactors it into clean, idiomatic Java Spring Boot microservices.

**Why this is a hard interview question:**
*   **Context:** Codebases are larger than any LLM context window.
*   **Accuracy:** Hallucinations in banking logic are unacceptable.
*   **Dependencies:** Changing one COBOL paragraph might break a variable used 5000 lines away.
*   **Evaluation:** How do you *know* the new code works?

---

## 2. High-Level Architecture (The "Agentic" Workflow)

We do not simply paste code into ChatGPT. We build an **Agentic Pipeline** using **LangGraph**.

### Phase 1: Knowledge Ingestion (The "RAG" Layer)
Before we write code, we must map the territory.
1.  **Code Parsing:** Use a COBOL grammar parser to split files into "Paragraphs" or "Sections".
2.  **Hybrid Indexing (Crucial for Interview):**
    *   **Vector Store (Pinecone/Milvus):** Stores *semantic* meaning (e.g., "Calculate Interest Logic").
    *   **Elasticsearch:** Stores *exact* tokens (variable names like `WS-ACC-BAL`, `PROC-DIV`). *Why? Vectors are bad at exact variable name matching; Keyword search is King here.*
    *   **Knowledge Graph (Neo4j):** Maps dependencies. `Paragraph A` CALLS `Paragraph B` which UPDATES `Table C`.
    *   **Summary:** We use **GraphRAG** (Graph-augmented Retrieval) to understand the flow, not just the text.

### Phase 2: The Agentic Workflow (LangGraph)
We design a state machine with cyclic graphs (loops), not a linear DAG.

*   **Agent A (The Archaeologist):**
    *   *Role:* Understands the COBOL.
    *   *Tool:* Queries the Knowledge Graph to find all side effects of a specific variable.
    *   *Prompt:* "Explain the business logic of this paragraph, considering it modifies global variable `X` found in copybook `Y`."
*   **Agent B (The Architect):**
    *   *Role:* Plans the Java structure.
    *   *Output:* "Create a `LoanService` class with a `calculateInterest` method."
*   **Agent C (The Developer):**
    *   *Role:* Writes the Java code.
    *   *Model:* **Claude 3.5 Sonnet** (Currently SOTA for coding tasks).
*   **Agent D (The QA/Compiler):**
    *   *Role:* Tries to compile the code.
    *   *Action:* If error -> Pass error back to Agent C (Self-Correction Loop).

---

## 3. Technology Deep Dive

### A. The "Brain": Model Selection
*   **Orchestrator:** **GPT-4o** (High reasoning, follows complex instructions).
*   **Coder:** **Claude 3.5 Sonnet** (Superior syntax handling, less verbose).
*   **Cost Saver:** **Llama 3 (70B)** for summarization tasks (running locally or cheaper API) to save tokens.

### B. Retrieval: Why Elasticsearch + Vector?
If the user searches "Logic for updating account", the **Vector DB** finds the relevant paragraph.
If the agent needs "Every reference to variable `WS-TAX-RATE`", **Elasticsearch** is required for precise retrieval.
**Interview Tip:** Always suggest **Hybrid Search** (Reciprocal Rank Fusion) for codebases.

### C. Evaluation: LangSmith
How do we trust the bot?
1.  **Tracing:** Use LangSmith to visualize the chain. Why did the agent decide to split this class?
2.  **Regression Testing:** Run a dataset of 50 known COBOL snippets. Does the agent consistently produce valid Java?
3.  **Human-in-the-loop:** The agent pauses and asks a human: "I am about to rename this critical table column. Approve?"

---

## 4. Key Interview Concepts to Mention

*   **Context Window Stuffing vs. RAG:** We can't stuff 2 million lines of COBOL into a prompt. We must use RAG to fetch only the *relevant* dependency tree.
*   **Chain-of-Thought (CoT):** Force the agent to explain the COBOL logic *before* writing the Java code.
*   **Hallucination Guardrails:** Use a "Compiler Tool". If the generated Java doesn't compile, the agent must fix it before showing the user.
