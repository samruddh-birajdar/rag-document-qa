# RAG Document Question Answering

A Retrieval-Augmented Generation (RAG) app that answers questions about a PDF using **Gemini 3.8 Flash**, with **page-level source citations** and a **hallucination guardrail**.

Upload a PDF, ask a question, get an answer grounded **only** in the document, and see exactly which pages the answer came from.

![RAG demo](docs/demo.png)

## Features

- **PDF ingestion** — extracts text page by page with PyPDF2
- **Page-aware chunking** — every chunk remembers which page it came from, so answers can cite sources
- **Sentence-Transformers embeddings** — `all-MiniLM-L6-v2` for fast, high-quality vector search
- **FAISS vector store** — in-memory similarity search
- **Gemini 3.8 Flash** — state-of-the-art LLM for accurate, reasoned answers (with automatic retry on API limits)
- **Similarity threshold guardrail** — if the retrieved context isn't relevant enough, the app refuses to answer instead of hallucinating
- **Tunable retrieval** — chunk size, overlap, top-K, and threshold exposed in an "Advanced Settings" menu

## Tech Stack

`Python` · `Streamlit` · `PyPDF2` · `sentence-transformers` · `FAISS` · `Gemini API` · `Google GenAI SDK`

## How It Works

1. Extract text page by page from the uploaded PDF
2. Chunk each page (configurable size and overlap), tagging each chunk with its source page
3. Embed chunks with Sentence-Transformers
4. Store vectors in FAISS
5. User asks a question
6. Embed the question and retrieve top-K similar chunks
7. **Apply similarity threshold** — if the best chunk is too weak, return "I don't know"
8. Build prompt with page-tagged context
9. Gemini generates the answer using *only* the retrieved context
10. Return the answer with source page numbers

## Run Locally

### 1. Get a Gemini API Key
Go to [Google AI Studio](https://aistudio.google.com/apikey) and create a free API key.

### 2. Install Python dependencies
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt