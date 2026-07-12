# MeGPT

An agentic AI chatbot built with LangGraph and Google Gemini, featuring tool use, document RAG, web search, long-term memory, and real-time streaming — all in a clean ChatGPT-style interface.

---

## Features

- **Multi-Model Support** — Choose between Gemini 2.0 Flash, 2.0 Flash Lite, 3.5 Flash, and 3.1 Flash Lite
- **Agentic Tool Use** — The AI autonomously decides when to invoke tools based on user intent
- **Web Search** — Real-time internet search via Tavily API for current events and latest information
- **Document RAG** — Upload PDFs, DOCX, TXT, MD, PY, or CSV files and ask questions about them
- **Long-Term Memory** — Save and recall user preferences and facts across conversations
- **Calculator Tool** — Built-in math calculations for arithmetic expressions
- **Conversation History** — Persistent chat history with sidebar navigation and delete functionality
- **SSE Streaming** — Real-time token-by-token response streaming for instant feedback
- **Voice Dictation** — Speech-to-text input using the Web Speech API
- **Dark Theme UI** — Clean, responsive ChatGPT-style interface

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Backend** | Python, FastAPI, Uvicorn |
| **Frontend** | Vanilla HTML/CSS/JavaScript (Jinja2 templates) |
| **AI/LLM** | LangChain, LangGraph, Google Gemini API |
| **Vector Database** | ChromaDB (via langchain-chroma) |
| **Relational Database** | SQLAlchemy + SQLite |
| **Web Search** | Tavily Search API |
| **Document Parsing** | pypdf, docx2txt |
| **Streaming** | Server-Sent Events (SSE) |

---

## Architecture

```
User Browser (Vanilla JS)
        |
        | POST /chat/stream (SSE)
        v
  FastAPI Server (app.py)
        |
        | LangGraph StateGraph
        v
  Agent (agent.py)
   /          \
  v            v
Tools       LLM (Gemini)
(tools.py)
   |            |
   v            v
 Calculator   Web Search (Tavily)
 Doc Search   Memory Save/Recall
      |
      v
  ChromaDB (RAG)
```

The LangGraph agent orchestrates a simple graph: the chatbot node calls Gemini with tool bindings, and if the model requests a tool call, it routes to the tool node before returning to the chatbot for a final response.

---

## Prerequisites

- **Python 3.10+**
- A **Google Gemini API key** — Get one at [Google AI Studio](https://aistudio.google.com/apikey)
- A **Tavily API key** — Get one at [tavily.com](https://tavily.com) (free tier available)

---

## Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/SamritaNagarkar/MeGPT.git
cd MeGPT/MeGPT
```

### 2. Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate   # macOS/Linux
# venv\Scripts\activate    # Windows
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```bash
touch .env
```

Add your API keys:

```
GEMINI_API_KEY=your_google_gemini_api_key_here
TAVILY_API_KEY=your_tavily_api_key_here
```

### 5. Run the server

```bash
python app.py
```

The app will be available at **http://localhost:8081**.

---

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `GEMINI_API_KEY` | Yes | Google Gemini API key for LLM and embeddings |
| `TAVILY_API_KEY` | Yes | Tavily API key for web search functionality |
| `GEMINI_MODEL` | No | Override the default model (default: `gemini-2.0-flash`) |

---

## Usage

### Chat
Type a message in the input field and press **Enter** or click the send button. The AI will respond in real-time with streaming text.

### Switch Models
Use the model dropdown in the input bar to switch between Gemini models mid-conversation.

### Upload Documents
Click the attachment button to upload PDF, DOCX, TXT, MD, PY, or CSV files. Once uploaded, ask questions like "Summarize the document" or "What does the file say about X?"

### Use Tools
The agent automatically selects tools based on your message:
- **"Calculate 125 * 48 / 6"** — Triggers the calculator
- **"Search the web for latest AI news"** — Triggers Tavily web search
- **"Remember that my email is x@y.com"** — Saves to long-term memory
- **"What do you remember about me?"** — Recalls saved memories
- **"Summarize the uploaded PDF"** — Searches documents via RAG

### Voice Input
Click the microphone button to dictate messages using your browser's speech recognition.

### Manage Conversations
- **New Chat** — Click "+ New chat" in the sidebar
- **Switch Chat** — Click any conversation in the sidebar
- **Delete Chat** — Click the trash icon next to any conversation

---

## Project Structure

```
MeGPT/
├── app.py              # FastAPI server, routes, SSE streaming
├── agent.py            # LangGraph agent definition, model config
├── tools.py            # 5 agent tools (calculator, search, memory, etc.)
├── rag.py              # Document parsing, ChromaDB ingestion, retrieval
├── database.py         # SQLAlchemy models and CRUD operations
├── test.py             # Simple test script
├── requirements.txt    # Python dependencies
├── .env                # Environment variables (not committed)
├── .gitignore          # Git ignore rules
├── templates/
│   └── index.html      # Full chatbot UI (HTML + CSS + JS)
├── data/
│   └── chatbot_memory.db   # SQLite database
├── chroma_db/
│   └── chroma.sqlite3      # Vector store
└── uploads/                # Uploaded documents
```

---

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Serve the chatbot UI |
| `GET` | `/conversations` | List all conversations |
| `DELETE` | `/conversations/{thread_id}` | Delete a conversation and its data |
| `GET` | `/history/{thread_id}` | Get chat history for a conversation |
| `POST` | `/chat/stream` | Send a message and receive SSE stream |
| `POST` | `/upload` | Upload a document for RAG |

---

## License

MIT
