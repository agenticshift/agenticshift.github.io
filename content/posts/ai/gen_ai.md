+++
date = '2025-05-10T12:44:47+10:00'
draft = false
title = 'Gen AI Patterns'
tags = ['LLM', 'AI', 'Design Patterns']
+++

LLM design patterns are reusable strategies for building robust, efficient, and scalable AI applications. They help developers structure retrieval, reading, rewriting, memory, agent, and orchestration workflows for large language models. These patterns improve performance, maintainability accuracy, cost and security.

## Table of Contents
- [Introduction](#introduction)
- [Concepts](#concepts)
  - [LLMs](#llms)
  - [Transformer Architecture](#transformer-architecture)
  - [Tokens](#tokens)
  - [LLM Model Variants and Techniques](#llm-model-variants-and-techniques)
  - [Chat Models](#chat-models)
  - [Model Parameters](#model-paramaters)
- [Popular LLM Tools and Frameworks](#popular-llm-tools-and-frameworks)
- [Prompt](#prompt)
- [Prompt Engineering and Patterns](#prompt-engineering-and-patterns)
- [Roles](#roles)
- [Structured Output](#structured-output)
- [Output & Performance](#output--performance)
- [Embeddings](#embeddings)
- [RAG (Retrieval-Augmented Generation)](#rag-retrieval-augmented-generation)
- [Vector Store](#vector-store)
- [Some Strategies for Fintech Use Cases](#some-strategies-for-fintech-usecases)
  - [1. Information in Confluence/Knowledge Bases](#1-information-in-confluenceknowledge-bases)
  - [2. Representing Information Based on Retrieval Strategy](#2-representing-information-based-on-retrieval-strategy)
  - [3. Chunking with Overlap](#3-chunking-with-overlap-strategy-for-splitting-text-into-meaningful-chunks)
  - [4. Language or Format-Aware Chunking](#4-language-or-format-aware-chunking-strategy-for-splitting-text-into-meaningful-chunks)
  - [5. Maintaining References](#5-maintaining-references-strategy-for-splitting-text-into-meaningful-chunks)
  - [6. Strategies for Mathematical Operations](#6-strategies-for-mathematical-operations)
  - [7. Strategies for Security Compliance](#7-strategies-for-security-compliance)
  - [8. Strategy for Vector DB - Document Change Tracking](#8-strategy-for-vector-db---document-change-tracking)
- [Some Design Patterns](#some-design-patterns)
  - [1. MultiVector Retrieval](#1-multivector-retrieval)
  - [2. Recursive Abstractive Processing for Tree-Organized Retrieval](#2-recursive-abstractive-processing-for-tree-organized-retrieval)
  - [3. ColBERT (Contextually Late Interactions using BERT)](#3-colbert-contextually-late-interactions-using-bert)
  - [4. Strategies to Get Control Over Prompting](#4-strategies-to-get-control-over-prompting)
  - [5. Rewritten Prompting aka Rewrite-Retrieve-Read](#5-rewritten-prompting-aka-rewrite-retrieve-read)
- [Effective Strategies Using RAG](#effective-strategies-using-rag)
  - [6. Multi-Query Retrieval](#6-multi-query-retrieval)
  - [7. RAG-Fusion](#7-rag-fusion)
  - [8. Hypothetical Document Embeddings (HyDE)](#8-hypothetical-document-embeddings-hyde)
- [Query Routing](#query-routing)
  - [9. Logical Routing](#9-logical-routing)
  - [10. Semantic Routing](#10-semantic-routing)
- [Query Construction](#query-construction)
  - [11. Text-to-Metadata Conversion](#11-text-to-metadata-conversion)
  - [12. Text-to-SQL](#12-text-to-sql)
- [Additional Prompt & Agent Patterns](#additional-prompt--agent-patterns)
  - [13. Chatbot with Memory](#13-chatbot-with-memory)
  - [14. ReAct (Reason + Act)](#14-react-reason--act)
  - [15. Reflexion](#15-reflexion)
  - [16. Human-in-the-Loop](#16-human-in-the-loop)
  - [17. Other Patterns](#17-other-patterns)

## Introduction

Before we proceed setting the stage.
## Concepts 
### LLMs
LLM an AI model trained on massive amounts of text. It is kind of able to understand, generate, and reason with natural language.
- GPT-3: 175B Parameters, ~350B tokens. GPT-4:is 6x bigger. GPT-5 was supposed to be 20x bigger but is counter intuitive 300B Parameters.
- Pre-trained on massive text corpora to learn language patterns.
- parametric memory: knowledge stored in model weights.
- Model hub: Repositories of pre-trained models for easy access and deployment e.g. Hugging Face, TensorFlow Hub, PyTorch Hub, Amazon SageMaker JumpStart, etc.
- Model Card: documentation that provides details about a model's architecture, training data, performance metrics, intended use cases, and ethical considerations. Model cards help users understand the capabilities and limitations of a model before deploying it in real-world applications.

---

### Chinchilla scaling laws
* Earlier large language models (like GPT-3) were trained with too many parameters and too little data, making them under-trained. 
* Researchers trained a family of models (called Chinchilla) and discovered a scaling law that balances model size (parameters, 𝑁) and  with dataset size (tokens, 𝐷) for compute-optimal performance.
  - 𝐷 = number of training tokens  
  - 𝑁 = number of parameters
  - 𝐶 = total training compute (in FLOPs)
  
If you have a fixed compute budget, the best performance is achieved when the number of training tokens scales linearly with the number of parameters. Older practice (like GPT-3) trained much larger models on fewer tokens → under-trained. Chinchilla showed that smaller models trained on more data perform better at the same compute cost.
```𝐷 ∝ 𝑁```

**Chinchilla Scaling Formula**
From the paper, the compute-optimal relation between parameters and tokens is:
```𝐷 ≈ 20 × 𝑁```
(So a model with 70B parameters should be trained on about 1.4 trillion tokens.)
hundreds of billions/trillions of parameters requires enormous data more than available high-quality text.

**Training Compute**
Total training FLOPs scale approximately as:
𝐶 ≈ 6 × 𝑁 × 𝐷
(where the constant 6 comes from forward + backward passes and optimizer overheads).

  - GPT-5 leverages on many architectural innovations like:
    - Unified Intelligence System: integrates multiple reasoning and perception modules for more general intelligence.
    - Mixture-of-Experts: routes inputs to specialized sub-models for improved efficiency and accuracy.
    - Multimodal Integration: processes and understands text, images, and other data types together.

---

### Tokens:
  - A token is a chunk of text, it could be entire word or part.
  - Roughly, one token is about 4 characters or for calculation sake 75% of  words in English language.
  - why important? - for us to estimate monthly cost: (tokens used per month) × (API cost per token).

---
- 
### Tokenizers: 
  - convert text into tokens (subwords, words, or characters) for model input. Common types include Byte Pair Encoding (BPE), WordPiece, and SentencePiece. 
  - vector id: unique identifier for each token in the tokenizer's vocabulary.
---

### Transformer Architecture: 
  - LLM's use the transformer neural network architecture.
  - Transformers understand relationships between words, regardless of their position in the text.
  - The architecture enables us to **predict the next word** in a sequence - they should **not be expected** to perform **accurate mathematical computation**; lot of our AI usecases some have some math oprations involved.
  - Three main techniques:
    - Predict next word (causal language modeling): Used by models like GPT; generates text by predicting the next token in a sequence.
    - Predict masked word: Used by models like BERT; predicts masked tokens within a sentence for better understanding of context.
  - **GPT models - Decoder Focused : Focus on text generation, conversation, and completion tasks.**
  - BERT models - Encoder Focused: Focus on understanding, classification, and extracting information from text.
  - T5, BART - Encoder & decoder : Good for translation, summarization, and more complex tasks.
  - Many LLMs are specialized for tasks like code generation, multimodal input (text + images), or domain-specific knowledge.

---

### Chat Models
- LLMs specialized in **conversational interactions**.

| Model                                                                                                        | Provider       | Strengths                                   | Typical Use Cases                                 |
|--------------------------------------------------------------------------------------------------------------|----------------|---------------------------------------------|---------------------------------------------------|
| Copilot                                                                                        |  Copilot utilizes Mixture-of-Experts -  under the hood uses - OpenAI,  Microsoft's property models and other models & tools      | Text generation, conversation, code, summarization | Chatbots, assistants, writing, code, content      |
| LLaMA                                                                                                        | Meta           | Open-source, efficient, adaptable           | Research, custom training, open-source apps       |
| Claude                                                                                                       | Anthropic      | Safety-focused, reliable, conversational    | Support, safe chatbots, document analysis         |
| Mistral                                                                                                      | Mistral AI     | Efficient, open weights, strong benchmarks  | Research, production NLP, open-source projects    |
| Gemini                                                                                                       | Google DeepMind | Multimodal (text, image, audio), reasoning  | Multimodal assistants, content analysis           |
| Anthropic (Claude), Amazon (Titan), Meta (Llama),<br/>Mistral, AI21 Labs (Jurassic), Cohere (Command) | Amazon Bedrock | Wide model selection, enterprise integration, scalable APIs | Chatbots, search, summarization, enterprise AI    |

---

### Compare Models
- https://artificialanalysis.ai/
- https://www.vellum.ai/llm-leaderboard?utm_source=direct&utm_medium=none#compare

---

### Model Paramaters
| Parameter            | Description                                                                                     | Typical Range        | Impact                                                                 |
|----------------------|-------------------------------------------------------------------------------------------------|----------------------|------------------------------------------------------------------------|
| **temperature**      | Controls randomness. Lower = deterministic, higher = creative/unpredictable.                  | `0.0` – `2.0`       | `0.0` = deterministic, `1.0` = balanced, `>1.2` = very random.        |
| **top_p**            | Nucleus sampling. keep the cumulative probability to p.           | `0.1` – `1.0`       | Lower = more focused, 1.0 = no restriction.                           |
| **top_k**            | token selection is from the k most probable tokens.   | `1` – `100`         | Low values reduce creativity, high values allow more variation.       |
| **max_tokens**       | Maximum tokens to generate in the response.                                                   | Depends on model    | Controls output length.                                               |
| **min_tokens**       | Minimum tokens to generate before stopping.                                                   | Depends on model    | Ensures longer answers if needed.                                     |
| **presence_penalty** | Penalizes tokens already present in the text (encourages new topics).                          | `-2.0` – `2.0`      | Higher = more diversity.                                              |
| **frequency_penalty**| Penalizes frequent tokens to reduce repetition.                                               | `-2.0` – `2.0`      | Higher = less repetition.                                             |
| **stop**             | List of stop sequences where generation should halt.                                          | Strings / tokens    | Useful for structured responses.                                      |
| **seed**             | Fixes randomness for reproducibility.                                                         | Integer             | Same seed = same output (with sampling).                              |
| **length_penalty**   | Adjusts likelihood for longer sequences in beam search.                                       | `>0`                | Higher = longer outputs favored.                                      |
| **early_stopping**   | Stops beam search when best candidates are found.                                             | `true` / `false`    | Speeds up generation, may miss better completions.                    |
| **logit_bias**       | dictionary to control probability of certain token ids   | Useful for forcing or avoiding words.                                 |
| **echo**             | Return the prompt along with the completion.                                                  | `true` / `false`    | For debugging or prompt reconstruction.                                |

#### Top p

| Token | Probability |
|-------|------------|
| the   | 0.4        |
| a     | 0.3        |
| an    | 0.1        |
| dog   | 0.05       |

If `p = 0.8`:
- Cumulative probabilities:
  - `"the" (0.4) + "a" (0.3) + "an" (0.1) = 0.8`
- Nucleus = `{"the", "a", "an"}`
- Next token is sampled **only** from this set.

#### Top k 

Suppose the token probabilities are:

| Token | Probability |
|-------|------------|
| the   | 0.4        |
| a     | 0.3        |
| an    | 0.1        |
| dog   | 0.05       |

If `k = 3`:

- Top 3 tokens = `{"the", "a", "an"}`
- Next token is sampled **only** from this set.

---

### Popular LLM Tools and Frameworks

| Tool/Framework         | Language   | Focus/Strengths                  | Integration      | Use Case Examples              |
|-----------------------|------------|----------------------------------|------------------|-------------------------------|
| LangChain             | Python     | Chaining, agents, retrieval      | Many LLM APIs    | Chatbots, RAG, workflows      |
| LangGraph             | Python     | Multi-agent, graph orchestration | LangChain        | Reasoning, modular agents     |
| Spring AI             | Java       | Enterprise, abstraction          | Spring ecosystem | Business apps, backend        |
| HF Transformers       | Python     | Model training, deployment       | Model hub, APIs  | NLP tasks, research, prod     |
| Agentic AI (AutoGPT, CrewAI, N8N) | Python/JS | Autonomous agents, automation | Various          | Task automation, multi-agent  |

---

### Prompt
- A prompt is the input text or set of instruction given to an LLM to guide its response. 
- Considerations for prompts Common Issues:
  - **Clarity**: We have ambiguity or we use our own DSLs. 
  - **Context**: Prompts lack enough relevant background or examples.
  - **Length**: Too short - lack detail; too long - confuse or dilute intent.
  - **Iteration**: you may have to refine prompts based on responses to improve outcomes.
  - **Inconsistent results**: Due to different models and also different model parameters.
  - **Structured Output** format varies by model:
    - Nested structures may be inconsistent or flattened unexpectedly. This was particularly the case with Mistral. 
    - Some prepend **`assistant:`** or add **Markdown fences (```json```)**.
    - Some omit required fields or slightly alter the structure.
  - Models may generate **extra explanations** or comments alongside the data.
  - Inconsistent **data types** (numbers as strings, null vs missing fields).
  - Missing **mandatory keys** in JSON or headers in CSV.
  - Some models **truncate long outputs**, breaking the structure.
  - Free-text injections may appear despite formatting instructions.

#### Solutions
- Use **prompt enginnering techniques** with examples to guide the structure.
- **Use of Strong models**: Models such as OpenAI or use of CoPilot Strong support via JSON mode or function calling.

---

### Prompt Engineering and Patterns
- Prompt engineering is the practice of designing and refining prompts to optimize LLM outputs.
- Prompt engineers experiment with wording, structure, and context to achieve desired results.
- Common patterns:

| **Type**                       | **Definition**                                                       | **Example**                                                                 |
|--------------------------------|----------------------------------------------------------------------|------------------------------------------------------------------------------|
| **Single-String Prompting**    | One large text prompt, meant to be completed.             | `Translate to French: "How are you?"`                                      |
| **Chat-Based Prompting**       | Structured messages with roles (`system`, `user`, `assistant`).     | System: "You are helpful." User: "Translate 'How are you?' to French."     |
| **Zero-Shot**                  | No examples provided; just the instruction.                         | `Summarize this text in one sentence: ...`                                  |
| **Few-Shot**                   | Include a few examples to guide the output.                         | `Q: Capital of France? A: Paris. Q: Capital of Germany? A:`                |
| **Chain-of-Thought (CoT)**     | Ask model to reason step-by-step before answering.                  | `Explain step by step: Why the calculated Balance amount is $200` |
| **Self-Consistency**           | Model generates multiple solutions, picks most consistent.          |  reasoning-heavy tasks. Why is the balance showing at $200 instead of $100? Generate multiple reasoning paths                                              |
| **Role-Based / Persona**       | Assign a role or persona for better context.                        | `Act as an Charging Business Analyst. Explain Adjustment.`                        |
| **Instruction-Based**          | Direct task-oriented commands without dialogue.                     | `Extract all email addresses from the text.`                                |
| **Structured Output**          | Request output in JSON, table, or specific format.                  | `Return the answer in JSON with keys: event_name, event_publisher.`                           |
| **ReAct (Reason + Act)**       | Model reasons, then calls tools or APIs.                            | `Thought: I need weather info. Action: call weather API.`                   |
| **Retrieval-Augmented (RAG)**  | Combine prompt with retrieved external knowledge.                   | `[Context] Answer based on above.`                                          |
| **Multimodal Prompting**       | Combines text + images (or other modalities).                       | `Describe the scene in this image.`                                         |
| **Prompt Assiting**       | Similar to meta prompting but using copilot to generate prompts for mistral                       | `Describe the scene in this image.`                                         |
| **Prompt Chaining**            | Splits a task into multiple linked prompts.                         | Step 1: Summarize → Step 2: Extract entities → Step 3: Generate questions. |
| **Reflection / Deliberation**  | Ask model to review or critique its own response.                   | `Check your previous answer and improve it.`                                |
| **Tool Calling**               | Enable model to trigger external tools for answers.                 | `If math needed, call calculator function.`                                  |
| **Function Calling**           | Model outputs structured JSON to call specific function.            | `{ "function": "get_weather", "location": "Paris" }`                        |

---

### Roles
| Role        | Purpose                                                       | Example                                                                 |
|-------------|--------------------------------------------------------------|------------------------------------------------------------------------|
| **system**  | Sets high-level instructions, behavior, or persona for model | "You are a software engineer in Charging Space. " |
| **user**    | Represents the end-user’s input or question                  | "Write a spring boot function to apply adjustment on the balance." |
| **assistant**| Represents the model’s own response                         | "Here is the Python code:\n```python\ndef reverse_string(s): return s[::-1]\n```" |
| **tool**    | Output from an external tool or function call                | `{"result": "42"}` (after a calculator function call)                  |
| **function**| (Older term for tool) Shows result from a function           | Same as tool                                                           |
| **critic**  | Used for self-evaluation or reinforcement learning loops     | "Check if the previous answer followed all constraints."               |
| **developer** (optional) | Provides additional instructions (multi-role systems) | "Add unit tests for the function above."                               |

apart from the system, user and assistant roles which are the default roles in some frameworks most frameworks support custom roles.
```python
from langchain.schema import ChatMessage

messages = [
    ChatMessage(role="system", content="You are an expert AI."),
    ChatMessage(role="developer", content="Always include type hints in Python code."),
    ChatMessage(role="user", content="Write a function to add two numbers.")
]
```
---

### Structured Output
Structured output model returns data in a **specific or predefined, machine-readable format** like JSON or CSV, instead of free-form text.

### Output & performance

| Strategy                   | Description / Tips                                                                                       |
|-----------------------------|---------------------------------------------------------------------------------------------------------|
| **Streaming vs Batch**      | Stream for user-facing outputs (low latency). Batch multiple prompts with templates for high throughput. |
| **Token Management**        | use things like **`max_tokens`**, **`stop`** tokens to avoid unnecessary generation.                     |
| **Parallelization**         | Run independent requests concurrently. few libraries support **async frameworks** or **thread pools.**                             |
| **Prompt Caching**                 | Cache repeated prompts/responses to reduce API calls and latency.                                       |
| **Model Selection**         | Use smaller/faster models for simple tasks; larger models for complex reasoning or high-quality output. |
| **Tooling Integration**     | Give it access to tools or RAG or vector databases                                            |
| **Function call & Structured Output** | to reduce parsing and token overhead.                        |
| support for **Imperative or Declarative Composition** | helps in controlling the sequence and flow of operations with LLMs, tools, and data        |
| **templating** | use of frameworks that support templating - easier to perform batch operations                       |

---

### Embeddings
- Embeddings represent text (words, sentences, or documents) as an **array of floating-point** numbers called **dimensions**, aka. **dense vector** .
- they capture semantic meaning of text
- Each floating-point value represents a **semantic dimension** of the text.
- **traveling the meaning** in space possible using the elementary math operations of addition and subtraction: **king** – **man** = **monarch** + **woman** = would arrived close to word **queen**. 
- how is it different from Chat models? Embedding models produce vectors for **semantic understanding**, while chat models produce **human-readable text**.
- **usecases**
    - semantic search
    - recommendation systems
    - document classification
    - clustering
    - information retrieval.
- **Common models for embeddings**:
    - OpenAI Embeddings
    - Cohere
    - Sentence Transformers
    - BERT

---

### RAG (Retrieval-Augmented Generation)
- Models have limited knowledge in the context of a specific business use case. RAG helps LLMs answer questions beyond their training data.
  
- **3 stages**
  - **Indexing:** Create embeddings of data and Store it in vector store.
  - **Retrieval:** retrieving the relevant embeddings and data from vector store
    - Techniques include:  
      - **Cosine Similarity:** Measures angle between vectors; commonly used for semantic similarity.  
      - **Euclidean Distance:** Measures straight-line distance in embedding space; good for spatial closeness.  
      - **Dot Product / Inner Product:** Measures magnitude of similarity; 
      - **Approximate Nearest Neighbors (KNN):** Efficient search in large vector stores for top-k similar vectors.  
  - **Generation:** Augmenting that with original prompt. 

- **Some issues you may face with RAG:**  
  - Sometimes the context data is large.  
  - sometimes data will be split across multiple documents or chunks.  
  - sometimes context data has irrelevant contents - requires the model to filter irrelevant info → risk of hallucination.

---

### Vector Store
- A vector store is a database for storing and searching embeddings.
- good for vector operations such as:
  - **Semantic Search:** Find documents or text that are meaningfully similar to a query.
  - **Classification:** Assigning a new document to a previously identified group or label (for instance, a topic)
  - **Question Answering:** Retrieve relevant context for LLMs to answer questions accurately.  
  - **Recommendation Systems:** Suggest items based on similarity of embeddings.  
  - **Clustering & Topic Analysis:** Group similar content or detect topics.  
  - **Anomaly Detection:** Identify outliers by distance from typical embeddings.
- Popular vector stores:
    - Pinecone
    - FAISS
    - PgVector
 
```python
# 1. Semantic Search
query = "Find documents about long documents."
results = pgvector_store.similarity_search(query, k=3)

# 2. Question Answering
answer = qa.run("What does the document talk about?")

# 3. Classification
nearest_docs = pgvector_store.similarity_search("New text to classify", k=1)
predicted_label = nearest_docs[0].metadata.get("label")

# 4. Recommendation
recommendations = pgvector_store.similarity_search("Guide on API design", k=5)

# 5. Clustering
all_embeddings = pgvector_store.get_embeddings()  # then apply KMeans externally

# 6. Anomaly Detection
new_embedding = embeddings.embed_query("Some unusual text.")
nearest = pgvector_store.similarity_search_by_vector(new_embedding, k=1)
```

---
## Some Strategis for Fintech usecases: 

### 1. Information in Confluence/knowledge bases:
- Start using Standard and Structured templates for documentation - serve as **context needed for the LLM**.
- follow best practices of **prompt engineering** check results in Copilot after generation.
- Knowledge can be provided as plain text, PDF, or **Markdown** format.  
- Markdown format easily converted to and from Confluence tools like **`pandoc`**.

---

### 2. Representing Information based on Retrieval strategy
- Sometimes representing information in **table** form is effective. Most of time **Markdown** is okay.
- Sometimes it more effective to use an RDBMS, Object Store or NoSQL where applicable.
- In Table - each cell corresponds to a **row and column**, making structured retrieval easier for the model.  

---

### 3. Chunking with Overlap (Strategy for Splitting Text into Meaningful Chunks)
- create an **overlap** between chunks or documents.  
- **Advantages:**  
  - It maintains context between chunks.  
  - Reduces risk of losing important information at **chunk boundaries**.  
- **Disadvantages:**  
  - **Increases the total number of chunks** → more embeddings and storage.  
  - Can introduce **redundancy in retrieval**.  

---

### 4. Language or Format-Aware Chunking (Strategy for Splitting Text into Meaningful Chunks)
- Split based on the type of content:  
  - **Markdown or structured text:** split by headings, sections, or paragraphs.  
  - **Code (Python, etc.):** split by functions, classes, or logical blocks.  
- Ensures each chunk is **semantically coherent or consistent**.  

---

### 5. Maintaining References (Strategy for Splitting Text into Meaningful Chunks)
- Retain a **reference to the original document** after splitting.  
- Useful for workflows like **Reflexion**, where you might need to trace information back to the source.  
- Useful when you need to provide provide **accurate citations or references**.

---

### 6. Strategies for Mathematical operations
- GPT models excel at predicting the next word and reasoning, but are not reliable for accurate mathematical calculations.
- Strategies to handle mathematical operations:
  - Use external tools or function calling (e.g., calculator APIs, Python code execution) for math tasks.
  - Integrate tool-calling patterns so the LLM delegates math problems to a dedicated computation engine.
  - Convert math queries into structured requests that trigger tool or function invocation.
  - For classification-type math (e.g., "is this a credit or debit"), use the LLM to route the query to the appropriate tool.
  - Always validate or post-process LLM-generated math answers with external logic or tools.

---

### 7. Strategies for Security Compliance
- **hybrid model approach**: classify and filter sensitive or secure data before sending to public or third-party models.
- **rewrite-retrieve-read** and **multi-query retrieval** patterns to detect and mitigate queries with ill intent or attempts to extract confidential information.
- **map and mask** pattern - left side is secure data right side is public data.
- **rewrite-match-return** 
  - Prefer on-prem or private models for highly confidential workloads.
  - Implement prompt sanitization and validation to prevent prompt injection attacks.
  - access controls and audit logging data & queries.
  - Regularly review and update security policies.

---

### 8. Strategy for Vector DB - Document Change Tracking
How to handle part of the document or chunk getting updated:
- libraries such as **SQLRecordManager** or similar strategies to track document updates.
- **Versioning with Metadata**: Each document update creates a new version; store version_id in metadata.
- **Hash-Based Updates**: Compute hash for each chunk; update only changed chunks.
- **Soft Deletes (Active Flag)**: Mark old chunks as is_active=False and insert new ones.
```python
vectorstore.similarity_search(query, filter={"is_active": True})
```
---
## Some Design Patterns:

### 1. MultiVector Retrieval  
- **Problem**: Mixed-content documents (text + tables) can lose structure if split only by text.
- **Design Pattern**: Follows *CQRS (Command Query Responsibility Segregation)* principle separate write (doc updates) and read (retrieval) models for consistency.
- seperate out the **vector store** and **doc store**.
- **Approach**: Maintain unique IDs across vector store and doc store.   
    - **vector store**: Store summaries or embeddings  
    - **doc store**" Store original content (tables, full text) in RDBMS, NoSQL, object store, or in-memory docstore.  
- Link via **ID references**.  
    
```python
# Helper function to fetch docs from MySQL
def fetch_docs_from_mysql(ids):
    placeholders = ','.join(['%s'] * len(ids))
    query = f"SELECT id, content, metadata FROM documents WHERE id IN ({placeholders})"
    mysql_cursor.execute(query, tuple(ids))
    rows = mysql_cursor.fetchall()
    return [Document(page_content=row['content'], metadata=row['metadata']) for row in rows]

# 3. Create MultiVectorRetriever
retriever = MultiVectorRetriever(
    vectorstore=pgvector_store,
    docstore=fetch_docs_from_mysql,
    id_key="id"
)
```

or 
```python
docstore = InMemoryDocstore()

# 2. Add documents to InMemoryDocstore
doc_ids = ["doc1", "doc2"]
chunks = [
    Document(page_content="First document about LangChain.", metadata={"category": "AI"}),
    Document(page_content="Second document about Vector Stores.", metadata={"category": "Database"})
]
docstore.mset(list(zip(doc_ids, chunks)))

# 3. Create MultiVectorRetriever
retriever = MultiVectorRetriever(
    vectorstore=pgvector_store,  # Same as before
    docstore=docstore,           # InMemoryDocstore instance
    id_key="id"                  # ID used in vector store entries
)
```
<img width="1200" height="611" alt="image" src="https://github.com/user-attachments/assets/830dff2f-637f-4987-9825-44a56cacb205" />

---

### 2. Recursive Abstractive Processing for Tree-Organized Retrieval —  
- **Problem:**  
  - RAG systems must handle:
    - **Lower-level questions:** referencing specific facts in a single document.  
    - **Higher-level questions:** synthesizing ideas across multiple documents.  
  - Simple k-NN retrieval on chunks struggles to cover both aspects.  

- **Solution is to follow the RAPTOR Approach**
  - First split document into **Chunks**.  
  - **document summaries** capturing higher-level concepts.  
  - based on semantic similarity of documents (Embeddings) **cluster** together.  
  - **Summarize each cluster** to produce **higher-level abstractions**.  
  - Repeat **recursively**, forming a **tree of summaries** with increasing abstraction.  

- **Indexing:**  
  - Index **both the summaries and the original documents** together.  
  - Covers for **questions ranging from low-level to high-level concepts**.

<img width="1810" height="1356" alt="image" src="https://github.com/user-attachments/assets/781bebc6-19d2-4814-af83-0983f081b6ba" />
 
```python
# 2. Split documents into chunks
docs = [Document(page_content=doc) for doc in all_documents]  # all_documents is your text list
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks = []
for doc in docs:
    chunks.extend(splitter.split_text(doc.page_content))

# 3. Generate summaries for each chunk
chunk_summaries = []
for chunk in chunks:
    summary = llm(f"Summarize the following text concisely:\n{chunk}")
    chunk_summaries.append(Document(page_content=summary))

# 4. Cluster summaries
num_clusters = 5
summary_embeddings = [embeddings.embed_query(s.page_content) for s in chunk_summaries]
kmeans = KMeans(n_clusters=num_clusters, random_state=0).fit(summary_embeddings)

# 5. Summarize each cluster (recursive abstraction)
cluster_summaries = []
for cluster_idx in range(num_clusters):
    cluster_docs = [chunk_summaries[i].page_content for i in range(len(chunk_summaries)) 
                    if kmeans.labels_[i] == cluster_idx]
    cluster_text = "\n".join(cluster_docs)
    cluster_summary = llm(f"Summarize the following cluster text:\n{cluster_text}")
    cluster_summaries.append(Document(page_content=cluster_summary))

# 6. Index both original chunks and cluster summaries in vector store
vectorstore.add_documents(chunks + cluster_summaries)

# 7. Query example
query = "Explain high-level ideas from the documents."
results = vectorstore.similarity_search(query, k=3)
for r in results:
    print(r.page_content)
```

---

### 3. ColBERT (Contextually Late Interactions using BERT): Optimizing Embeddings

- **Problem with standard embeddings:**  
  - They **compress** the whole text into one **fixed-length vector**.  
  - Any **Irrelevant or redundant content** in that vector can lead to **wrong** or **hallucinated answers**.  

**ColBERT Approach:**
- uses **late interaction**, Late interaction means each token in **query** and **document** is **encoded independently first**, and **only after encoding**, their similarities are computed (using **MaxSim**).
  1. **Token-level embeddings:** Instead of one vector per document, ColBERT creates an embedding for each token.  
  2. **Compare at token level:** For every token in **query**, it compute similarity with all tokens in the **document**.  
  3. **Aggregate smartly:** **Sum these matches** = **final document score** = **best match**.  

- **Advantages:**  
  - **Reduces irrelevant** contents.  
  - Improves accuracy of retrieved context for LLMs.  

- **Key takeaway:**  

```python
from ragatouille import RAGPretrainedModel
# Load the ColBERT-based RAG model
RAG = RAGPretrainedModel.from_pretrained("colbert-ir/colbertv2.0")

# Example usage: encode documents or queries
docs = ["LangChain helps build applications with LLMs efficiently."]
query = "How to use LangChain for LLM apps?"

# Encode the documents (token-level embeddings)
doc_embeddings = RAG.encode_documents(docs)

# Encode the query
query_embedding = RAG.encode_query(query)

# Retrieve top-k relevant documents
results = RAG.retrieve(query_embedding, k=3)
for r in results:
    print(r)
```
<img width="1400" height="578" alt="image" src="https://github.com/user-attachments/assets/cc1d9783-9da8-4220-a1c4-e7b48beb5e16" />

---

### 4. strategies to get Control over Prompting:

1. Query Transformation
- Incoming inputs can be **varied and uncontrolled**, sources may be:  
  1. Event-driven data (which may contain secure data)
  2. Future integration with new systems (which may be outside VPC)
  3. User inputs  
- **Solution:** Transform queries into a format the system can reliably answer.  
- Benefits:  
  - Ensures consistent processing across diverse inputs.  
  - Helps address security concerns and prevents misuse.  

2. Step-Back Prompting
- Ask the model to **analyze or reflect** before giving a final answer.  
- Helps identify assumptions, gaps, or errors in reasoning.  
- Reduces mistakes in **multi-step reasoning**.

3. Subquestion Prompting
- Breaks a **complex question into smaller subquestions**.  
- Answer each subquestion individually and aggregate results.  
- Improves accuracy for **multi-part queries** and reduces hallucination.

## Patterns for Controlling Prompt 

### 5.Rewritten Prompting aka Rewrite-Retrieve-Read 
- We have seen before 2 patterns
  - **map and mask** pattern - left side is secure data right side is public data.
  - **rewrite-match-return** 
- Reformulates a query to **clarify intent or simplify language**.  
- Ensures the LLM focuses on the intended question.  
- Reduces misinterpretation and improves output quality.

---

## Effective Strategies Using RAG
- Depending on the data type and usecase , it may be more efficient to spread data across
  - **vector store** for operations like semantic search.
  - **RDBMS** for structured data with relationships.
  - **NoSQL** for unstructured or semi-structured data.
  - **Object Store**. for large files or blobs.

---

### 6. Multi-Query Retrieval
- **Problem:** A single user query may not capture the full scope of information needed for a comprehensive answer.  

- **Solution:** Multi-query retrieval strategy:
  1. **Query Expansion:** Use an LLM to generate multiple related queries from the user’s initial query.  
  2. **Parallel Retrieval:** Execute each generated query against the data source (vector store, RDBMS, etc.).  
  3. **Context Aggregation:** Combine retrieved results as prompt context for the LLM.  
  4. **Final Output:** The LLM generates a more complete and accurate answer using the aggregated context.  

- **Benefit:** Improves coverage and accuracy by capturing **all relevant information** across the dataset.
- **Example**: What is the weather in Sydney?”, give me 5 possible queries:
    1. What is the current temperature in Sydney?
    2. Are there any weather warnings or alerts in Sydney right now?
    3. What is the forecast for Sydney for the next few hours?
    4. Is it sunny, rainy, or cloudy in Sydney currently?
    5. What is the humidity and wind speed in Sydney today?
       
<img width="1676" height="596" alt="image" src="https://github.com/user-attachments/assets/595030dc-1a2b-4fa4-8a82-4ec58822a72d" />

---

### 7. RAG-Fusion
Sometimes **information space is large** and **spread out** or **diverse**, and a single query is **insufficient** to capture all relevant context.
RAG-Fusion (Retrieval-Augmented Generation with Fusion) is an **extension** of the **multi-query retrieval strategy**. It enhances the retrieval process by introducing a **final reranking step** using the **Reciprocal Rank Fusion (RRF)** algorithm.

### Steps

1. **Query Expansion:** Generate multiple related queries from the user's initial query.
2. **Parallel Retrieval:** of each query independently against the data source (**vector store**, **RDBMS**, **search engine** etc).
3. **Reciprocal Rank Fusion (RRF):**  
   - Each retrieved document has a rank for each query.  
   - RRF combines these ranks into a single, unified ranking.  
   - Formula: ```math  RRF Score = \sum_{i} \frac{1}{k + \text{rank}_i}``` where \(k\) is a constant (commonly 60) to reduce the effect of lower-ranked documents. a big contant so that the rank doesn't domainate and have advantage.
   - The most relevant documents across all queries rise to the top.
4. **Context Aggregation:** Use the top-ranked documents as context for the LLM.
5. **Final Output:** LLM generates a more accurate and comprehensive answer using aggregated, reranked context.

#### Benefits

- Improves retrieval quality by prioritizing documents relevant to multiple query perspectives.
- Handles queries with different scoring scales or distributions effectively.
- Reduces noise from less relevant results while promoting consensus across queries.

#### Use Cases

1. **Legal or Compliance like ACMA or TCP or Security documenations:**  
   - Searching across multiple regulations, rules, and previous incidents.  
   - RRF helps surface the most relevant documents that are supported across different search formulations.

2. **Enterprise Search:**  
   - Documents across Company
```python
embeddings_model = OpenAIEmbeddings()

db = PGVector.from_documents(
    documents, embeddings_model, connection=connection)

# return the top 5 results ranked by similarity score (most relevant first).
retriever = db.as_retriever(search_kwargs={"k": 5})

prompt_rag_fusion = ChatPromptTemplate.from_template(
    """You are a helpful assistant that generates multiple search queries based on a single input query. 
    \n Generate multiple search queries related to: {question} \n Output (4 queries):""")

query_gen = prompt_rag_fusion | llm | parse_queries_output
generated_queries = query_gen.invoke(query)

def reciprocal_rank_fusion(results: list[list], k=60):
    """reciprocal rank fusion on multiple lists of ranked documents 
    and an optional parameter k used in the RRF formula"""
    # Initialize a dictionary to hold fused scores for each document
    # Documents will be keyed by their contents to ensure uniqueness
    fused_scores = {}
    documents = {}
    for docs in results:
        # Iterate through each document in the list, with its rank (position in the list)
        for rank, doc in enumerate(docs):
            doc_str = doc.page_content
            if doc_str not in fused_scores:
                fused_scores[doc_str] = 0
                documents[doc_str] = doc
            fused_scores[doc_str] += 1 / (rank + k)
    # sort the documents based on their fused scores in descending order to get the final reranked results
    reranked_doc_strs = sorted(
        fused_scores, key=lambda d: fused_scores[d], reverse=True)
    return [documents[doc_str] for doc_str in reranked_doc_strs]

retrieval_chain = query_gen | retriever.batch | reciprocal_rank_fusion
```
<img width="1284" height="481" alt="image" src="https://github.com/user-attachments/assets/9597f42d-7e23-40f5-985e-cabef52035b3" />

---

### 8. Hypothetical Document Embeddings (HyDE)
**HyDE** is a retrieval strategy that leverages LLMs to improve vector-based search by creating a **hypothetical document** from the user’s query. The idea is that the LLM can expand, paraphrase, or contextualize the query into a richer representation that is more semantically similar to relevant documents in the dataset.
HyDE effectively **bridges the gap between natural language queries and document embeddings**, improving the relevance of retrieved documents in retrieval-augmented generation (RAG) systems.

* How HyDE Works
  - The user provides a query, e.g., `"What is the current Balance and give step by step how you arrive at that."`
  - An LLM generates a **hypothetical document** or passage as if it were an answer to the query.  
  - The hypothetical document is converted into a **vector embedding** using the same embedding model as used for the document database.
  - The embedding is used to perform a **vector search** against the document corpus.  
  - The assumption: the LLM-generated document is **closer in embedding space** to relevant documents than the raw query. 
  - Top-k similar documents are retrieved and can then be fed as context to the LLM for final answer generation. 

* Benefits
    - **Query Expansion without Manual Effort:** LLM generates richer semantic content automatically.
    - **Better Retrieval Accuracy:** Hypothetical documents are often closer to relevant documents in embedding space than terse user queries.
    - **Works with Sparse Queries:** Short or ambiguous queries benefit from the additional context generated by the LLM.
      
<img width="460" height="300" alt="image" src="https://github.com/user-attachments/assets/564420f8-9bd2-4f97-9e68-0e0dbcfb02f4" />

## Query Routing
Query routing is a strategy used in retrieval or search systems to **direct a user query to the most relevant subset of data or service**. The goal is to improve retrieval efficiency, relevance, and response time. There are two main strategies: **logical routing** and **semantic routing**.

### 9. Logical Routing
Logical routing directs queries based on **predefined rules, categories, or metadata** associated with the documents or data sources.
- Documents are classified or tagged into **logical partitions** (e.g., departments, product lines, regions).  
- Queries are routed to the partition(s) that match certain **keywords, tags, or rules**.  

**Example:**  
```python
class RouteQuery(BaseModel):
    """Route a user query to the most relevant datasource."""
    datasource: Literal["python_docs", "js_docs"] = Field(
        ...,
        description="Choose the most relevant datasource based on keywords."
    )
```

---

### 10. Semantic Routing
Semantic routing directs queries based on **meaning or intent**, often using embeddings, LLMs, or vector similarity, rather than strict keywords or rules.

**How it works:**  
- Queries are converted into **semantic representations** (embeddings).  
- Documents or partitions are also represented in the same embedding space.  
- The system routes the query to the **most semantically relevant subset**.  

**Example:**  
```python
physics_template = """You are a very smart physics professor. You are great at     answering questions ... {query}"""
math_template = """You are a very good mathematician. You are great at answering     math questions. You a... {query}"""

# Embed prompts
embeddings = OpenAIEmbeddings()
prompt_templates = [physics_template, math_template]
prompt_embeddings = embeddings.embed_documents(prompt_templates)

# Route question to prompt
@chain
def prompt_router(query):
    query_embedding = embeddings.embed_query(query)
    similarity = cosine_similarity([query_embedding], prompt_embeddings)[0]
    most_similar = prompt_templates[similarity.argmax()]
    print("Using MATH" if most_similar == math_template else "Using PHYSICS")
    return PromptTemplate.from_template(most_similar)
```
**Use Cases:**  
- Logical routing: Enterprise databases, departmental knowledge bases, FAQs.  
- Semantic routing: RAG systems, multi-domain knowledge search, chatbots, recommendation engines.  

---

## Query Construction
 **user's natural language query** into **a structured query** that can be executed against various data sources. This is essential for retrieval systems, vector stores, and relational databases.

---

### 11. Text-to-Metadata Conversion

Vector stores often support **metadata-based filtering**, allowing more precise searches. During the embedding process:

- **Attach metadata**: Each vector can be associated with key-value pairs (e.g., document type, author, date)
- **Filter on query**: When performing a search, you can specify metadata filters to narrow down results

1. **Embed documents and attach metadata:**
   ```python
   {
       "text": "Annual financial report 2024",
       "embedding": [...],
       "metadata": {"year": 2024, "type": "report"}
   }
   ```

2. **Construct a query with metadata filters:**
   ```python
   query_embedding = embed("Financial summary 2024")
   results = vector_store.search(
       query_embedding,
       filter={"year": 2024, "type": "report"}
   )
   ```

---

## Self-Querying Approach
An LLM can translate a natural language query into metadata filters automatically.

**Example:** "Show event by event name and event publisher" → `{"event_name": "payment event", "event_publisher": "payment system"}`

---

### 12. Text-to-SQL
Relational databases require SQL queries, which are not naturally compatible with human language. Text-to-SQL allows LLMs to translate user queries into SQL queries.
* Strategies for Effective Translation
* Provide the LLM with table definitions using `CREATE TABLE` statements, including:
    - Column names and types
    - Sample rows (typically 2-3 examples)
    - Include question-to-SQL examples in the prompt to guide query generation. example - Q: **Total Adjustments applied in the month of August** and its answer
* Reserve a user for this purpose
* Preferrably only run it on Read only instance
* When SQL execution fails an agent can auto:
- Regenerate the query based on error messages
- Repair the existing query with corrections
- Run agent in a docker container

---

### 13. Additional Prompt & Agent Patterns
#### Chatbot with Memory
- Most LLM models are stateless; they do not remember previous interactions unless history is provided in the prompt.
- **Actions to control history:**
  - **Trimming:** Remove older messages to fit within context window.
  - **Filtering:** Exclude irrelevant or redundant exchanges.
  - **Merging consecutive messages:** Combine similar or repeated messages for brevity.
  - **Summarization:** Use LLMs to summarize long histories into concise context.
- **Memory Patterns:** 
  - Windowed memory (last N messages)
  - Summarized memory (summary of all prior conversation)
  - Hybrid (recent messages + summary)

#### 14. ReAct (Reason + Act)
- Combines reasoning steps with tool or function calls.
- Model alternates between generating thoughts and taking actions (API calls, tool invocation).
- Useful for multi-step tasks, retrieval, and automation.
<img width="1400" height="686" alt="image" src="https://github.com/user-attachments/assets/67a2124c-9270-440b-b222-5b70ab293266" />

#### 15. Reflexion
- Model reviews its own outputs, critiques, and iteratively improves answers.
- Can be used for self-correction, debugging, or refining reasoning.
<img width="2000" height="1322" alt="image" src="https://github.com/user-attachments/assets/e607b1f1-8b79-4ee6-99b2-596eea8d8693" />

#### 16. Human-in-the-Loop
- Allows human intervention in agent workflows:
  - **Resume:** Continue from a paused state.
  - **Restart:** Begin a new workflow or correct errors.
  - **Edit State:** Modify intermediate results or context.
  - **Fork:** Branch the workflow for alternative solutions or exploration.

### 17. Other Patterns
- **Agent Orchestration:** Coordinate multiple agents (LLMs, tools, humans) for complex workflows.
- **Meta-Prompting:** Use one LLM to generate or optimize prompts for another model.
- **Multi-Agent Collaboration:** Multiple agents work together, each with specialized roles or skills.
- **Stateful Agents:** Persist agent state across sessions for long-running tasks.
