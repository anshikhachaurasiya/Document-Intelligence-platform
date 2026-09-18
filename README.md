# 📄 Document Intelligence Platform

> An enterprise-grade Agentic RAG platform for intelligent document question answering using LangGraph, LangChain, Qdrant, Gemini Embeddings, FlashRank, NeMo Guardrails, and RAGAS.

---

## 🖼️ Agentic Intelligence Flow

<!-- Add your architecture / agent intelligence flow image here -->

<p align="center">
  <img src="./docs/agentic-intelligence-flow.png" alt="Agentic Intelligence Flow" width="900"/>
</p>

> **Note:** Place your architecture image at:
>
> `docs/agentic-intelligence-flow.png`

---

## 🚀 Overview

The **Document Intelligence Platform** is a production-oriented Retrieval-Augmented Generation (RAG) system designed for intelligent question answering over enterprise documents.

Instead of using a simple:

```text
User Query
    ↓
Vector Search
    ↓
LLM
    ↓
Answer
```

pipeline, the platform uses an agentic workflow to determine how each query should be handled.

The system combines:
* Agentic query planning
* Document ingestion
* Semantic retrieval
* Vector search
* Semantic reranking
* Conversational memory
* LLM routing and fallback
* Prompt-injection protection
* Guardrails
* RAG evaluation
* Observability
* FastAPI backend
* Streamlit interface

## 🧠 System Architecture

```text
                           ┌───────────────────┐
                           │       User        │
                           │      Query        │
                           └─────────┬─────────┘
                                     │
                                     ▼
                           ┌───────────────────┐
                           │   Streamlit UI    │
                           └─────────┬─────────┘
                                     │
                                     ▼
                           ┌───────────────────┐
                           │    FastAPI API    │
                           └─────────┬─────────┘
                                     │
                                     ▼
                           ┌───────────────────┐
                           │  NeMo Guardrails  │
                           │ Input Validation  │
                           └─────────┬─────────┘
                                     │
                                  Allowed
                                     │
                                     ▼
                           ┌───────────────────┐
                           │ LangGraph Planner │
                           └─────────┬─────────┘
                                     │
                       ┌─────────────┴─────────────┐
                       │                           │
                       ▼                           ▼
                Conversational                Technical
                       │                           │
                       ▼                           ▼
                  Responder                   Retriever
                                                   │
                                                   ▼
                                            Qdrant Vector DB
                                                   │
                                                   ▼
                                              FlashRank
                                             Reranking
                                                   │
                                                   ▼
                                              Responder
                                                   │
                                                   ▼
                                            Final Answer
```

## ✨ Key Features

### 🤖 Agentic RAG
The platform uses LangGraph to orchestrate the RAG workflow.
The planner determines whether a query requires:
* Direct conversational response
* Retrieval from enterprise documents
* Context-aware response generation

This enables conditional execution instead of performing vector retrieval for every request.

### 📚 Multi-Format Document Ingestion
The platform supports ingestion of multiple document formats:
* PDF
* DOCX
* PPTX
* HTML
* TXT

Documents are processed through an ingestion pipeline before being converted into embeddings and stored in the vector database.

```text
Documents
    ↓
Document Loader
    ↓
Text Extraction
    ↓
Chunking
    ↓
Embedding Generation
    ↓
Qdrant
```

### 🔎 Retrieval Pipeline
The retrieval system uses a two-stage approach.

**Stage 1 — Vector Retrieval**
The user query is converted into an embedding and searched against the Qdrant vector database.
```text
User Query
    ↓
Gemini Embedding
    ↓
Qdrant
    ↓
Top-K Candidates
```

**Stage 2 — Semantic Reranking**
The retrieved candidates are passed through FlashRank.
```text
Top-K Candidates
       ↓
    FlashRank
       ↓
Reranked Context
       ↓
      LLM
```
This allows the system to refine the initial vector-search results before passing context to the language model.

## 🧩 Agentic Workflow
The core workflow consists of three major logical stages:

