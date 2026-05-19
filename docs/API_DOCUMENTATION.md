# 🔌 RAG REST API Documentation

The **LLM_AI RAG Service** exposes a fast, modern REST API built with FastAPI. It handles secure document ingestion, tokenization/chunking, embedding generation using local models, and vector indexing inside MilvusDB.

---

## 🔐 Authentication & Authorization

All API endpoints are protected using API key headers. 

### Request Header
```http
X-API-Key: cai_your_generated_secure_api_key
```

### Roles and Permissions
The RAG service supports Role-Based Access Control (RBAC):

| Role | Permissions | Rate Limit (requests/hour) |
|:---|:---|:---|
| **admin** | `read`, `write`, `delete`, `manage_users` | Unlimited |
| **developer** | `read`, `write` | 1000 |
| **viewer** | `read` | 100 |

### Generating API Keys
API keys are stored in `data/api_keys.json`. To generate a new developer key programmatically:
```python
from pathlib import Path
from api.auth import key_manager

# Generate a developer key for a user
new_key = key_manager.generate_key(user_id="dev_user_01", role="developer")
print(f"Generated Key: {new_key}")
```

---

## 🚀 Endpoint Reference

### 1. Root / Status Info
Check basic metadata and running service states.

* **URL**: `/`
* **Method**: `GET`
* **Required Permission**: None

#### cURL Request:
```bash
curl -X GET http://localhost:8001/
```

#### JSON Response (200 OK):
```json
{
  "service": "Company AI RAG API",
  "status": "running",
  "version": "1.0.0",
  "endpoints": {
    "upload": "/upload - Upload documents",
    "search": "/search - Search for relevant context",
    "documents": "/documents - List all documents",
    "delete": "/delete/{filename} - Delete a document"
  }
}
```

---

### 2. Upload Document
Uploads a text, Markdown, Python, PDF, or DOCX file, automatically chunks the text, computes speaker-style embeddings, and indices them.

* **URL**: `/upload`
* **Method**: `POST`
* **Content-Type**: `multipart/form-data`
* **Required Permission**: `write`

#### Request Parameters (Form Data):
* `file`: Binary file upload (e.g., `policy.pdf`)
* `doc_type`: Category identifier, string (default: `"general"`)
* `uploaded_by`: User ID string, optional

#### Python Integration Example:
```python
import requests

url = "http://localhost:8001/upload"
headers = {"X-API-Key": "cai_your_api_key"}
files = {"file": ("guide.txt", open("guide.txt", "rb"))}
data = {"doc_type": "technical", "uploaded_by": "john_dev"}

response = requests.post(url, headers=headers, files=files, data=data)
print(response.json())
```

#### JSON Response (200 OK):
```json
{
  "status": "uploaded",
  "filename": "guide.txt",
  "message": "Document is being processed in background"
}
```

---

### 3. Search Vector Database
Queries the vector database using cosine similarity search to retrieve matching context chunks.

* **URL**: `/search`
* **Method**: `POST`
* **Content-Type**: `application/json`
* **Required Permission**: `read`

#### Request Body Schema:
```json
{
  "query": "What are our coding standards?",
  "top_k": 3
}
```

#### JavaScript Integration Example:
```javascript
const response = await fetch("http://localhost:8001/search", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "X-API-Key": "cai_your_api_key"
  },
  body: JSON.stringify({
    query: "coding standards",
    top_k: 2
  })
});
const data = await response.json();
console.log(data);
```

#### JSON Response (200 OK):
```json
[
  {
    "chunk_id": "a1b2c3d4_0",
    "text": "All python source files must strictly follow PEP 8 standards. Use black for formatting and flake8 for lint checks before committing code.",
    "metadata": {
      "filename": "coding_standards.txt",
      "doc_type": "technical",
      "chunk_index": 0
    },
    "score": 0.89421
  },
  {
    "chunk_id": "a1b2c3d4_1",
    "text": "For frontend code, configure Prettier and ESLint. Use TypeScript for new components to guarantee typing safety.",
    "metadata": {
      "filename": "coding_standards.txt",
      "doc_type": "technical",
      "chunk_index": 1
    },
    "score": 0.76211
  }
]
```

---

### 4. List Documents
Returns a comprehensive list of all unique documents currently indexed in the vector store.

* **URL**: `/documents`
* **Method**: `GET`
* **Required Permission**: `read`

#### cURL Request:
```bash
curl -X GET http://localhost:8001/documents \
  -H "X-API-Key: cai_your_api_key"
```

#### JSON Response (200 OK):
```json
{
  "documents": [
    {
      "filename": "coding_standards.txt",
      "doc_type": "technical"
    },
    {
      "filename": "annual_report.pdf",
      "doc_type": "financial"
    }
  ],
  "count": 2
}
```

---

### 5. Delete Document
Deletes all vector chunks and files associated with a specific filename.

* **URL**: `/delete/{filename}`
* **Method**: `DELETE`
* **Required Permission**: `delete`

#### cURL Request:
```bash
curl -X DELETE http://localhost:8001/delete/annual_report.pdf \
  -H "X-API-Key: cai_your_api_key"
```

#### JSON Response (200 OK):
```json
{
  "status": "deleted",
  "filename": "annual_report.pdf"
}
```

---

### 6. Service Health Check
Direct check verifying the connection status of the active FastAPI app, the Ollama embedding container, and the Milvus standalone vector database.

* **URL**: `/health`
* **Method**: `GET`
* **Required Permission**: None

#### JSON Response (200 OK):
```json
{
  "status": "healthy",
  "milvus": "connected",
  "ollama": "connected",
  "collection": "company_documents"
}
```

---

## ⚠️ Error Codes & Troubleshooting

| HTTP Status | Error Message / Code | Root Cause | Resolution |
|:---|:---|:---|:---|
| **401 Unauthorized** | `API key required` | Missing `X-API-Key` header. | Add the authentication header to your HTTP request. |
| **401 Unauthorized** | `Invalid or expired API key` | Provided key is invalid or has expired. | Verify the token in `api_keys.json` or generate a new key. |
| **403 Forbidden** | `Permission denied` | The user's role lacks access to this endpoint. | Upgrade the API key to a developer or admin role. |
| **429 Too Many Requests** | `Rate limit exceeded` | Hourly request limit has been reached. | Wait for the next hourly window or increase limits in `ROLES`. |
| **500 Internal Error** | `Embedding generation failed` | FastAPI cannot communicate with the Ollama model server. | Ensure the Ollama container or local service is running and active. |
| **500 Internal Error** | `Search failed` | Connections to the MilvusDB server timed out or failed. | Verify the `milvus-standalone` Docker service is up and running. |
