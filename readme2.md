# 🚀 [Project Name] - Scalable Multi-Tenant Backend System

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen?style=flat-square)](#)
[![Python Version](https://img.shields.io/badge/python-3.11%2B-blue?style=flat-square)](#)
[![Framework](https://img.shields.io/badge/framework-FastAPI%20%7C%20Django-009688?style=flat-square)](#)
[![Docker](https://img.shields.io/badge/docker-ready-2496ED?style=flat-square)](#)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](#)

A production-oriented backend architecture engineered for **tenant isolation, role-governed access, and resilient high-concurrency workflows**.

---

## 📌 Architectural Overview

```mermaid
graph TD
    Client[Web / Mobile Client] -->|HTTPS / WSS| Nginx[Nginx Reverse Proxy]
    Nginx --> App[API Application Layer]
    App -->|Auth & Revocation Check| Redis[(Redis Cache & Session)]
    App -->|Data Persistence| DB[(PostgreSQL Multi-Tenant DB)]
    App -->|Async Tasks| Queue[Celery / Message Broker]
    Queue --> Worker[Background Workers]
```

---

## 🧠 Core Features

- **Multi-Tenant Isolation**: Schema-based and row-level separation ensuring zero tenant data leakage.
- **Dynamic Role-Based Access Control (RBAC)**: Fine-grained permissions across Super-Admin, Admin, Manager, and Employee roles.
- **Secure Authentication Lifecycle**: JWT access + refresh tokens with redis-backed `jti` revocation blacklist.
- **Resilient Background Processing**: Asynchronous workers and message queues for heavy workloads (emails, reporting, exports).
- **Comprehensive API Documentation**: Auto-generated interactive OpenAPI/Swagger schemas.

---

## 🛠️ Tech Stack

- **Backend Core**: Python 3.11+ (FastAPI / Django REST Framework)
- **Primary Database**: PostgreSQL 15+ (with connection pooling via PgBouncer)
- **Caching & Real-Time**: Redis 7+ (Pub/Sub, session store, `jti` blacklist)
- **Task Queue**: Celery / RabbitMQ
- **Containerization**: Docker & Docker Compose
- **Reverse Proxy**: Nginx with SSL termination

---

## 🚀 Getting Started

### Prerequisites
- Docker Engine & Docker Compose
- Python 3.11+
- Git

### 1. Clone & Setup
```bash
git clone git@github.com:mrpal39/<repo-name>.git
cd <repo-name>
cp .env.example .env
```

### 2. Environment Configuration
Update `.env` with your environment credentials:

| Variable | Description | Example / Default |
|---|---|---|
| `DEBUG` | Enables detailed error traces | `False` (in production) |
| `SECRET_KEY` | Application cryptographic secret | `super-secret-hex-token` |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@localhost:5432/dbname` |
| `REDIS_URL` | Redis cache and session URL | `redis://localhost:6379/0` |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Access token time-to-live | `15` |
| `REFRESH_TOKEN_EXPIRE_DAYS` | Refresh token time-to-live | `7` |
| `ALLOWED_HOSTS` | CORS and Host header domains | `localhost,127.0.0.1,api.domain.com` |

### 3. Run with Docker Compose
```bash
# Build and run containers in detached mode
docker-compose up -d --build

# View container logs
docker-compose logs -f api
```
The API server will be available at `http://localhost:8000`.  
Interactive documentation: `http://localhost:8000/docs`.

---

## 🔐 Authentication & RBAC Flow

1. **User Login**: Submits credentials to `POST /api/v1/auth/login`.
2. **Token Issuance**: Returns signed JWT access token (15m TTL) and secure HTTP-only refresh token (7d TTL).
3. **Role Enforcement**: Every request validates tenant claims against the role permissions matrix.
4. **Token Revocation**: On logout or permission change, token `jti` is cached in Redis until expiry.

---

## 🧪 Testing & Code Quality

```bash
# Run pytest with coverage report
pytest --cov=app tests/

# Run static type checking
mypy app/

# Run linter and formatting checks
black --check .
flake8 .
```

---

## 🛡️ Security Best Practices
- Non-root user in Docker containers.
- Strict rate limiting via Redis token bucket.
- SQL injection prevention via parameterized ORM queries.
- Helmet-style security headers configured in Nginx.

---

## 📄 License
Distributed under the MIT License. See `LICENSE` for details.

---

## 👨‍💻 Author
**Rahul Pal**  
- Website: [dev-code.in](https://www.dev-code.in)  
- GitHub: [@mrpal39](https://github.com/mrpal39)  
- LinkedIn: [in/mrpal39](https://www.linkedin.com/in/mrpal39/)
