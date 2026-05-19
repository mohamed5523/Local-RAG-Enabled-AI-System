# 🧠 LLM_AI - Local RAG-Enabled AI System

> A **commercial-grade, fully offline AI research and development platform** combining **Ollama**, **Milvus Standalone**, and **FastAPI** for secure, on-premises LLM inference with deep semantic document retrieval.

---

<p align="center">
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="License: MIT">
  <img src="https://img.shields.io/badge/Python-3.11+-blue.svg" alt="Python 3.11+">
  <img src="https://img.shields.io/badge/Docker-Supported-blue.svg" alt="Docker Supported">
  <img src="https://img.shields.io/badge/Ollama-Accelerated-orange.svg" alt="Ollama Accelerated">
  <img src="https://img.shields.io/badge/Milvus-Vector%20DB-cyan.svg" alt="Milvus Vector DB">
  <img src="https://img.shields.io/badge/FastAPI-RAG%20Server-green.svg" alt="FastAPI RAG Server">
</p>

---

## 🎯 Features

### ✨ Core System Capabilities
* 🤖 **Local LLM Inference Engine**: Run advanced models like Qwen and DeepSeek locally without external API costs or internet reliance.
* 🔍 **RAG (Retrieval-Augmented Generation)**: Automatically retrieve deep, contextual document references to answer complex internal queries.
* 📚 **Multi-Format Ingestion**: Ingest and process standard text, Markdown, codebases, PDFs, and Microsoft Word DOCX files.
* 🔐 **100% Offline & Private**: Zero cloud telemetry, no data egress, and perfect for highly sensitive documents or enterprise policies.
* 💾 **Fast Similarity Search**: Powered by Milvus, etcd, and MinIO for rapid sub-millisecond semantic similarity lookup.
* 🎨 **ChatGPT-Like Web Interface**: Equipped with OpenWebUI for a modern, multi-user chat, document management, and model selector.
* 🧩 **VS Code Integration**: Send code snippets, generate tests, and review syntax directly from your editor.
* ⌨️ **Powerful Interactive CLI**: A rich terminal companion supporting direct prompts, live RAG toggle, model switches, and health checks.

---

## 🏗️ System Architecture

The platform operates on a microservice architecture orchestrated through Docker, interacting with your host-level Ollama service for hardware-accelerated model executions:

```
                  ┌───────────────────────────────┐
                  │       CLIENT INTERFACES       │
                  │  CLI  │  Web UI  │  VS Code   │
                  └───────────────┬───────────────┘
                                  │
                       [ HTTP REST Requests ]
                                  │
                                  ▼
                  ┌───────────────────────────────┐
                  │       FASTAPI RAG GATEWAY     │
                  │       ( Port 8001 )           │
                  └──────┬────────┬────────┬──────┘
                         │        │        │
      [ Get Embeddings ] │        │        │ [ Vector Search ]
                         ▼        │        ▼
                  ┌────────────┐  │  ┌─────────────────────────────┐
                  │  OLLAMA    │  │  │  MILVUS STANDALONE CLUSTER  │
                  │  (Host)    │  │  │  ( Port 19530 )             │
                  │  nomic-    │  │  └──────────────┬──────────────┘
                  │  embed     │  │                 │
                  └────────────┘  │                 ├─► etcd (Schemas)
                                  │                 │
                                  │                 └─► MinIO (Blobs)
                                  ▼
                  ┌───────────────────────────────┐
                  │   PERSISTENT UPLOADS / DATA   │
                  │   data/uploads  │ data/milvus │
                  └───────────────────────────────┘
```

---

## 🚀 Quick Start Guide

Deploy the complete offline platform on your machine in 5 steps:

### 1. Clone the Codebase & Initialize Settings
```bash
git clone https://github.com/yourusername/LLM_AI.git
cd LLM_AI

# Setup your local environment file
cp .env.example .env
```
*(Optionally open `.env` and update the passwords for production).*

