# 🫀 Congenital Heart Disease RAG Assistant

> A Retrieval-Augmented Generation (RAG) assistant that answers questions about congenital heart disease using information retrieved from reference documents.

## ✨ Overview

This project explores how Retrieval-Augmented Generation can be used to build a domain-specific AI assistant.

Instead of relying only on the language model's internal knowledge, the application retrieves relevant passages from a curated document collection and provides them to the LLM as context before generating an answer.

**Important:** This is an educational prototype and is **not a medical device**. It must not be used for diagnosis or clinical decision-making.

## 🧠 How It Works

```text
                    DOCUMENT INGESTION
                           │
                           ▼
                    PDF Documents
                           │
                           ▼
                    Text Extraction
                           │
                           ▼
                 Recursive Chunking
                           │
                           ▼
                   OpenAI Embeddings
                           │
                           ▼
                    Qdrant Vector DB
                           │
                           │
                    USER QUESTION
                           │
                           ▼
                   Query Embedding
                           │
                           ▼
                Similarity Search (Top-k)
                           │
                           ▼
                  Retrieved Context
                           │
                           ▼
                     GPT-4o-mini
                           │
                           ▼
                    Final Response
```

## 🛠️ Tech Stack

| Technology     | Purpose                   |
| -------------- | ------------------------- |
| n8n            | Workflow orchestration    |
| OpenAI         | Embeddings + LLM          |
| Qdrant         | Vector database           |
| RAG            | Retrieval architecture    |
| GPT-4o-mini    | Response generation       |
| Docker         | Containerized environment |
| PDF extraction | Document ingestion        |

## 🔍 RAG Pipeline

### 1. Document ingestion

Reference PDFs are uploaded through an n8n form.

### 2. Text processing

Documents are extracted and split using a Recursive Character Text Splitter.

* Chunk size: `1000`
* Chunk overlap: `~100`

### 3. Embeddings

Text chunks are converted into vector embeddings using:

`text-embedding-3-small`

### 4. Vector storage

Embeddings are stored in Qdrant.

### 5. Retrieval

When a user asks a question, the query is embedded and compared against the stored vectors.

The system retrieves the most relevant chunks.

### 6. Generation

The retrieved context is passed to GPT-4o-mini, which generates the final answer.

## 📚 Knowledge Base

The prototype uses four reference documents covering:

* Congenital heart disease
* Cardiomyopathy
* Atrial septal defects
* Patient-oriented CHD information

## ⚙️ Configuration

The workflow is provided as n8n JSON exports.

You will need:

* n8n
* OpenAI API credentials
* Qdrant
* The required reference documents

## 📸 Demo

<img width="505" height="356" alt="Capture d&#39;écran 2026-07-19 151046" src="https://github.com/user-attachments/assets/cb36c0da-4079-4706-9efd-b004b8870c61" />
<img width="837" height="323" alt="Capture d&#39;écran 2026-07-19 135827" src="https://github.com/user-attachments/assets/d688add5-28d5-4549-9324-7ffb5cb077af" />
<img width="1712" height="633" alt="image" src="https://github.com/user-attachments/assets/7d5477da-2ac8-4d75-b363-6c3079a99149" />



##  What I Learned

* RAG architecture
* Document ingestion
* Text chunking
* Embeddings
* Vector similarity search
* Vector databases
* Prompt construction
* Workflow orchestration with n8n
* Docker-based environments

##  Future Improvements

* Add citation/source retrieval to responses
* Add RAG evaluation metrics
* Compare different chunking strategies
* Experiment with different embedding models
* Add conversation memory
* Build a dedicated frontend
* Deploy the complete application

## ⚠️ Disclaimer

This project is an educational AI prototype. It is not intended for medical diagnosis, treatment, or clinical decision-making.
