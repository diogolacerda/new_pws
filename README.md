# ProtoWorks System (PWS)

Sistema de gerenciamento de protocolos de comissionamento industrial.

## Estrutura do Projeto

```
new_pws/
├── backend/          # FastAPI + SQLAlchemy + PostgreSQL
├── frontend/         # React + TypeScript + Vite
├── docs/             # Documentação e tasks
│   ├── TASKS_DATABASE.md
│   ├── TASKS_BACKEND.md
│   ├── TASKS_FRONTEND.md
│   └── WORKFLOW_EXECUTION.md
└── docker-compose.yml
```

## Branches

| Branch | Propósito |
|--------|-----------|
| `master` | Produção |
| `stage` | Homologação/QA |
| `feature/*` | Desenvolvimento de tarefas |

## Setup Local

### Pré-requisitos

- Python 3.11+
- Node.js 20+
- PostgreSQL 15+
- Docker (opcional)

### Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
alembic upgrade head
uvicorn app.main:app --reload
```

### Frontend

```bash
cd frontend
npm install
cp .env.example .env
npm run dev
```

### Docker

```bash
docker-compose up -d
```

## Documentação

- [PRD - Product Requirements Document](PRD_ProtoWorks_System.md)
- [Tasks Database](docs/TASKS_DATABASE.md)
- [Tasks Backend](docs/TASKS_BACKEND.md)
- [Tasks Frontend](docs/TASKS_FRONTEND.md)
- [Workflow de Execução](docs/WORKFLOW_EXECUTION.md)

## Tech Stack

**Backend:**
- FastAPI
- SQLAlchemy 2.0 (async)
- Alembic
- PostgreSQL
- Pydantic v2

**Frontend:**
- React 18
- TypeScript
- Vite
- TanStack Query
- Zustand
- React Hook Form + Zod
- Tailwind CSS

## Licença

Proprietary - All rights reserved
