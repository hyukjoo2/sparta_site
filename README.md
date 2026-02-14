# Sparta Site

Brief Introduction

This repository contains a sample project integrating a static SPA frontend, an Express + MySQL backend, and a RAG/Neo engine with LLM integrations. For a detailed overview, see [PROJECT_OVERVIEW.md](PROJECT_OVERVIEW.md).

Project Background

This project is not a simple asset tracker; it aims to be a gamified asset growth and management system. The 'Architect' component interprets market volatility as data and designs automated decision-making frameworks based on that data. We pursue a trading architecture that operates consistently and without emotion according to predefined 'Gate' rules. Users define and validate strategies and rules, then monitor results to grow assets systematically. The approach emphasizes reproducibility, discipline, and risk management.


Installation and Running

Requirements:

- Node.js 18+ (recommended)
- npm or pnpm

Install and run (root optional + server)

```bash
# Install root dependencies (if there are global project scripts)
npm install

# Install server dependencies
cd server
npm install

# Run server for development
node src/app.js
# or if a start script exists in package.json
npm start
```

Frontend (static files)

```bash
# Static files are under /public. To quickly view locally:
open public/index.html

# Or serve with a simple static server:
cd public
npx http-server -p 8080
# Open http://localhost:8080 in your browser
```

RAG-related (optional)

```bash
# A Python environment for RAG is included (rag-env).
# To activate the virtualenv (macOS / bash / zsh):
source rag-env/bin/activate
# Install required packages (if a requirements.txt exists)
pip install -r requirements.txt
```

If you run into issues, check `server/src/app.js`, server console logs, and the browser devtools Network tab.

Key paths & responsibilities

- Frontend: `public/src` — UI; `aiPopup.js` manages LLM interactions
- Backend: `server/src` — Express routes and MySQL integration (`/api/history`, `/api/chat_log`, etc.)
- RAG/Neo: `server/src/neo/*` and `ingest_rag.py` — document indexing and retrieval support

Project structure (summary)

```
sparta-site/
├─ public/         # static SPA, assets, src
├─ server/         # Express app and Neo engine
├─ rag-env/        # Python venv (RAG tools)
├─ tables.sql      # DB schema
├─ PROJECT_OVERVIEW.md
└─ README.md
```

LLM Relationship

```mermaid
flowchart LR
	U[User] --> F[Frontend (public/src/app/aiPopup.js)]
	F -->|API call /api/*| S[Server API (server/src/app.js)]
	S -->|reads/writes| DB[(MySQL: history, chat_log, rag_doc, rag_chunk)]
	S --> Neo[Neo Engine (server/src/neo)]
	F -->|direct or proxied| LLM[LLM Provider (OpenAI / HuggingFace / ExaOne)]
	Neo -->|SSE / queries| F
	S -->|calls| LLM
	ingest[ingest_rag.py] -->|writes| RAG[r ag_doc / rag_chunk]
```

Short explanation:

- `aiPopup.js` reads user input, determines intents (e.g., OPEN_HISTORY_MODAL), and calls server APIs or LLMs as needed.
- The server connects DB with the Neo engine for search/generation tasks; external LLMs are used for response generation or RAG augmentation.

See [PROJECT_OVERVIEW.md](PROJECT_OVERVIEW.md) for full directory tree and DB schema details.

Note: GitHub's markdown renderer may not display Mermaid diagrams inline in all contexts. If it does not render, paste the diagram above into https://mermaid.live to preview, or generate an SVG locally with `@mermaid-js/mermaid-cli` and embed that image instead.

MySQL installation and running (macOS / Docker)

This project uses MySQL. Below are instructions for installing MySQL on macOS (Homebrew) and an alternative Docker option, plus examples for creating the database and applying `tables.sql`.

1) macOS (Homebrew)

```bash
# Install MySQL with Homebrew
brew update
brew install mysql

# Start the service
brew services start mysql

# You may be able to access MySQL without an initial root password.
# For security, run:
mysql_secure_installation

# Example: create database, user, and grant privileges
mysql -u root -p
CREATE DATABASE sparta CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'sparta_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON sparta.* TO 'sparta_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;

# Apply schema
mysql -u sparta_user -p sparta < tables.sql
```

