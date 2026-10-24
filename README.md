# SIH 26117 — Sovereign On-Premise Agentic AI Workbench

**Organisation:** Mangalore Refinery and Petrochemicals Limited (MRPL)  
**Category:** Software · Smart India Hackathon

---

## Overview

A fully on-premise, air-gap-compatible AI workbench that lets industrial staff query **confidential internal documents** using open-weight LLMs — no data ever leaves the local server.

## Architecture

```
User (Browser)
     |
     v
Streamlit UI  (app.py)
     |
     |-- Auth gate (login page)
     |
     +-- Sidebar: model select | PDF upload | source filter | session mgmt
     |
     +-- Tab: Chat
     |        |
     |        +-- Vision path:  image + question --> Ollama (stream)
     |        |
     |        +-- RAG path:
     |                Planner Agent (Ollama)
     |                     |
     |                Researcher Agent (Ollama + ChromaDB)
     |                     |
     |                Answer Agent (Ollama, streamed token by token)
     |
     +-- Tab: Documents (list, ingest, delete)
     +-- Tab: Status    (Ollama / ChromaDB health)
     +-- Tab: Audit Log (local JSONL, downloadable)
     +-- Tab: About

Persistent storage (100% local):
  data/raw_pdfs/     -- uploaded documents
  data/chroma_db/    -- vector embeddings (ChromaDB)
  data/sessions.db   -- chat sessions (SQLite)
  data/audit_log.jsonl -- audit trail
```

## Feature Checklist

| Feature | Status |
|---|---|
| PDF upload and ingestion (chunked, embedded) | Done |
| Semantic search with ChromaDB | Done |
| Multi-agent pipeline (Planner, Researcher, Answerer) | Done |
| Streaming responses (token-by-token) | Done |
| System prompt injected into every LLM call | Done |
| Source/document filtering | Done |
| Image/vision support (multimodal) | Done |
| Chat with persistent session history (SQLite) | Done |
| Multiple sessions per user | Done |
| User login and authentication | Done |
| Delete documents from vector store via UI | Done |
| Health / status page | Done |
| Audit trail (local JSONL, downloadable) | Done |
| .env config file | Done |
| Docker + docker-compose deployment | Done |
| 100% on-premise, zero cloud calls | Done |

---

## Quick Start (Local)

### 1. Install Ollama
Download from https://ollama.com and pull a model:
```bash
ollama pull gemma2:2b
```

### 2. Install Python dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure
```bash
copy .env.example .env
# Edit .env to set your USERS, DEFAULT_MODEL, etc.
```

### 4. Run
```bash
streamlit run app.py
```

Open http://localhost:8501 — default login: `admin` / `admin123`

---

## Docker Deployment

```bash
# Build and start everything (Ollama + Workbench)
docker-compose up --build

# Pull a model into the Ollama container
docker exec -it sih_ollama ollama pull gemma2:2b
```

Open http://localhost:8501

All data persists in `./data/` on the host.

---

## File Structure

```
SIH/
├── app.py                  -- Streamlit web UI
├── main.py                 -- CLI entry point
├── ingest.py               -- Batch PDF ingestion CLI
├── requirements.txt
├── .env.example            -- Configuration template
├── Dockerfile
├── docker-compose.yml
├── README.md
│
├── src/
│   ├── config.py           -- Central config (reads .env)
│   ├── auth.py             -- Username/password auth
│   ├── session_store.py    -- SQLite chat session storage
│   ├── rag.py              -- Multi-agent RAG pipeline
│   ├── chat.py             -- Ollama LLM wrapper
│   ├── embed.py            -- Sentence-transformers embeddings
│   ├── extract.py          -- PDF text extraction
│   ├── chunker.py          -- Text chunking
│   ├── vectorstore.py      -- ChromaDB interface
│   └── instructions.py     -- System prompt
│
└── data/
    ├── raw_pdfs/           -- Uploaded PDFs and images
    ├── chroma_db/          -- Vector store (auto-created)
    ├── sessions.db         -- Chat sessions (auto-created)
    └── audit_log.jsonl     -- Audit trail (auto-created)
```

---

## Recommended Models

| Model | Use case | Command |
|---|---|---|
| `gemma2:2b` | Fast, low memory | `ollama pull gemma2:2b` |
| `gemma3:4b` | Vision + text | `ollama pull gemma3:4b` |
| `llava:7b` | Dedicated vision | `ollama pull llava:7b` |
| `llama3.2:3b` | General purpose | `ollama pull llama3.2:3b` |
| `qwen2.5:7b` | Strong reasoning | `ollama pull qwen2.5:7b` |

---

## Default Credentials

Set in `.env` via the `USERS` variable:
```
USERS=admin:admin123,engineer:eng456
```

[//]: # ()
[//]: # (**Change these before any production/demo deployment.**)
