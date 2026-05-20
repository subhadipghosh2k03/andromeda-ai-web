# Andromeda AI

> Enterprise-grade local AI workspace powered by FastAPI, Node.js, llama.cpp, GGUF models, and local inference pipelines. 

Andromeda AI is a secure, local-first AI platform designed to deliver production-grade LLM workflows entirely on local infrastructure.

The platform combines:

* FastAPI middleware orchestration
* Node.js runtime management
* llama.cpp inference execution
* GGUF model deployment
* Local document processing pipelines
* Client-side RAG workflows
* Dynamic AI Skill injection
* Streaming inference interfaces
* Secure remote access tunneling

All prompts, documents, embeddings, inference traces, and conversations remain under local control.

---

# Architecture Overview

```mermaid
flowchart TB

    subgraph LAN["Local AI Workspace"]

        subgraph CLIENT["Frontend Layer"]
            UI["Vanilla SPA"]
            JS["app.js"]
            CSS["style.css"]
            LS[("LocalStorage")]
        end

        subgraph API["FastAPI Gateway"]
            BACKEND["backend.py"]
            PARSER["Document Parser"]
            SKILLS[("Skills Registry")]
            CONFIG[("Model Registry")]
        end

        subgraph INFERENCE["Inference Runtime"]
            NODE["Node.js Runtime"]
            LLAMA["llama.cpp Server"]
            MODELS[("GGUF Models")]
        end

    end

    subgraph WAN["Remote Access"]
        ZROK["zrok Tunnel"]
        REMOTE["Remote Client"]
    end

    UI --> JS
    UI --> CSS
    JS <--> LS

    JS <--> BACKEND

    BACKEND <--> PARSER
    BACKEND <--> SKILLS
    BACKEND <--> CONFIG

    BACKEND <--> NODE
    NODE <--> LLAMA
    LLAMA <--> MODELS

    BACKEND <--> ZROK
    ZROK <--> REMOTE
```

---

# Request Lifecycle

## Interactive Chat and Local RAG Flow

```mermaid
sequenceDiagram
    autonumber

    actor User

    participant UI as Browser Client
    participant API as FastAPI Gateway
    participant NODE as Node.js Runtime
    participant LLM as llama.cpp

    User->>UI: Submit prompt and files

    alt Text File
        UI->>UI: Parse locally
    else PDF File
        UI->>API: POST /api/extract-text
        API->>API: Extract document text
        API-->>UI: Return parsed text
    end

    UI->>UI: Build token budget
    UI->>API: POST /v1/chat/completions

    API->>NODE: Forward payload
    NODE->>LLM: Start inference

    loop Stream Tokens
        LLM-->>NODE: Token stream
        NODE-->>API: Forward chunks
        API-->>UI: Stream packets
        UI-->>User: Render output
    end
```

---

# AI Skills Execution Flow

```mermaid
sequenceDiagram
    autonumber

    participant UI as Browser Client
    participant API as FastAPI Gateway
    participant NODE as Node.js Runtime
    participant LLM as llama.cpp Model

    Note over UI,LLM: AI Skills enabled

    UI->>API: Send payload with tools
    API->>NODE: Forward request
    NODE->>LLM: Start inference

    LLM-->>NODE: Tool Call read_skill(frontend-design)

    NODE-->>API: Forward tool event
    API-->>UI: Stream tool event

    UI->>API: GET /api/skills/frontend-design

    API->>API: Read SKILL.md
    API-->>UI: Return instructions

    UI->>API: Continue generation
    API->>NODE: Forward enriched context
    NODE->>LLM: Resume inference

    LLM-->>NODE: Specialized response
    NODE-->>API: Stream output
    API-->>UI: Render response
```

---

# System Directory Structure

```bash
Andromeda/
├── backend.py
├── server.js
├── models.json
├── requirements.txt
├── package.json
├── start_local.bat
├── start_remote.bat
├── update_models.py
├── public/
│   ├── index.html
│   ├── css/
│   │   └── style.css
│   └── js/
│       └── app.js
├── skills/
│   └── frontend-design/
│       └── SKILL.md
└── models/
    └── *.gguf
```