```text
                 ┌───────────────┐
                 │    Planner    │
                 └───────┬───────┘
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
       Conversational          Technical
              │                     │
              ▼                     ▼
         Responder              Retriever
                                    │
                                    ▼
                                Qdrant
                                    │
                                    ▼
                                FlashRank
                                    │
                                    ▼
                                Responder
```

* **Planner:** Determines how the query should be processed.
* **Retriever:** Searches the indexed enterprise knowledge base for relevant context.
* **Responder:** Uses the query, conversation history, and retrieved context to generate the final answer.

## 🛡️ Guardrails & Security
The platform incorporates NeMo Guardrails before the main RAG workflow.

```text
User Input
    ↓
Guardrails
    │
    ├── Blocked → Reject Request
    │
    └── Allowed
           ↓
        Planner
           ↓
        RAG Flow
```

The guardrail layer is designed to help protect the system against:
* Prompt injection
* Jailbreak attempts
* Irrelevant requests
* Unsafe inputs

This prevents untrusted user input from directly flowing into the retrieval and generation pipeline.

## 🧠 Conversational Memory
The platform maintains conversational state using LangGraph memory.
For example:
> **User:** What are the main components of the architecture?
> **Assistant:** The architecture consists of...
> **User:** Which component handles retrieval?
> **Assistant:** The Retriever handles...

The second query can be interpreted using the context of the previous interaction.

## 🔀 LLM Gateway & Fallback
LLM requests are routed through Portkey.
The system supports fallback API keys to improve reliability when the primary API key is unavailable or encounters a failure.

```text
                    Application
                         │
                         ▼
                     Portkey
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
          Primary               Fallback
          API Key               API Key
```
This provides an additional reliability layer around LLM inference.

## 📊 RAG Evaluation
Evaluation is treated as an integral part of the platform.
The project includes a RAGAS-based evaluation pipeline to assess the quality of retrieval and generated responses.

```text
Evaluation Dataset
        │
        ▼
   RAG Pipeline
        │
        ▼
Generated Responses
        │
        ▼
       RAGAS
        │
        ▼
Evaluation Metrics
        │
        ▼
Evaluation Results
```
The platform also includes a custom Tool Correctness evaluation based on Jaccard similarity.

## 🔬 Observability
The application integrates observability tools including:
* LangSmith
* Pydantic Logfire

These tools help trace the execution of the agentic workflow.

```text
User Query
    ↓
Guardrails
    ↓
Planner
    ↓
Retriever
    ↓
Qdrant
    ↓
FlashRank
    ↓
Responder
    ↓
Final Answer
```

This makes it easier to investigate:
* Retrieval failures
* Incorrect routing
* Poor responses
* Agent execution
* LLM calls
* Pipeline behavior

## 🏗️ Document Processing Pipeline
The ingestion pipeline transforms raw documents into searchable knowledge.

```text
                     Raw Documents
                           │
              ┌────────────┼────────────┐
              │            │            │
             PDF          DOCX        PPTX
              │            │            │
              └────────────┼────────────┘
                           │
                           ▼
                    Document Parser
                           │
                           ▼
                     Text Extraction
                           │
                           ▼
                        Chunking
                           │
                           ▼
                  Gemini Embeddings
                           │
                           ▼
                    Qdrant Vector DB
```

## 🛠️ Technology Stack

| Category | Technology |
| :--- | :--- |
| Language | Python |
| LLM Orchestration | LangGraph |
| LLM Framework | LangChain |
| LLM | Groq / Llama |
| LLM Gateway | Portkey |
| Embeddings | Gemini |
| Vector Database | Qdrant |
| Reranking | FlashRank |
| Guardrails | NeMo Guardrails |
| Backend | FastAPI |
| Frontend | Streamlit |
| Evaluation | RAGAS |
| Observability | LangSmith, Pydantic Logfire |
| Containerization | Docker |

