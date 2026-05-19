# ⚙️ Installation & Deployment Setup Guide

This guide provides a comprehensive, step-by-step walkthrough to install, configure, and verify the **LLM_AI** local RAG-Enabled platform. Follow these instructions to get your secure, offline system running on Windows, macOS, or Linux.

---

## 📋 System Prerequisites

Before starting, ensure your machine meets these requirements:

| Requirement | Minimum Specification | Recommended Specification |
|:---|:---|:---|
| **OS** | Windows 10/11 (with WSL2), macOS 12+, or Ubuntu 20.04+ | Windows 11 / Ubuntu 22.04 LTS |
| **CPU** | 4-Core Intel/AMD or Apple Silicon (M1/M2/M3) | 8-Core CPU or Apple Silicon Pro/Max/Ultra |
| **RAM** | 8 GB | 16 GB or 32 GB (highly recommended for deep learning) |
| **GPU** | Optional (CPU execution supported) | NVIDIA GPU (8+ GB VRAM) for accelerated inference |
| **Disk Space** | 20 GB free space (SSD preferred) | 50 GB free space |
| **Software** | Docker Desktop & Python 3.11+ | Docker Desktop, Python 3.11+, Git |

---

## 🚀 Step-by-Step Installation

### Step 1: Clone the Repository
Clone the codebase to your local system:
```bash
git clone https://github.com/yourusername/LLM_AI.git
cd LLM_AI
```

### Step 2: Configure Environment Variables
Copy the secure environment template to `.env` and adjust the variables for your setup:
```bash
# Copy template
cp .env.example .env
```
Open the `.env` file in your text editor and modify the passwords and secret keys (especially `WEBUI_SECRET_KEY` and `MINIO` credentials) to secure your production database.

---

### Step 3: Install & Start Ollama
Ollama handles the execution of high-performance local language and embedding models.

1. **Download Ollama**:
   - **Windows**: Download and run the setup from [Ollama's Windows Page](https://ollama.com/download/windows).
   - **macOS**: Download from [Ollama's macOS Page](https://ollama.com/download/mac).
   - **Linux**: Install via terminal command:
     ```bash
     curl -fsSL https://ollama.com/install.sh | sh
     ```

2. **Verify Ollama is Running**:
   Open a terminal and test the service:
   ```bash
   ollama --version
   ```

---

### Step 4: Download LLM and Embedding Models
Pull the lightweight coding model and the standard text embedding model:
```bash
# Pull Qwen Coder model (7B parameters, optimized instruct)
ollama pull qwen2.5-coder:7b-instruct-q5_K_M

# Pull Nomic Embed Text model (768-dimension vectors)
ollama pull nomic-embed-text
```

Verify models are active:
```bash
ollama list
```

---

### Step 5: Configure Docker Resources (Crucial for Windows/macOS)
Since Milvus and the Web UI run in Docker, you must allocate sufficient resources to the Docker virtual machine:
1. Open **Docker Desktop Settings**.
2. Navigate to **Resources** ➡️ **Advanced**.
3. Allocate at least **4 CPUs** and **8 GB of RAM** (recommend **12 GB+** if executing large RAG indexing jobs).
4. Apply and restart Docker Desktop.

---

### Step 6: Launch Docker Containers
Orchestrate the 5 Docker services using Docker Compose:
```bash
# Navigate to the docker orchestration folder
cd docker

# Start all containers in detached mode
docker-compose up -d
```
Wait 2 to 3 minutes for Milvus, MinIO, Attu, OpenWebUI, and the RAG API to fully initialize.

---

### Step 7: Verify Service Health
Ensure all components are running correctly by checking their status:
```bash
# Show container status
docker-compose ps
```

You can also test the custom RAG API health check endpoint directly:
```bash
curl http://localhost:8001/health
```
*Expected JSON Output:*
```json
{
  "status": "healthy",
  "milvus": "connected",
  "ollama": "connected",
  "collection": "company_documents"
}
```

---

### Step 8: Open Web and Management Interfaces
Access the graphical dashboards through your browser:

* **OpenWebUI (ChatGPT-like Chat)**: [http://localhost:8080](http://localhost:8080)
  * *Note: The first registered user automatically becomes the Admin.*
* **Attu (Milvus Vector DB GUI)**: [http://localhost:8000](http://localhost:8000)
  * *Connect by typing `milvus-standalone:19530` into the Host address field.*
* **Swagger API Docs (RAG API)**: [http://localhost:8001/docs](http://localhost:8001/docs)

---

## 🌐 Local Network Sharing (Team Deployment)

To share the AI system and document database with other devices on your local network (LAN):

1. **Find your Server IP Address**:
   * **Windows (PowerShell)**: `ipconfig` (Look for IPv4 Address, e.g. `192.168.1.100`)
   * **macOS/Linux (Terminal)**: `ifconfig` or `ip a`

2. **Connect from Client Machines**:
   * Open the Web UI via: `http://192.168.1.100:8080`
   * Run CLI commands pointing to the server:
     ```bash
     python ai.py --server 192.168.1.100 "What is our company workflow?"
     ```

3. **Firewall Rules**:
   Ensure ports `8080` (Web UI), `8001` (RAG API), and `11434` (Ollama) are allowed through the host firewall.

---

## 🛠️ Verification & Testing

Verify CLI features by executing connection tests and queries:

```bash
# Test connection from root directory
python ai.py --test

# Run pure local inference
python ai.py "Write a python function to compute prime numbers"

# Launch the interactive console
python ai.py interactive
```

If RAG is active and you have uploaded files, run a contextual search:
```bash
python ai.py --rag "Summarize the system specs listed in our SETUP.md"
```
