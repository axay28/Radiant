# Radiant Architecture

Radiant is a retrieval-augmented generation system for rare disease question answering. This repository contains an earlier open-source implementation of the research prototype; the deployed production system at `radiant.rtx.ai` is a separate React application with authenticated and guest chat flows.

## Open-Source Prototype In This Repository

```text
Angular chat UI
    |
    | POST /get_response
    v
Flask API
    |
    v
Query processing pipeline
    |
    +-- abbreviation expansion and query paraphrasing with LangChain/OpenAI
    +-- OpenAI text-embedding-3-large query embeddings
    +-- Chroma vector retrieval from vector-store-rare-diseases1
    +-- MaxSim-style reranking over retrieved embeddings
    +-- GPT-4o and/or Llama 3 generation
    +-- source URL extraction from document metadata
```

### Backend

- `app.py` exposes the Flask API used by the Angular frontend.
- `query_processing/query_processor.py` coordinates the RAG pipeline.
- `query_processing/query_expansion.py` expands abbreviations and paraphrases queries with LangChain and OpenAI.
- `query_processing/database_retrieval.py` embeds queries with OpenAI and retrieves matching documents from Chroma.
- `query_processing/reranker.py` reranks retrieved passages using a MaxSim-style score.
- `query_processing/generation.py` generates answers with GPT-4o and Llama 3 through Amazon Bedrock.
- `query_processing/summarizer.py` compresses prior answers into conversation context.

### Frontend

- `frontend/` is an Angular 18 chat interface for the open-source prototype.
- The app posts to the local Flask backend at `http://localhost:5000/get_response`.
- The current live site is not this Angular app. The live `radiant.rtx.ai` bundle is a Vite/React 18 application using React Router, Chakra UI, Clerk authentication, and APIs under `https://radiant.rtx.ai/llm`.

## Data Layer

The public prototype expects a running Chroma server on `localhost:8000` and a collection named `vector-store-rare-diseases1`.

The collection should contain:

- document text chunks
- vector embeddings compatible with the retrieval pipeline
- metadata with a `URL` field when source citations are available

This repository does not include the Chroma database, source corpus, or a SQLite database file. If chat-history persistence exists in the production system, it is not represented in this open-source checkout.

## Production/Published Project Context

The Springer AIME 2025 paper describes Radiant as a chat agent using LLMs, retrieval-augmented generation, a domain-specific vector database, clickable primary-source references, and reranking to reduce standalone LLM inaccuracies.

Resume/email source-of-truth notes from the project describe a broader system using LLaMA 3.1 405B, OpenAI embeddings, ChromaDB, roughly 50,000 curated articles, 20 rare genetic diseases, AWS deployment, and latency optimizations with ONNX and FP16 quantization. Those details reflect the broader Radiant work and should not be confused with the smaller open-source prototype in this repository.

## Medical Safety

Radiant is a research prototype and clinical decision-support exploration tool. It is not a medical device and should not be used as a substitute for qualified medical advice, diagnosis, or treatment.
