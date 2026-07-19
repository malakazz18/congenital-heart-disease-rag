# Congenital Heart Disease RAG Assistant — n8n Mini Project

An AI assistant built in n8n that answers questions about congenital heart disease using
Retrieval-Augmented Generation (RAG). Users upload PDFs through an n8n Form, the content is
chunked and embedded into a Qdrant vector store, and a chat interface retrieves relevant
context to answer user questions grounded in the source documents.

## Design Choices

The pipeline chunks source PDFs with a **Recursive Character Text Splitter** (chunk size:
**1000 characters**, overlap: **~100 characters**) to balance
retaining enough context per chunk while preserving continuity across chunk boundaries.
Text is embedded using OpenAI's **text-embedding-3-small** model (**1024-dimensional**
vectors) and stored in a **Qdrant** vector database. At query time, the user's question is
embedded and matched against stored vectors via Qdrant's **similarity search** (top-k
retrieval, default k=4), returning the most relevant chunks as context. This context is
injected into a system prompt sent to **gpt-4o-mini**, which generates the final answer.
Generation settings were left at n8n/OpenAI defaults (**temperature: 0.7, top_p: 1**).

## Pipeline Overview

```
Form Trigger (multi-PDF upload)
   → Code node (splits binary properties into separate items)
   → Extract from File (PDF text extraction)
   → Default Data Loader + Recursive Character Text Splitter (chunking)
   → Embeddings OpenAI (text-embedding-3-small)
   → Qdrant Vector Store (ingestion)

Chat Trigger (user question)
   → Qdrant Vector Store (retrieval, top-k similarity search)
   → Edit Fields (extract document.pageContent → context)
   → Aggregate (combine chunks into one array)
   → Basic LLM Chain (gpt-4o-mini, system prompt + retrieved context)
   → Chat response
```

## Chunking Strategy

| Setting | Value |
|---|---|
| Algorithm | Recursive Character Text Splitter |
| Chunk size | 1000 characters |
| Chunk overlap | ~100 characters  |

## Embedding Model

| Setting | Value |
|---|---|
| Model | `text-embedding-3-small` (OpenAI) |
| Dimensions | 1024 |

## Retrieval Method

Similarity search against the Qdrant vector store, returning the top-k most relevant chunks
(default k=4) based on cosine similarity between the query embedding and stored document
embeddings.

## Generation Settings

| Setting | Value |
|---|---|
| Model | `gpt-4o-mini` |
| Temperature | 0.7 (default) |
| top_p | 1 (default) |
| top_k | N/A (not used by OpenAI Chat Completions API) |

## System Prompt

```
You are a congenital heart disease specialist assistant. Use the following retrieved context
to answer the user's question accurately. If the answer isn't in the context, say so.

Context:
{{ $json.context.join("\n\n---\n\n") }}
```

## Documents Used

Four reference documents on congenital heart disease and related cardiac conditions were
ingested into the knowledge base:

1. **Congenital Heart Defect** (Wikipedia) — overview of CHD types, causes, genetics,
   diagnosis (including newborn pulse oximetry screening), classification, and treatment.
2. **What Is Congenital Heart Disease?** (Cleveland Clinic) — patient-facing explainer
   covering cyanotic vs. acyanotic CHD, symptoms, causes, complications, diagnosis, and
   treatment/prognosis.
3. **Cardiomyopathy** (Wikipedia) — overview of hypertrophic, dilated, restrictive, and
   other cardiomyopathy types, causes, symptoms, diagnosis, and classification.
4. **Atrial Septal Defect** (Wikipedia) — detailed breakdown of ASD types (ostium secundum,
   primum, sinus venosus), patent foramen ovale, complications, diagnosis, and surgical vs.
   catheter-based treatment.


- This is an educational prototype, not a medical device — outputs should not be used for
  clinical decision-making.
