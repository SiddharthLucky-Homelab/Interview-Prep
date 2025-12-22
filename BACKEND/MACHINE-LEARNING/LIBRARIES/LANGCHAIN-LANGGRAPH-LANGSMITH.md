# Agents, Orchestration & MLOps

## 1. What is an Agent?
An **Agent** is an LLM given access to **Tools** and a **Loop**.
*   **Prompt:** "You are a researcher. You have a search tool."
*   **Loop:**
    1.  Thought: "I need to find the weather."
    2.  Action: Call `get_weather("London")`.
    3.  Observation: "15°C, Raining".
    4.  Final Answer: "It is raining in London."

## 2. Frameworks: LangChain vs. LangGraph

### LangChain (The Primitives)
*   Great for simple chains: `Prompt -> LLM -> Output`.
*   Good for "Chat with PDF".
*   **Legacy Issues:** Became bloated and hard to debug for complex loops.

### LangGraph (The Orchestrator)
*   **State Machines:** Defines flows as Graphs (Nodes and Edges).
*   **Cyclic:** Allows loops (e.g., Code -> Test -> Fail -> Fix Code -> Test...).
*   **State:** Keeps track of the "memory" across steps explicitly.
*   **Best for:** Complex enterprise agents (like our COBOL Modernizer).

## 3. Evaluation: LangSmith
Building the bot is easy. **Knowing it works is hard.**

**LangSmith** provides:
*   **Tracing:** See exactly what the LLM inputted and outputted at every step of the chain.
*   **Playgrounds:** Tweak a prompt in the UI and re-run the trace to see if it improves.
*   **Datasets:** Store "Golden Q&A Pairs". Run your bot against 100 questions to calculate an accuracy score (e.g., "85% Correct").

## 4. Concepts for Interview

*   **ReAct Pattern:** **Re**asoning + **Act**ing. The standard pattern for agents (Think -> Act -> Observe).
*   **Tool Use (Function Calling):** The model doesn't "run" code. It outputs a JSON like `{"tool": "calculator", "args": "5 + 5"}`. Your Python code parses this, runs `5+5`, and feeds `10` back to the LLM.
*   **Human-in-the-Loop:** Designing breakpoints in LangGraph where the agent pauses for user approval before executing a dangerous action (like `DROP TABLE`).