---

# Core Components

## FastAPI Gateway

`backend.py` acts as the orchestration and middleware layer between the frontend interface and the local inference runtime.

### Responsibilities

* OpenAI-compatible API proxying
* Streaming response forwarding
* Authentication enforcement
* Document ingestion
* AI Skill orchestration
* Runtime status management
* Model registry coordination

### Document Processing Pipeline

Supported formats include:

* PDF
* Markdown
* TXT
* CSV
* JSON
* Python
* JavaScript
* HTML

Extraction stack:

1. PyMuPDF
2. pdfplumber fallback
3. Encoding-safe text decoding

---

## Node.js Runtime Layer

`server.js` manages inference orchestration and communication with llama.cpp.

### Responsibilities

* Launching llama.cpp inference services
* Streaming token forwarding
* OpenAI-compatible payload normalization
* Runtime lifecycle monitoring
* Model process management

---

## Frontend Runtime

The frontend is implemented as a lightweight Vanilla JavaScript SPA.

### Features

* Streaming markdown rendering
* Syntax-highlighted code blocks
* Dynamic reasoning panels
* Persistent local state storage
* Token budget management
* Theme and UI state preservation

---

# Token Budgeting and Context Compression

Andromeda implements a custom context compression pipeline to prevent context overflow during long-running sessions.

Target context limit:

```txt
24,000 tokens ≈ 84,000 characters
```

---

## Compression Strategy

```mermaid
flowchart TD

    A["System Instructions"]
    B["Initial User Prompt"]
    C["Compressed Middle History"]
    D["Recent Conversation"]
    E["Final Payload"]

    A --> B --> C --> D --> E
```

The compression engine preserves:

* System instructions
* Initial task objectives
* Most recent conversational state

Older intermediary history is truncated first.

---

# API Reference

| Endpoint             | Method | Description                       |
| -------------------- | ------ | --------------------------------- |
| `/api/models`        | GET    | Returns available models          |
| `/api/models/load`   | POST   | Loads a selected model            |
| `/api/status`        | GET    | Returns runtime status            |
| `/api/extract-text`  | POST   | Extracts text from uploaded files |
| `/api/skills`        | GET    | Lists installed AI Skills         |
| `/api/skills/{name}` | GET    | Returns skill instruction content |
| `/v1/{path:path}`    | ALL    | OpenAI-compatible inference proxy |

---

# Runtime Modes

## Local Workspace Mode

```bash
start_local.bat
```

Launches:

* FastAPI gateway
* Node.js orchestration runtime
* llama.cpp inference backend
* Browser-based SPA interface

Default endpoint:

```txt
http://localhost:8080
```

---

## Secure Remote Access Mode

```bash
start_remote.bat
```

Creates a secure zrok tunnel for remote WAN access without exposing local ports directly.

Example endpoint:

```txt
https://your-instance.share.zrok.io
```

---

# Security Model

Andromeda follows a strict local-first execution model.

## Local Data Residency

The following remain local unless explicitly shared:

* prompts
* uploaded documents
* embeddings
* inference outputs
* reasoning traces
* conversation history

---

# Architectural Goals

The platform is designed around the following principles:

* Local-first execution
* Open inference standards
* Offline-capable workflows
* Modular orchestration
* Streaming-first UX
* Extensible AI Skills system
* OpenAI-compatible interoperability
* Minimal frontend overhead

---

# Future Expansion Areas

* Multi-agent orchestration
* Distributed inference routing
* Local vector database integration
* GPU-aware scheduling
* Voice interaction pipelines
* Plugin-based workspace extensions
* Collaborative local workspaces

---

# License

MIT License

---

# Closing Notes

Andromeda AI combines:

* FastAPI
* Node.js orchestration
* llama.cpp
* GGUF inference runtimes
* Local RAG pipelines
* Dynamic AI Skill injection
* Streaming reasoning interfaces

to provide a fully sovereign AI workspace architecture optimized for local inference environments.