## 📁 Project Structure
```text
8hr-MARATHON/
│
├── app/
│   ├── agents/
│   │   └── nodes/
│   │       ├── planner/
│   │       ├── retriever/
│   │       └── responder/
│   │
│   ├── gateway/
│   │
│   ├── guardrails/
│   │
│   ├── ingestion/
│   │   ├── chunking/
│   │   └── loaders/
│   │
│   ├── services/
│   │   └── retrieval/
│   │
│   ├── config.py
│   └── main.py
│
├── evals/
│
├── ui/
│
├── DATA/
│
├── processed_data/
│
├── docs/
│   └── agentic-intelligence-flow.png
│
├── Dockerfile
├── requirements.txt
├── requirements-prod.txt
├── .env.example
└── ARCHITECTURE.md
```

## 🚀 Getting Started

### Prerequisites
Install the following:
* Python 3.10+
* Git
* Qdrant account
* Gemini API key
* Groq API key
* Portkey API key
* LangSmith API key
* Pydantic Logfire token

### 1. Clone the Repository
```bash
git clone https://github.com/d-hackmt/8hr-MARATHON.git
cd 8hr-MARATHON
```

### 2. Create a Virtual Environment
**Windows**
```bash
python -m venv venv
venv\Scripts ctivate
```
**Linux / macOS**
```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```
For production dependencies:
```bash
pip install -r requirements-prod.txt
```

### 🔐 Environment Variables
Create a `.env` file in the project root.
```env
GROQ_API_KEY=
GROQ_FALLBACK_API_KEY=

PORTKEY_API_KEY=

QDRANT_API_KEY=
QDRANT_CLUSTER_ENDPOINT=

GEMINI_API_KEY=

LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT=https://api.smith.langchain.com
LANGSMITH_API_KEY=
LANGSMITH_PROJECT=

LOGFIRE_TOKEN=

BACKEND_URL=http://localhost:8000

JUDGE_GROQ=
```
> **Important:** Never commit API keys or `.env` files to GitHub.

### 📥 Ingest Documents
Place documents inside: `DATA/`
Supported formats: PDF, DOCX, PPTX, HTML, TXT

Run the ingestion pipeline:
```bash
python -m app.ingestion.processor DATA --wipe
```
*The `--wipe` option recreates the Qdrant collection before indexing the documents.*

### ▶️ Run the Application

**Start the FastAPI Backend**
```bash
uvicorn app.main:app --reload --port 8000
```
Backend: `http://localhost:8000`

**Start the Streamlit Frontend**
Open another terminal:
```bash
streamlit run ui/app.py
```
The Streamlit application provides the conversational interface for interacting with the Document Intelligence Platform.

### 🧪 Run Evaluation
Start the FastAPI backend first.
Then run:
```bash
streamlit run evals/app.py
```
This launches the evaluation interface for testing the RAG pipeline.

## 💬 Example Queries
| Type | Question |
| :--- | :--- |
| **Document Question** | What are the main components of the system architecture? |
| **Technical Question** | How does the retrieval pipeline work? |
| **Follow-up Question** | Which database is used for storing the embeddings? |
| **Architecture Question** | Explain the role of the reranking stage. |
| **Comparison** | What is the difference between vector retrieval and reranking? |

## 🔄 Complete Query Lifecycle

```text
                    USER QUERY
                        │
                        ▼
                ┌───────────────┐
                │   Guardrails  │
                └───────┬───────┘
                        │
                        ▼
                  ┌───────────┐
                  │  Planner  │
                  └─────┬─────┘
                        │
                ┌───────┴───────┐
                │               │
                ▼               ▼
         Conversational     Technical
                │               │
                │               ▼
                │          Query Embedding
                │               │
                │               ▼
                │            Qdrant
                │               │
                │               ▼
                │           FlashRank
                │               │
                └───────┬───────┘
                        │
                        ▼
                  ┌───────────┐
                  │ Responder │
                  └─────┬─────┘
                        │
                        ▼
                  Final Answer
```

## 🎯 Design Goals
The Document Intelligence Platform focuses on five major areas:

1. **Relevance:** Retrieve context that is actually useful for answering the user's question.
2. **Safety:** Prevent malicious or unsafe input from reaching the core RAG pipeline.
3. **Reliability:** Use LLM gateway routing and fallback mechanisms to improve inference reliability.
4. **Evaluation:** Measure RAG quality using automated evaluation instead of relying solely on manual inspection.
5. **Observability:** Trace agent execution and identify failures across different stages of the pipeline.

