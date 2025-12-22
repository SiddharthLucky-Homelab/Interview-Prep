# RAG Implementation & Architecture

**RAG (Retrieval-Augmented Generation)** is the process of fetching relevant data and giving it to the LLM so it doesn't hallucinate.

## 1. The RAG Pipeline
1.  **Ingestion:** Load Documents -> Chunking (Split into pieces).
2.  **Embedding:** Turn text into Vectors (Lists of numbers) using models like `text-embedding-3-small` or `Cohere`.
3.  **Storage:** Save vectors in a Vector DB.
4.  **Retrieval:** User Query -> Vector -> Find similar Vectors.
5.  **Generation:** Context + Query -> LLM -> Answer.

## 2. Chunking Strategies (Critical Interview Topic)
*   **Fixed Size:** "Every 500 characters". (Bad: cuts sentences in half).
*   **Recursive Character Split:** Split by Paragraph, then Sentence, then Word. (Standard).
*   **Semantic Chunking:** Use an AI to decide when a "topic" changes and split there. (Best quality, slower).
*   **Agentic Chunking:** An agent reads the document and writes summaries to be indexed.

## 3. Database Selection

| Type | Examples | Best For |
| :--- | :--- | :--- |
| **Specialized Vector DBs** | **Pinecone, Weaviate, Milvus, Qdrant** | Pure speed, advanced filtering, built-for-purpose. Best for massive scale. |
| **Vector-Enabled SQL** | **pgvector (PostgreSQL)** | Best if you already have Postgres. Keeps relational data and vectors together. "Good enough" for 90% of apps. |
| **Search Engines** | **Elasticsearch / OpenSearch** | **Hybrid Search.** The King of text search. Combines BM25 (Keyword) + KNN (Vector). |

## 4. Advanced RAG Patterns

### A. Hybrid Search (The Gold Standard)
*   **Problem:** Vector search matches *meaning*, but misses *keywords*. (e.g., Searching for "Error 503" might match "Server Issue" but fail to find the specific log line "Error 503").
*   **Solution:** Run **BM25** (Keyword Search) AND **Vector Search**. Combine results using **RRF (Reciprocal Rank Fusion)**.

### B. GraphRAG (The New Hotness)
*   **Problem:** Standard RAG treats chunks as isolated islands. It misses global connections.
*   **Solution:** Use a Knowledge Graph (Neo4j). Connect chunks: `Chunk A` --(references)--> `Chunk B`.
*   **Use Case:** "Summarize the relationship between Character X and Character Y across this entire book series." Standard RAG fails; GraphRAG walks the edges to find the connection.

### C. Self-Reflective RAG
1.  Retrieved docs.
2.  LLM checks: "Do these docs actually answer the question?"
3.  If No -> Rewrite query and search again.
