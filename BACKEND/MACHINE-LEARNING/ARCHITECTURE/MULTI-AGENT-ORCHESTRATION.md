# Multi-Agent Orchestration & Patterns

In advanced system design, a single agent often isn't enough. We use **Multi-Agent Systems (MAS)** where specialized agents collaborate. This is a critical interview topic for 2024/2025.

## 1. Why Multi-Agent? (The "Divide and Conquer" Principle)
Instead of one massive prompt ("You are an expert Coder, QA, and Architect..."), we split responsibilities.
*   **Context Efficiency:** Each agent only sees the context relevant to its sub-task.
*   **Role Specialization:** You can use different models for different agents (e.g., **Claude 3.5 Sonnet** for coding, **Llama-3-70B** for summarizing).
*   **Reliability:** Easier to debug "Why did the QA fail?" than "Why did the Mega-Bot fail?".

---

## 2. Core Orchestration Patterns

### A. The Supervisor Pattern (Hub and Spoke)
A central "Manager" agent decides which "Worker" agent should act next.

*   **Flow:**
    1.  User: "Fix the bug in `login.py`."
    2.  **Supervisor:** Analyzes request. Decides: "Send to Researcher."
    3.  **Researcher Agent:** Finds the bug location. Returns info to Supervisor.
    4.  **Supervisor:** Decides: "Send to Coder."
    5.  **Coder Agent:** Fixes code. Returns to Supervisor.
    6.  **Supervisor:** Decides: "FINISH."
*   **Implementation:** In LangGraph, the Supervisor is a node that outputs the *name* of the next node (conditional routing).

### B. Hierarchical Teams (The Org Chart)
Agents managing agents in a tree structure.
*   **Top Level:** VP of Engineering Agent (High-level plan).
*   **Mid Level:** Backend Lead Agent vs. Frontend Lead Agent.
*   **Leaf Level:** SQL Writer Agent, CSS Designer Agent.
*   **Use Case:** Large scale projects where a single Supervisor would get overwhelmed by the context of every single file change.

### C. Sequential Handoffs (The Assembly Line)
Linear progression where Agent A explicitly passes control to Agent B.
*   **Flow:** `Spec Writer` -> `Code Generator` -> `Unit Test Generator` -> `Reviewer`.
*   **Difference from Supervisor:** No central brain. The "Code Generator" knows its *only* job is to pass output to "Unit Test Generator".

### D. Joint Collaboration (The Chat Room / AutoGen Style)
Agents discuss among themselves without a strict router.
*   **Scenario:** A "Developer Agent" and a "User Proxy Agent" chat.
    *   Dev: "I fixed it."
    *   UserProxy: "I ran it, got Error 500."
    *   Dev: "Ah, try this."
*   **Pros:** Very creative.
*   **Cons:** Hard to control; they can get stuck in infinite loops complimenting each other.

---

## 3. Implementation Details (LangGraph)

How do we actually code a "Sub-Agent"?

### Shared State (The "Blackboard")
All agents read/write to a shared state object.
```typescript
interface TeamState {
  messages: BaseMessage[];
  code_snippet: string;
  errors: string[];
  current_agent: string;
}
```

### The Router (The Brain)
The Supervisor is just an LLM call with a specific tool constraint.
*   **Prompt:** "You are the manager. Given the conversation above, who should act next? Options: [Researcher, Coder, Reviewer, FINISH]."
*   **Output:** `{"next": "Coder"}`.
*   **Logic:** The graph executes the node matching the string "Coder".

---

## 4. Advanced: Debate & Consensus
How do we improve accuracy? **Let agents argue.**
*   **Scenario:** We need to modernize a critical COBOL calculation.
*   **Agent A (Optimist):** Suggests a refactor for speed.
*   **Agent B (Pessimist):** critiques Agent A's code for safety violations.
*   **Agent C (Judge):** Reviews the arguments and synthesizes the final answer.
*   *Research indicates this "Multi-Persona" approach significantly reduces hallucinations.*

## 5. Summary Table for Interviews

| Pattern | Best For | Complexity | Tool Examples |
| :--- | :--- | :--- | :--- |
| **Supervisor** | General purpose tasks with tool selection | Medium | LangGraph, CrewAI |
| **Hierarchical** | Massive, complex projects | High | LangGraph |
| **Sequential** | Pipelines (CI/CD, Content Gen) | Low | LangChain Chains |
| **Collaboration** | Creative writing, open-ended solving | High (Unstable) | Microsoft AutoGen |
