
![image](https://github.com/Charikshith/RAG_techniques/blob/main/Theory/images/RAG6.png)

Link :- [RAG from Scratch](https://github.com/langchain-ai/rag-from-scratch/tree/main)

## Advanced RAG Techniques: A Deeper Dive 🚀

This diagram outlines advanced techniques used to enhance Retrieval-Augmented Generation (RAG) systems beyond the basic 4-step process.  It categorizes these techniques across different stages of the RAG pipeline, from query handling to generation.

**(Refer to the image provided for visual context of these techniques and their flow.)**

---

### 1. Query Construction:  Crafting Better Questions 🤔

This section focuses on how to transform the initial user query to better interact with different knowledge sources.

*   **Relational DBs (Text-to-SQL):**
    *   **Concept:**  If your knowledge is stored in a relational database, you can translate natural language questions into SQL queries.
    *   **Technique:**  Use an LLM or specialized models to convert user questions into SQL (Structured Query Language) to directly query the database.  This can also involve using PGVector for vector similarity search within PostgreSQL databases.

*   **Graph DBs (Text-to-Cypher):**
    *   **Concept:**  For knowledge represented as graphs (relationships between entities), translate questions into graph query languages like Cypher (used by Neo4j).
    *   **Technique:**  Employ LLMs to convert natural language into Cypher queries to efficiently navigate and extract information from graph databases.

*   **Vector DBs (Self-Query Retriever):**
    *   **Concept:**  Improve retrieval from vector databases by automatically generating metadata filters directly from the user's query.
    *   **Technique:**  Use a "Self-Query Retriever" which leverages an LLM to parse the user's question and intelligently create metadata filters that are applied during the vector database search, making retrieval more precise.

---

### 2. Query Translation:  Refining User Input for Better Retrieval ✍️

This stage focuses on modifying or rephrasing the user's question to improve retrieval effectiveness before it hits the knowledge base.

*   **Query Decomposition:**
    *   **Concept:** Break down complex, multi-part questions into simpler, more focused sub-questions or step-back questions.
    *   **Techniques:**
        *   **Multi-query:** Generate multiple related queries from the original question to broaden the search.
        *   **Step-back Question:** Rephrase the question to a more general, higher-level query to retrieve broader context before answering the specific question.
        *   **RAG-Fusion:** Combine results from multiple different queries related to the user's intent to get a more comprehensive set of relevant documents.

*   **Pseudo-documents (HyDE - Hypothetical Document Embeddings):**
    *   **Concept:**  Instead of embedding the user's question directly, have the LLM generate a hypothetical "document" that *would* answer the question.  Then, embed this hypothetical document and use it for retrieval.
    *   **Technique:**  Use HyDE. The idea is that embedding a hypothetical answer might capture the semantic intent of the query more effectively than embedding the question itself, leading to better retrieval of actual relevant documents.

---

### 3. Routing:  Intelligent Knowledge Source Selection 🚦

Routing addresses how to choose the most appropriate knowledge source(s) for a given query when you have multiple options (e.g., different databases, document collections).

*   **Logical Routing:**
    *   **Concept:**  Use an LLM to *logically* determine which knowledge source is most likely to contain the answer based on the question's content and nature.
    *   **Technique:**  Train or prompt an LLM to act as a router.  It analyzes the question and then directs the query to the most suitable database or knowledge source from the available options.

*   **Semantic Routing:**
    *   **Concept:**  Embed the question and compare its embedding similarity to embeddings of prompts associated with different knowledge sources. Choose the source with the highest similarity.
    *   **Technique:**  Create embeddings for representative prompts or descriptions of each knowledge source. Embed the incoming user question and find the prompt embedding it's most similar to. Route the query to the knowledge source associated with that most similar prompt.

---

### 4. Retrieval:  Advanced Techniques for Finding Relevant Information 🔍

This section focuses on refining the retrieval process itself to get more accurate and relevant chunks.

*   **Ranking (Relevance Enhancement):**
    *   **Concept:**  After initial retrieval, re-rank or filter the retrieved documents to prioritize the most relevant ones or compress them for better context.
    *   **Techniques:**
        *   **Re-Rank:** Use a more sophisticated model (like RankGPT) to re-score and re-order the initially retrieved documents based on relevance to the query.
        *   **RAG-Fusion (in Retrieval):**  As mentioned in Query Translation, can also be applied at the retrieval stage to fuse results from different retrieval strategies.
        *   **Rank or Filter / Compress:**  Rank documents and then either filter out low-ranking ones or compress the content of the top-ranked documents to fit within the LLM's context window.

*   **Refinement (CRAG - Corrective Retrieval Augmented Generation):**
    *   **Concept:**  Implement a feedback loop where the system checks if the initially retrieved documents are actually relevant. If not, it triggers re-retrieval or retrieval from new data sources (like live web search).
    *   **Technique:**  Use CRAG. If the LLM, after reviewing the initial retrieved context, determines it's insufficient or irrelevant, CRAG initiates a new retrieval process, potentially expanding the search or using different retrieval methods. This iterative approach improves retrieval quality dynamically.

*   **Active Retrieval (CRAG - also applies here):**
    *   **Concept:**  Similar to Refinement, actively decide whether to re-retrieve or retrieve from *new* data sources if the initial retrieval isn't satisfactory.
    *   **Technique:**  Again, CRAG methodology.  If initial results are poor, it can dynamically decide to search the web or other sources in real-time to augment the knowledge base for the current query.

---

### 5. Indexing:  Optimizing Knowledge Organization 🗂️

Indexing techniques go beyond simple chunking to create more effective knowledge representations for retrieval.

*   **Chunk Optimization:**
    *   **Concept:**  Experiment with different strategies for splitting documents into chunks to optimize retrieval performance.
    *   **Techniques:**
        *   **Semantic Splitter:**  Use semantic understanding to chunk text at meaningful boundaries (sentence breaks, paragraph breaks, section divisions) rather than fixed character or token lengths.
        *   **Optimize Chunk Size:** Tune the chunk size based on the embedding model, vector database, and LLM's context window to find the sweet spot for retrieval relevance and context provision.
        *   **Delimiters:** Use specific delimiters or structural elements in documents to guide chunking (e.g., headings, bullet points).

*   **Multi-representation Indexing:**
    *   **Concept:**  Index documents in multiple formats or representations to capture different aspects of the information.
    *   **Techniques:**
        *   **Summary Indexing:** Create summaries of larger documents and index both the summaries *and* the full documents. Retrieve based on summaries, then access full documents for detailed context.
        *   **Parent Document, Dense X:**  Store documents in a hierarchical structure. Index smaller chunks (e.g., sentences) but also keep track of their "parent" document or larger context unit. This allows retrieval of fine-grained chunks while retaining access to broader context if needed.
        *   **Convert Documents into Compact Retrieval Units (e.g., a summary):**  Transform entire documents into more condensed, retrieval-optimized units like summaries or key-point extractions before indexing.

*   **Specialized Embeddings:**
    *   **Concept:**  Use embedding models that are specifically fine-tuned or designed for particular domains or tasks to improve semantic representation and retrieval accuracy.
    *   **Techniques:**
        *   **Fine-tuning Embedding Models:** Fine-tune general-purpose embedding models on domain-specific data to make them better at capturing nuances in that domain.
        *   **ColBERT (Contextualized Late Interaction over BERT):**  Use models like ColBERT which are designed for efficient retrieval tasks and can handle longer contexts more effectively.
        *   **Domain-specific and/or Advanced Embedding Models:** Explore and utilize embedding models specifically trained for tasks like document retrieval, code search, or biomedical text, depending on your application.

*   **Hierarchical Indexing & Summaries (RAPTOR - Recursive Abstractive Phrasal Tree for Open-domain Retrieval):**
    *   **Concept:**  Create a multi-level index with document summaries at different levels of abstraction.  This allows for efficient browsing and retrieval at varying levels of detail.
    *   **Technique:**  Use RAPTOR. Build a tree-like index where higher levels contain summaries of lower levels. This allows for a coarse-to-fine retrieval process, quickly narrowing down to relevant sections of documents.

---

### 6. Generation:  Refining the Final Answer Output ✍️

Generation techniques focus on improving the quality and relevance of the final answer produced by the LLM.

*   **Active Retrieval (in Generation - also uses CRAG):**
    *   **Concept:** If the LLM judges the retrieved context as insufficient *during generation*, trigger re-retrieval or retrieval from new sources to get more information before finalizing the answer.
    *   **Technique:**  CRAG framework (again). If, during the generation phase, the LLM determines it needs more information to answer effectively, Active Retrieval is triggered to dynamically fetch more context.

*   **Self-RAG, RRR (Relevance Ranking and Re-writing):**
    *   **Concept:**  Use generation quality feedback to inform question rewriting and/or re-retrieval of documents.  Iteratively refine the query and retrieval process based on the LLM's initial generation attempts.
    *   **Technique:**  Self-RAG or RRR.  The LLM itself evaluates the quality and relevance of its generated answer. Based on this self-assessment, it might decide to re-write the original question, trigger a new retrieval round with the revised question, or refine the retrieved documents before generating a final, improved answer. This creates a self-improving RAG loop.

---