## 🔍 Why Agentic RAG?
A conventional RAG pipeline assumes every query needs retrieval.
The Document Intelligence Platform introduces a planning layer:

```text
                     User Query
                          │
                          ▼
                       Planner
                       /                           /                            ▼         ▼
            Conversational   Technical
                   │             │
                   ▼             ▼
               Responder     Retriever
                                 │
                                 ▼
                              Qdrant
                                 │
                                 ▼
                              Reranker
                                 │
                                 ▼
                              Responder
```
This allows retrieval to be executed conditionally based on the query.

## ⚡ Why Reranking?
Vector similarity is useful for retrieving candidate documents, but the highest similarity score does not necessarily guarantee the best context. Therefore, the platform uses:

```text
Vector Search
     ↓
Candidate Documents
     ↓
Semantic Reranking
     ↓
Relevant Context
     ↓
LLM
```
This separates candidate retrieval from final relevance ranking.

## 🛡️ Why Guardrails?
LLM applications are exposed to untrusted natural-language input.
The platform places guardrails before the agentic workflow:

```text
Untrusted Input
      ↓
Guardrails
      ↓
Validated Input
      ↓
Agentic RAG
```
This helps reduce the risk of prompt injection and jailbreak-style interactions.

## 📈 Production-Oriented Components
The project incorporates several components that are useful when moving from a basic RAG prototype toward a production-oriented AI system:
* Agent orchestration
* Vector retrieval
* Semantic reranking
* Conversational memory
* LLM gateway
* Fallback mechanisms
* Input/output guardrails
* API backend
* Evaluation pipeline
* Observability
* Docker support

## 🔮 Future Improvements
Potential extensions include:
* Hybrid BM25 + vector retrieval
* Query rewriting
* Multi-query retrieval
* Adaptive top-K retrieval
* Hallucination detection
* Automated answer verification
* Document-level access control
* Multi-tenant architecture
* Streaming responses
* Async document ingestion
* CI/CD automation
* Kubernetes deployment
* Automated prompt regression testing
* Continuous RAG evaluation

## 🤝 Contributing
Contributions are welcome.
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-feature`)
3. Commit your changes (`git commit -m "feat: add new feature"`)
4. Push the branch (`git push origin feature/new-feature`)
5. Open a Pull Request
 ## Data
This repo excludes the `DATA/` (raw noisy and true documents) and `processed_data/` 
(processed JSON outputs) folders to keep the repository lightweight. 

To run the pipeline locally, place your own documents inside `DATA/noisy_data/` 
and `DATA/true_data/`, then run the ingestion pipeline to generate `processed_data/`.

## 📜 License
This project is intended for educational and development purposes.

## 👨‍💻 Author
**Your Name- Anshikha Chaurasiya**
GitHub: https://github.com/anshikhachaurasiya

## ⭐ Acknowledgements
Built using open-source technologies and frameworks including:
* LangChain
* LangGraph
* Qdrant
* Google Gemini
* Groq
* FlashRank
* NeMo Guardrails
* RAGAS
* FastAPI
* Streamlit
* LangSmith
* Pydantic Logfire

## 📌 Project Summary
Document Intelligence Platform is an end-to-end Agentic RAG system that combines:

```text
             ┌──────────────────────┐
             │   Document Ingestion │
             └──────────┬───────────┘
                        │
                        ▼
             ┌──────────────────────┐
             │      Embeddings      │
             └──────────┬───────────┘
                        │
                        ▼
             ┌──────────────────────┐
             │    Vector Retrieval  │
             └──────────┬───────────┘
                        │
                        ▼
             ┌──────────────────────┐
             │      Reranking       │
             └──────────┬───────────┘
                        │
                        ▼
             ┌──────────────────────┐
             │   Agentic Reasoning  │
             └──────────┬───────────┘
                        │
                        ▼
             ┌──────────────────────┐
             │   Grounded Response  │
             └──────────────────────┘

      + Guardrails
      + Memory
      + Evaluation
      + Observability
      + LLM Routing