### 2. Download and Run Ollama
1. Download Ollama from the [Official Download Page](https://ollama.com).
2. Install and launch the application.
3. Open a terminal and pull the models:
   ```bash
   # Pull text embedding model
   ollama pull nomic-embed-text
   
   # Pull optimized code assistant model
   ollama pull qwen2.5-coder:7b-instruct-q5_K_M
   ```

### 3. Deploy Docker Containers
Navigate to the orchestration folder and launch the services:
```bash
cd docker
docker-compose up -d
```
*Wait 2 minutes for the database cluster to initialize.*

### 4. Verify System Connections
Run the CLI connection checker from the root directory:
```bash
cd ..
python ai.py --test
```
*Expected Output:*
```
🔍 Testing connection to http://localhost:11434...
✅ Connected! Available models: 2
```

### 5. Access Dashboards
Open these URLs in your web browser:
* 💬 **Chat Web UI**: [http://localhost:8080](http://localhost:8080)
* 📊 **Database Viewer (Attu)**: [http://localhost:8000](http://localhost:8000) (Connect to: `milvus-standalone:19530`)
* 🔌 **REST API Documentation**: [http://localhost:8001/docs](http://localhost:8001/docs)

---

## ⌨️ Command Line Interface (CLI) Usage

The `ai.py` CLI provides immediate terminal access to your AI engine.

### Run Single Prompts
```bash
# General query
python ai.py "Explain quantum computing in simple terms"

# Write code
python ai.py code -l python "A recursive fibonacci generator"
```

### Review, Explain, & Test Source Files
```bash
# Deep explain
python ai.py explain -l python -f my_script.py

# Check for bugs & security issues
python ai.py review -l python -f my_script.py

# Auto-generate unit tests
python ai.py test -l python -f my_script.py
```

### Interactive RAG Shell
Launch a continuous shell with live commands:
```bash
python ai.py interactive
```
* **`/rag on` / `/rag off`**: Toggle semantic document ingestion.
* **`/model deepseek`**: Switch model targets instantly.
* **`/search <keyword>`**: Search vector chunks in the database.
* **`exit`**: Terminate shell.

---

## 🔌 API Requests Reference

The FastAPI RAG service supports automated pipelines:

### Upload Document
```bash
curl -X POST http://localhost:8001/upload \
  -H "X-API-Key: cai_your_api_key" \
  -F "file=@technical_spec.pdf" \
  -F "doc_type=architecture"
```

### Query Context
```bash
curl -X POST http://localhost:8001/search \
  -H "Content-Type: application/json" \
  -H "X-API-Key: cai_your_api_key" \
  -d '{"query": "database ports", "top_k": 3}'
```

---

## 🐳 Docker Services Matrix

| Service Name | Default Port | Internal Role | Health Verification Endpoint |
|:---|:---|:---|:---|
| **open-webui** | `8080` | Multi-user ChatGPT chat UI | `http://localhost:8080/health` |
| **milvus-attu** | `8000` | Milvus Database Management GUI | `http://localhost:8000` |
| **rag-api** | `8001` | Document parser & vectorizer | `http://localhost:8001/health` |
| **milvus-standalone** | `19530` | Vector similarity engine | `http://localhost:9091/healthz` |
| **milvus-minio** | `9000` | Local S3 object store | `http://localhost:9000/minio/health/live` |

---

## 🛡️ Security & Best Practices

1. **API Key Generation**: Never hardcode API keys. Generate keys using python's `secrets` module:
   ```bash
   python -c "import secrets; print('cai_' + secrets.token_urlsafe(32))"
   ```
2. **Access Control**: Keep `WEBUI_AUTH=true` in `.env` to prevent unauthorized network clients from interacting with your system.
3. **Environment Files**: Add `.env` to your server's global ignores. Never commit real keys or database credentials to a shared repository.
4. **GPU Accelerations**: Ensure Docker and Ollama have access to CUDA or Metal drivers to accelerate processing for large files.

---

## 📖 Extended Documentation

Dive deeper into the sub-modules and design layers under the `docs/` folder:
* ⚙️ **[Installation & Networks Setup](./docs/SETUP.md)**: Thorough setup instructions for Docker, Ollama, and local network sharing.
* 🔌 **[API Schemas Guide](./docs/API_DOCUMENTATION.md)**: Details on all REST endpoints, requests, responses, and authorization roles.
* 🏗️ **[System Architecture & Design](./docs/ARCHITECTURE.md)**: Diagrams and descriptions of the RAG ingestion pipelines and Milvus collection layouts.
* 🤝 **[Contributing Guidelines](./CONTRIBUTING.md)**: Instructions for setting up development files, Pytest structures, and PR processes.

---

## 🛠️ Troubleshooting Matrix

| Problem | Potential Root Cause | Recommended Command Line Resolution |
|:---|:---|:---|
| **`Cannot connect to AI server`** | Ollama is stopped or the model is not pulled. | Run `ollama serve` in a shell, and check downloaded models via `ollama list`. |
| **`Milvus connection failed`** | Containers are not running or Docker is out of RAM. | Increase Docker memory to 8 GB+ in Settings, then run `docker-compose restart` in `docker/`. |
| **`Port already in use`** | A local process is blocking ports `8080`, `8000`, or `8001`. | Identify the process using `lsof -i :8080` (Mac/Linux) or `netstat -ano \| findstr 8080` (Windows) and terminate it. |
| **`PyPDF2 / Docx ImportErrors`** | Python dependencies are missing in the local environment. | Re-run pip installer: `pip install -r api/requirements.txt`. |

---

## 📝 License

This project is licensed under the permissive **MIT License** - see the [LICENSE](./LICENSE) file for details.
