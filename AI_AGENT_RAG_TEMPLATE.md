# 🤖 [Project Name] - Autonomous Software Development Agent & RAG System

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg?style=flat-square)](https://www.python.org/downloads/)
[![MCP Protocol](https://img.shields.io/badge/MCP-Supported-black.svg?style=flat-square)](https://modelcontextprotocol.io/)
[![pgvector](https://img.shields.io/badge/Vector%20DB-pgvector-336791.svg?style=flat-square)](https://github.com/pgvector/pgvector)
[![FastAPI](https://img.shields.io/badge/API-FastAPI-009688.svg?style=flat-square)](https://fastapi.tiangolo.com/)

A production-grade, extensible framework for building **autonomous software engineering agents** powered by **Model Context Protocol (MCP)** tool execution and **Hybrid Retrieval-Augmented Generation (RAG)** over large codebases.

---

## 📌 Architecture Overview

```mermaid
graph TD
    User([Developer / CI Trigger]) -->|Task Instruction| Orchestrator[Agent Orchestrator / Planner]
    
    subgraph "Knowledge & Context (RAG)"
        Codebase[(Codebase Repository)] --> ASTParser[AST & Tree-Sitter Parser]
        ASTParser --> Chunker[Semantic Code Chunker]
        Chunker --> Embeddings[Dense Embedding Model]
        Embeddings --> VectorStore[(pgvector / ChromaDB)]
        VectorStore --> HybridRetriever[Hybrid Dense + BM25 Retriever]
        HybridRetriever --> Reranker[Cross-Encoder Reranker]
        Reranker --> Orchestrator
    end

    subgraph "Execution Layer (MCP Tools)"
        Orchestrator --> MCPClient[MCP Client]
        MCPClient --> MCPServerFilesystem[MCP Filesystem Tool]
        MCPClient --> MCPServerTerminal[MCP Bash / Command Tool]
        MCPClient --> MCPServerGit[MCP Git / PR Tool]
    end

    subgraph "Self-Healing Evaluation Loop"
        MCPServerTerminal --> TestRunner[Test & Linter Runner]
        TestRunner -->|Pass / Fail Diagnostic| Evaluator[Agent Evaluator]
        Evaluator -->|Error Trace| Orchestrator
        Evaluator -->|Verified Code| Success([Commit & PR Created])
    end
```

---

## 🧠 Core Features

- **Hierarchical Multi-Agent Architecture**:
  - `Planner Agent`: Breaks complex engineering goals into actionable dependency subtasks.
  - `Coder Agent`: Writes syntactic, typed, context-aware code guided by codebase index.
  - `Critic / Reviewer Agent`: Performs automated security, style, and regression audits.
  - `Executor Agent`: Runs test suites, parses stack traces, and provides self-healing feedback.
- **Model Context Protocol (MCP) First**:
  - Decoupled tool integration supporting filesystem, git, database, and terminal servers.
  - Compatible with any MCP-compliant client or IDE.
- **Hybrid Codebase RAG**:
  - AST-aware chunking preserving classes, functions, and docstrings.
  - Hybrid dense vector search + sparse BM25 keyword matching with cross-encoder reranking.
- **Multi-Tenant / Multi-Repo Isolation**:
  - Isolated vector namespaces and workspace sandboxes per tenant or repository.

---

## 🚀 Quickstart

### Prerequisites
- Python 3.11+
- Docker & Docker Compose
- API Keys for OpenAI, Anthropic, or local LLM (Ollama / vLLM)

### 1. Clone & Configure Environment
```bash
git clone git@github.com:mrpal39/<repo-name>.git
cd <repo-name>
cp .env.example .env
```

Configure `.env`:
```env
# LLM Providers
OPENAI_API_KEY=your-openai-key
ANTHROPIC_API_KEY=your-anthropic-key

# Vector DB & Cache
DATABASE_URL=postgresql://agent:agentpass@localhost:5432/agent_rag
REDIS_URL=redis://localhost:6379/0

# Agent Execution Mode (SANDBOXED / LOCAL)
AGENT_EXECUTION_MODE=SANDBOXED
```

### 2. Start Supporting Services (Vector DB + Redis)
```bash
docker-compose up -d postgres-vector redis
```

### 3. Ingest Codebase into Vector Store
```bash
python -m agent.rag.ingest --repo-path ./target-repo --chunk-mode ast
```

### 4. Run the Agent
```bash
# Interactive CLI mode
python -m agent.cli --goal "Implement JWT authentication with redis jti blacklist"

# MCP Server mode
python -m agent.mcp_server --port 8080
```

---

## 📂 Project Structure

```text
├── agent/
│   ├── orchestrator/      # Task graph & planning engine
│   ├── actors/            # Planner, Coder, Reviewer, Tester agents
│   ├── mcp/               # Model Context Protocol client & server handlers
│   ├── rag/               # AST parsing, embedding, hybrid search, and reranker
│   └── sandbox/           # Dockerized execution sandbox for code execution
├── config/                # Prompts, model weights, and tool manifests
├── tests/                 # Unit, integration, and agent trajectory evaluations
├── docker-compose.yml     # pgvector, Redis, and execution containers
└── pyproject.toml         # Dependencies and packaging
```

---

## 🛡️ Security & Sandboxing

All terminal execution, package installations, and git commands invoked by the agent run inside an isolated Docker sandbox with non-root privileges, memory limits, and egress network rules.

---

## 📄 License
Distributed under the MIT License. See [LICENSE](LICENSE) for details.

---

## 👨‍💻 Author
**Rahul Pal**  
- Portfolio: [dev-code.in](https://www.dev-code.in)  
- GitHub: [@mrpal39](https://github.com/mrpal39)  
- LinkedIn: [in/mrpal39](https://www.linkedin.com/in/mrpal39/)
