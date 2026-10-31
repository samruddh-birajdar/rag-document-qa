# RAG Document Question Answering

A Retrieval-Augmented Generation (RAG) app that answers questions about a PDF using a local LLM, with page-level source citations.

Upload a PDF, ask a question, get an answer grounded only in the document, and see which pages the answer came from.

## Features

- PDF ingestion: extracts text page by page with PyPDF2
- Page-aware chunking: every chunk remembers which page it came from, so answers can cite sources
- Sentence-Transformers embeddings: all-MiniLM-L6-v2 for fast, high-quality vectors
- FAISS vector store: in-memory similarity search
- Local LLM: Llama 3.2 (1B) via Ollama; no API keys, no cloud cost
- Tunable retrieval: chunk size, overlap, and top-K exposed as sliders in the sidebar
- Source citations: answers include the page numbers used as context

## Tech Stack

Python, Streamlit, PyPDF2, sentence-transformers, FAISS, Ollama, Llama 3.2

## How It Works

1. Extract text page by page from the uploaded PDF
2. Chunk each page (configurable size and overlap), tagging each chunk with its source page
3. Embed chunks with Sentence-Transformers
4. Store vectors in FAISS
5. User asks a question
6. Embed the question
7. Retrieve top-K similar chunks
8. Build prompt with page-tagged context
9. Llama 3.2 generates answer
10. Return answer with source page numbers

## Run Locally

1. Install Ollama and pull a model:
   curl https://ollama.com/install.sh | sh
   ollama pull llama3.2:1b

2. Make sure Ollama is running:
   ollama serve

3. Install Python dependencies:
   python3 -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt

4. Run the app:
   streamlit run app.py

Open the URL that Streamlit prints (usually http://localhost:8501).

## Usage

1. Upload any PDF
2. Adjust chunk size, overlap, or top-K in the sidebar (optional)
3. Ask a question
4. Read the answer; the page numbers used are shown below

## Design Decisions

Why page-aware chunking? Plain chunking loses source information. By tagging each chunk with its page, answers can cite sources, which matters for any real document-assistant use case such as contracts, papers, or reports.

Why local Llama 3.2 via Ollama? No API cost, no data leaving the machine, and full control over the prompt. Good for privacy-sensitive documents.

Why FAISS? Fast in-memory similarity search with no external service. Good enough for a single-document Q&A use case. The architecture would swap in Chroma or Pinecone for multi-tenant scale.

## Attribution

Initial pipeline structure based on a Marvellous Infosystems RAG course. Extended with:

- Page-aware chunking and source citations
- Configurable chunk size, overlap, and top-K in the UI
- Cleaner prompt engineering with page labels
- Full rewrite and refactor for readability

## License

MIT