2) Docker (alternative)

```bash
# Run a simple MySQL container (map port 3306)
docker run --name sparta-mysql -e MYSQL_ROOT_PASSWORD=rootpw -e MYSQL_DATABASE=sparta -e MYSQL_USER=sparta_user -e MYSQL_PASSWORD=secure_password -p 3306:3306 -d mysql:8.0

# Copy the schema into the container and import it (or import from host client)
docker cp tables.sql sparta-mysql:/tmp/tables.sql
docker exec -it sparta-mysql bash -c "mysql -u sparta_user -psecure_password sparta < /tmp/tables.sql"
```

Example environment variables (`.env` or server config):

```
DB_HOST=localhost
DB_PORT=3306
DB_USER=sparta_user
DB_PASS=secure_password
DB_NAME=sparta
```

Troubleshooting tips

- If the server reports DB connection errors, check that `server/src/db.js` matches the environment variables above.
- Test MySQL connectivity locally with: `mysql -u sparta_user -p -h localhost -P 3306 sparta`.
- If `tables.sql` does not include `USE sparta;`, specify the DB on import: `mysql -u user -p dbname < tables.sql`.

Python development server (FastAPI)

If there is an `app.py` at the project root exposing a FastAPI instance named `app`, you can run a local development server with a virtual environment and `uvicorn` as shown below.

```bash
# Create virtual environment
python3 -m venv .venv

# Activate (macOS / Linux)
source .venv/bin/activate

# Install required packages
pip install fastapi uvicorn httpx

# Run the FastAPI app (app.py must define `app = FastAPI()`)
uvicorn app:app --reload --port 8000
```

Notes:

- `python3 -m venv .venv` creates an isolated Python virtual environment in the current directory.
- `source .venv/bin/activate` activates the venv so `python`/`pip` use that environment.
- `pip install fastapi uvicorn httpx` installs FastAPI, the ASGI server, and an HTTP client.
- `uvicorn app:app --reload --port 8000` runs the `app` object from `app.py` with auto-reload for development.

Warning (production): do not use `--reload` in production. Deploy with a process manager or container.

Example test request:

```bash
curl http://localhost:8000/
```

ExaOne LLM — Installation & Quickstart

This project can integrate with an LLM provider called ExaOne. Below are generic setup steps — adapt them to the exact SDK or API details from ExaOne's documentation.

1) Obtain credentials

- Sign in to your ExaOne dashboard and create an API key. Note the API endpoint and the key.

2) Environment variables

Add the following to your `.env` (or export in your shell):

```
EXAONE_API_KEY=your_exaone_api_key_here
EXAONE_API_URL=https://api.exaone.example.com
```

3) Using ExaOne via HTTP (generic example)

```bash
# Quick test with curl (replace URL and KEY)
curl -X POST "$EXAONE_API_URL/v1/generate" \
	-H "Authorization: Bearer $EXAONE_API_KEY" \
	-H "Content-Type: application/json" \
	-d '{"prompt":"Hello, ExaOne!","max_tokens":128}'
```

4) Python SDK (if ExaOne provides one)

```bash
# install (package name may vary; follow ExaOne docs)
pip install exaone
```

Example usage (illustrative):

```python
import os
from exaone import Client  # adjust per SDK

client = Client(api_key=os.environ['EXAONE_API_KEY'], base_url=os.environ.get('EXAONE_API_URL'))
resp = client.generate(prompt='Hello, ExaOne!', max_tokens=128)
print(resp)
```

5) Node / JavaScript (HTTP example)

```javascript
// using fetch or node-fetch
const res = await fetch(process.env.EXAONE_API_URL + '/v1/generate', {
	method: 'POST',
	headers: {
		'Authorization': `Bearer ${process.env.EXAONE_API_KEY}`,
		'Content-Type': 'application/json'
	},
	body: JSON.stringify({ prompt: 'Hello, ExaOne!', max_tokens: 128 })
});
const data = await res.json();
console.log(data);
```

Notes

- Replace `EXAONE_API_URL` with the actual endpoint from ExaOne.
- If ExaOne provides an official SDK, prefer using it for authentication, retries, and batching.
- Store API keys securely and do not commit them to source control.
