# AI Model Selection Strategy

In an interview, "Which model?" is never a single answer. It depends on **Latency**, **Cost**, **Privacy**, and **Capability**.

## 1. The Big Three (Closed Source)

| Model | Best For | Pros | Cons |
| :--- | :--- | :--- | :--- |
| **GPT-4o (OpenAI)** | **Complex Reasoning / Orchestration** | The best "General Manager". Handles complex instructions, JSON formatting, and function calling better than most. | Expensive. Data privacy concerns for some enterprises. |
| **Claude 3.5 Sonnet (Anthropic)** | **Coding / Writing** | Currently widely regarded as the SOTA (State of the Art) for code generation. Less "lazy" than GPT-4. Large context window (200k). | Tool calling ecosystem is slightly less mature than OpenAI's. |
| **Gemini 1.5 Pro (Google)** | **Massive Context RAG** | **2 Million Token Context.** You can dump entire books or codebases into the prompt without RAG. | varying reasoning performance compared to GPT-4o on short tasks. |

## 2. Open Source / Local Models

| Model | Best For | Why use it? |
| :--- | :--- | :--- |
| **Llama 3 (Meta) - 8B** | **Edge Devices / Speed** | Fast, runs on a laptop. Great for simple classification or chat bots. | Not smart enough for complex logic. |
| **Llama 3 (Meta) - 70B** | **Privacy / Enterprise** | Approaches GPT-4 level intelligence but can be hosted within your own VPC (Virtual Private Cloud). | Requires significant GPU hardware to host. |
| **Mistral Large** | **European Compliance** | Strong performance, good for EU GDPR compliance scenarios. | |

## 3. Decision Matrix (Cheat Sheet)

1.  **"We need the code to be absolutely perfect."**
    *   **Choice:** Claude 3.5 Sonnet.
2.  **"We need to summarize 100 PDFs at once."**
    *   **Choice:** Gemini 1.5 Pro (Long Context).
3.  **"We cannot send data to the cloud (Banking/Healthcare)."**
    *   **Choice:** Llama 3 70B (Hosted via vLLM or Ollama on-prem).
4.  **"We need a generic chatbot agent to use many tools."**
    *   **Choice:** GPT-4o (Best tool-following).

## 4. Fine-Tuning vs. RAG
*   **RAG (Retrieval Augmented Generation):** Use when you need the model to know *new* facts (e.g., today's stock price, your company's private documents).
*   **Fine-Tuning:** Use when you need the model to learn a *style* or a specific *format* (e.g., speaking like a pirate, outputting a specific JSON structure consistently).
*   *Interview Tip:* 90% of the time, the answer is RAG. Fine-tuning is expensive and hard to maintain.
