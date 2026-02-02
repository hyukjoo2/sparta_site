# docs/DEV_NOTES.md
# Development Notes (Quick Start)

## Frontend
- Entry: `public/index.html`
- Main boot: `public/src/main.js`

If using a static server:
- Serve `public/` as web root.

## Node server
- Location: `server/`
- Entry: `server/src/app.js`

Typical commands (adjust to your package.json):
- `npm install`
- `npm run dev` (or `node server/src/app.js`)

## Python FastAPI
- Entry: `app.py`
- Virtual env: `rag-env/`

Typical commands:
- activate venv
- run fastapi (uvicorn)
  - `uvicorn app:app --reload --port <port>`

## Data/RAG
- `ingest_pdf.py`: PDF ingestion
- `ingest_rag.py`: build vector index (if used)

## Where to change what
- AI routing/UI tool behavior:
  - `public/src/app/aiPopup.js`
  - (recommended) extract a router to `public/src/app/intentRouter.js`

- History modal:
  - `public/src/app/historyTool.js`
  - `public/src/features/history.js`

- Neo:
  - backend: `server/src/neo/*`
  - frontend: `public/src/app/neoClient.js`