# Document RAG Agent API (FAISS + Groq + FastAPI)

A lightweight Retrieval-Augmented Generation (RAG) system that lets you ask questions about a PDF document.  
It builds embeddings, indexes chunks in FAISS, and uses a Groq-hosted LLM to answer—then exposes everything via a FastAPI endpoint.

## What this project includes
- PDF text extraction + chunking
- Embeddings with `sentence-transformers`
- Vector search with FAISS
- RAG answering with Groq (Llama)
- A simple “tool-using” agent that can:
  - search passages
  - answer via RAG
  - summarize
  - extract numeric facts
  - compare numbers
  - generate business insights
- FastAPI server with one endpoint: `POST /ask` returning the answer + an execution trace

---

## Quickstart

### 1) Install
```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# Mac/Linux:
source .venv/bin/activate

pip install -r requirements.txt
2) Set environment variables
Create a .env file:

env
Copy code
GROQ_API_KEY=your_groq_key_here
3) Add a PDF
Place your PDF in the project folder (example: data/report.pdf).

Note: if your code still uses Colab upload, replace that part with a local file path (e.g., pdf_path = "data/report.pdf").

4) Run the API
bash
Copy code
uvicorn main:app --reload --port 8000
API Usage
Ask a question
Request

bash
Copy code
curl -X POST "http://127.0.0.1:8000/ask" \
  -H "Content-Type: application/json" \
  -d "{\"question\":\"Summarize the main points of the document.\"}"
Response

json
Copy code
{
  "answer": "...",
  "trace": [
    {
      "tool": "search_docs",
      "input": "...",
      "observation": "..."
    }
  ]
}
answer: final response

trace: step-by-step tool calls the agent used (helpful for debugging + demos)

How it works (high level)
Extract text from PDF

Split into overlapping chunks

Embed each chunk

Store embeddings in FAISS for similarity search

For a question:

retrieve top-k chunks

build a context prompt

call the LLM to answer grounded on retrieved context

Agent mode repeats steps using tools until it produces a final answer

Configuration
You can tune:

chunk size + overlap

top-k retrieval

LLM model name (Groq)

max agent steps

Suggested repo structure (optional)
If you want to make it more “production-looking” later:

css
Copy code
.
├── main.py
├── requirements.txt
├── README.md
├── .env.example
├── .gitignore
└── data/
    └── report.pdf
